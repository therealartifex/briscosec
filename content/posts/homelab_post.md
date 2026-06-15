---
title: "Building a Threat Intelligence Research Platform: Architecture and Design Decisions"
date: 2026-06-13
draft: false
tags: ["homelab", "threat-intelligence", "clickhouse", "infrastructure", "detection"]
description: "Architecture and design rationale for a multi-tier security research environment supporting threat intelligence operations, detection engineering, and breach data analysis."
---

Several years of running threat intelligence and investigative operations on
informal infrastructure made the gaps obvious. Breach data analysis, Telegram
collection, case investigation tooling, and security research were sharing the
same environment with no isolation between operational contexts, no network
visibility, and no detection capability pointed inward. A malformed archive
during ingestion could affect active case data. A noisy scan could contaminate
telemetry. This post documents the architecture built to address those
problems and the reasoning behind each design decision.

---

## Requirements

The platform was designed around five core requirements:

- Network segmentation isolating distinct operational contexts from each other
  and from general internet traffic
- A production-grade analytical data store for the breach intelligence corpus,
  capable of spanning hundreds of datasets and supporting aggregation-heavy
  OLAP queries
- Network-level behavioral visibility with telemetry feeding a detection layer,
  covering all inter-VLAN traffic including flows that never reach internet egress
- A centralized identity and access management layer with auditable
  authentication, replacing ad-hoc SSH key management
- Sufficient compute and memory for local large language model inference,
  eliminating the operational security risk of sending sensitive case data to
  external cloud APIs

---

## Hardware and Hypervisor

The primary host is a Cisco UCS C240 M5, a dual-socket 2U rack server
running enterprise-class Xeon processors with several hundred gigabytes of
RAM. The memory headroom was the primary selection criterion: local LLM
inference at useful context lengths, memory-mapped columnar storage for the
breach data analytical workload, and multiple isolated VM environments running
simultaneously without resource contention all require it.

The hypervisor is Proxmox VE, selected for its zero licensing cost and
comprehensive community documentation. The platform runs a mix of Debian
and FreeBSD guest VMs depending on workload requirements.

---

## Network Architecture

The network stack runs on UniFi hardware. Seven VLANs isolate distinct
security contexts, with inter-VLAN traffic controlled by firewall policy at
the gateway. The default policy is deny; explicit rules permit only the
specific flows the architecture requires.

**Management:** Hypervisor management interfaces and network equipment
administration. No internet routing. Reachable only from a dedicated
management workstation on the same VLAN.

**Operations:** Active investigation tooling, Telegram collection, and case
work. Internet access through a dedicated egress path with logging.

**Data Platform:** ClickHouse, ingestion pipelines, and analytical
infrastructure. No direct internet routing; data arrives exclusively via
the ingestion VLAN.

**Ingestion:** The boundary zone where raw collected data enters the
platform. Isolated from both the operations and data platform VLANs; data
flows in one direction only. This prevents a compromised or malformed
dataset from having network-level access to operational systems during
processing.

**Detection:** Network sensor infrastructure receiving a SPAN copy of the
trunk uplink. No internet routing. The sensor VM has a write-only path to
the data platform for telemetry delivery and participates in no other
network flows.

**Services:** Internal services including the reverse proxy, DNS resolver,
and authentication infrastructure.

**Lab/Research:** Isolated environment for working with potentially hostile
samples, running offensive tooling, and general security research. No
routing to production VLANs.

The segmentation design follows a consistent threat model: assume compromise
of any single VLAN and evaluate whether that compromise can propagate. Each
boundary is enforced at the network layer via firewall policy rather than
by application-level convention.

The detection VLAN receives a SPAN mirror of the trunk uplink, giving the
sensor VM full visibility into all inter-VLAN traffic without participating
in any of it. This means the sensor is invisible at the network layer while
observing all flows across every segment boundary.

---

## Data Platform

