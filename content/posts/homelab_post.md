---
title: "Building a Threat Intelligence Research Platform: Architecture and Design Decisions"
date: 2026-06-13
draft: false
tags: ["homelab", "threat-intelligence", "clickhouse", "infrastructure", "detection"]
description: "How and why I designed a multi-tier security research environment
for threat intelligence operations, detection engineering, and breach data analysis."
---

When I left Cisco earlier this year after four years doing product security
research, I had a clear picture of what I wanted to build next: a proper
infrastructure platform for the threat intelligence and investigation work
I had been running in parallel at Southwest Valley Research Group. Not
a collection of scripts running on a spare laptop, but a designed system
with real network segmentation, a defensible data architecture, and the
operational capacity to support serious analytical work.

This post documents the architecture decisions behind that build and the
reasoning behind each one. I am writing it primarily because I found very
few resources that approached homelab design from an intelligence
operations perspective rather than a media server or self-hosting
perspective. The requirements are different.

---

## The Problem I Was Solving

My existing setup had grown organically over several years and it showed.
Breach data analysis, Telegram collection, case investigation tooling,
and general security research were all running on the same machine with
no isolation between them. A mistake in one operational context could
affect another. Ingest a malformed archive and potentially affect active
case data. Run a noisy scan and contaminate telemetry. There was no
network visibility, no centralized logging, and no detection capability
pointed inward.

The requirements I set for the new platform were:

- **Network segmentation** isolating operational security contexts from
  each other and from general internet traffic
- **A production-grade data platform** for the breach intelligence
  corpus, capable of handling hundreds of datasets and supporting fast
  analytical queries
- **Detection infrastructure** — not just defensive controls but actual
  network visibility with behavioral telemetry
- **A proper identity and access management layer** replacing ad-hoc
  SSH key management
- **Sufficient compute and memory** to run local large language model
  inference without sending sensitive operational data to cloud APIs

That last requirement shaped the hardware decision significantly.

---

## Hardware

The primary host is a Cisco UCS C240 M5, a dual-socket 1U rack server
with dual Intel Xeon Gold 6140 processors (18 cores each, 36 cores per
socket, 72 cores total) and 503 GiB of RAM. This is enterprise
datacenter hardware, not consumer homelab equipment. It runs loud,
draws significant power, and requires a rack or dedicated space. For
most homelab use cases it is massive overkill.

For my use case it is correctly sized. Running a local LLM at any useful
context length requires substantial RAM. The breach data analytical
workload benefits from memory-mapped columnar storage. Running multiple
isolated VM environments simultaneously without resource contention
requires headroom. A 503 GiB machine gives me that headroom without
compromise.

The hypervisor is Proxmox VE, chosen for zero licensing cost and
strong community documentation. The VMware alternative has become
significantly less attractive since the Broadcom acquisition changed
the licensing model. Proxmox handles the workload without issue.

---

## Network Architecture

The network runs on a UniFi stack I was already using for the home
network. I defined seven VLANs, each isolating a distinct security
context:

- **Management** — hypervisor management interfaces, network equipment
  administration. No internet access. Reachable only from a dedicated
  management workstation.
- **Operations** — active investigation tooling, Telegram collection,
  case work. Internet access through a dedicated egress path.
- **Data Platform** — ClickHouse, ingestion pipelines, analytical
  infrastructure. No direct internet access; data arrives via the
  ingestion VLAN.
- **Ingestion** — the boundary zone where raw collected data enters the
  platform. Isolated from both operations and data platform; data flows
  in one direction.
- **Detection** — network sensor infrastructure. Receives a SPAN copy
  of all inter-VLAN traffic. No internet access. Write-only to the
  data platform.
- **Services** — internal services: authentication, reverse proxy, DNS.
- **Lab/Research** — isolated environment for working with potentially
  hostile samples, running offensive tooling, and general security
  research that should not touch production.

Inter-VLAN traffic is controlled by firewall policy at the UniFi
gateway. The default policy is deny; explicit rules permit only the
specific flows the architecture requires. The detection VLAN receives
a SPAN mirror of the trunk uplink, giving the sensor VM visibility
into all inter-VLAN traffic without participating in any of it.

The segmentation philosophy is: assume compromise of any single VLAN
and evaluate whether that compromise can propagate. Operations
compromised should not affect case data. Lab compromised should not
affect operations. Ingestion compromised should not allow data to flow
backward into operations. Each boundary is enforced at the network
layer, not just by convention.

---

## Data Platform: Why ClickHouse