The breach intelligence corpus spans several hundred datasets including
traditional breach dumps, stealer logs, and ransomware leak data. The
analytical workload is aggregation-heavy: identity correlation across
sources, infrastructure attribution, password reuse pattern analysis, and
actor profiling across datasets. These are OLAP queries over columnar data,
structurally different from the document retrieval and full-text search
workloads that Elasticsearch was designed for.

ClickHouse was selected after running both stacks against this workload.
The relevant characteristics:

**Columnar storage.** Analytical queries over specific fields across hundreds
of millions of rows read only the columns they need. A query correlating
email addresses across all datasets touches the email column exclusively,
regardless of how many other fields exist in the schema.

**Native zstd compression at the column level.** Breach data has highly
repetitive structure within columns: the same email domains, password
patterns, and field value distributions appear across datasets. Column-level
compression of repetitive data achieves substantially better ratios than
general-purpose row-level compression.

**Resource footprint.** At equivalent data volume, ClickHouse requires
significantly less RAM than Elasticsearch for aggregation queries. This
matters when the same hardware also runs LLM inference.

**Text indexes.** Recent ClickHouse versions added full-text search indexes
that cover the use cases where Elasticsearch previously had an advantage.

**Migration cost.** The ingestion pipeline was rewritten and the data models
redesigned for the columnar paradigm. The performance and resource gains
justified the effort.

The ingestion pipeline is Python, handling collection, deduplication,
parsing, normalization, and schema-aligned loading. The full pipeline from
raw source data to queryable ClickHouse records is owned and operated
independently.

---

## Detection Infrastructure

A dedicated FreeBSD VM runs Zeek and Suricata, receiving SPAN-mirrored
traffic from the trunk uplink via a dedicated virtual network interface.
FreeBSD was selected for the sensor VM for its security posture and to
maintain a clear distinction between the sensor environment and the
Debian-based guest VMs comprising the rest of the platform.

Zeek generates structured connection logs, protocol analysis records, and
file extraction. Suricata provides signature-based detection against the
same traffic stream. Both ship telemetry to Fluentbit, which normalizes
and forwards to ClickHouse. Grafana provides dashboard visibility over
the telemetry.

The result is behavioral visibility across all inter-VLAN flows, including
inter-segment traffic that would otherwise be invisible at the host level.
Unexpected flows crossing segment boundaries surface in telemetry.

---

## Identity and Access Management

The platform runs an internal SSH certificate authority using Ed25519 keys.
Every host presents a certificate signed by the CA rather than a static
authorized key. Removing access means revoking a certificate centrally.
Certificate-based authentication also provides a consistent audit trail
across all hosts.

Keycloak provides SSO via OIDC for all internal web services. MFA is
enforced via WebAuthn hardware tokens. No internal service accepts
credentials directly; all authentication flows through Keycloak.

FreeRADIUS provides 802.1X WPA Enterprise authentication for the wireless
network, backed by Keycloak for identity. Client devices authenticate with
per-device credentials rather than a shared passphrase. RADIUS
authentication events are forwarded to ClickHouse alongside network
telemetry, providing a unified authentication and network event timeline
in a single queryable store.

The identity architecture enforces three properties: every authentication
event is logged, every access control decision is centralized, and no
static credentials exist anywhere in the platform.

---

## Planned Extensions

**Local LLM inference.** Ollama running a self-hosted model for
analyst-facing intelligence synthesis. Local inference is a hard
requirement for this use case: sensitive case data does not leave the
platform.

**RAG pipeline.** Qdrant for vector search over unstructured corpus data
including Telegram message exports and forum content. Combined with
ClickHouse for structured queries, the goal is a unified natural language
query interface spanning both data types.

**Graph layer.** Neo4j for entity relationship storage. Usernames, email
addresses, infrastructure, and identities connected across data sources.
The graph layer enables relationship traversal that SQL queries cannot
express: finding all entities within N hops of a known actor across all
datasets.

**Detection content.** The sensor infrastructure is operational. Writing
and tuning detection logic against the behavioral baseline is the work
that makes the visibility layer analytically useful.