The breach intelligence corpus has grown to several hundred datasets
spanning traditional breach dumps, stealer logs, and ransomware leak
data. The analytical questions I ask of it are aggregation-heavy:
identity correlation across sources, infrastructure attribution,
password reuse pattern analysis, actor profiling across datasets.
These are OLAP workloads, not OLTP.

I ran Elasticsearch for the first two years. It worked, but the
resource profile was wrong for this use case. Elasticsearch is
designed for log indexing and full-text search over document stores.
Using it for columnar analytical queries over structured breach data
meant paying a significant RAM tax for capabilities I was not using
while getting suboptimal performance on the queries I was actually
running.

ClickHouse addressed this directly. The reasons I migrated:

**Columnar storage** means analytical queries over specific fields
across hundreds of millions of rows are fast because only the relevant
columns are read from disk. A query asking "find all email addresses
from this domain across all datasets" does not need to touch every
field in every row.

**Native zstd compression** at the column level. Breach data has highly
repetitive structure — the same email domains, the same password
patterns, the same field names. Column-level compression of repetitive
data achieves significant storage reduction over row-oriented storage
with general-purpose compression.

**Resource footprint.** At equivalent data volume, ClickHouse requires
substantially less RAM than Elasticsearch for the queries I run. The
difference is material when you are also trying to run LLM inference
on the same hardware.

**Text indexes.** Recent ClickHouse versions added full-text search
indexes that cover the primary use case where Elasticsearch previously
had an advantage. There is no longer a meaningful reason to choose
Elasticsearch for this workload.

The migration was not trivial — the data models are different and I
rewrote the ingestion pipeline — but it was worth doing.

---

## Detection Infrastructure

A dedicated FreeBSD VM runs Zeek and Suricata, receiving SPAN-mirrored
traffic from the trunk uplink via a dedicated virtual network interface.
FreeBSD is not Linux; I chose it specifically for the sensor VM because
it has a stronger default security posture and I wanted the sensor
environment to be distinct from the Debian-based guest VMs that make
up the rest of the platform.

Zeek generates structured connection logs, protocol analysis, and file
extraction. Suricata provides signature-based detection against the
same traffic stream. Both ship telemetry to Fluentbit, which normalizes
and forwards to ClickHouse. Grafana dashboards provide visibility into
the telemetry.

This gives me network-level behavioral visibility for the entire
platform — not just internet-facing traffic but all inter-VLAN flows.
If something unexpected is crossing a segment boundary, I will see it.

---

## Identity and Access Management

The platform runs an SSH certificate authority using Ed25519 keys.
Every host presents a certificate signed by the internal CA rather than
a static authorized key. Certificate rotation is managed centrally.
Removing access means revoking a certificate, not hunting down
authorized_keys files across every host.

Keycloak provides SSO via OIDC for all internal web services. MFA is
enforced using WebAuthn hardware tokens. No service accepts a username
and password directly; everything authenticates through Keycloak.

FreeRADIUS provides 802.1X WPA Enterprise authentication for the
wireless network, backed by Keycloak for identity. Client devices
authenticate with credentials rather than a shared passphrase, and
RADIUS authentication events are logged to ClickHouse alongside
network telemetry.

The identity architecture follows a single principle: every
authentication event is logged, every access control decision is
centralized, and there are no static credentials anywhere in the
platform.

---

## What Comes Next

The platform is in active deployment. Immediate next phases:

**AI inference layer.** Ollama running a locally-hosted LLM for
analyst-facing intelligence synthesis. The operational security
requirement is firm: sensitive case data does not leave the
platform. Local inference is the only acceptable model for
production intelligence work.

**RAG pipeline.** Qdrant for vector search over the unstructured
corpus — Telegram message data, forum posts, investigation notes.
The goal is a unified query interface spanning both structured
breach data (ClickHouse) and unstructured collection (Qdrant),
accessible through natural language.

**Graph layer.** Neo4j for entity relationship storage — connecting
usernames, emails, infrastructure, and identities across data
sources. Identity correlation today requires manual pivot chains.
The graph layer automates traversal.

**Detection content.** The sensor infrastructure is in place.
Writing and tuning detection content against the behavioral
baseline is the work that makes it operationally useful.

I will document each of these as they come online. The architecture
document for the full platform is available on request for anyone
building something similar.

---

*Brian Scott is a security researcher and threat intelligence
practitioner based in Northeast Tennessee. He spent four years
as a Security Research Engineer at Cisco's Advanced Security
Initiatives Group and is the founder of Southwest Valley Research
Group.*
