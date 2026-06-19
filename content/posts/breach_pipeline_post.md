---
title: "Building a Large-Scale Breach Intelligence Pipeline: Collection, Ingestion, and ClickHouse Analytics"
date: 2026-06-18
draft: false
tags: ["threat-intelligence", "breach-data", "clickhouse", "data-engineering", "osint"]
description: "End-to-end architecture and design decisions behind a production breach intelligence corpus spanning hundreds of datasets, including stealer logs and ransomware leaks."
---

Several years of operational threat intelligence work made the limitations
of ad-hoc tooling obvious. Manual searches across scattered breach dumps,
inconsistent parsing, and slow aggregation queries limited my ability to
perform timely identity correlation, password reuse analysis, and actor
profiling. This post documents the full pipeline I built, from
primary-source collection through normalization and loading into a
purpose-built analytical backend, and the reasoning behind key technical
decisions.

---

## Requirements

The system was designed around four core needs:

- Reliable collection from criminal Telegram channels, forums, and public
  breach distribution networks, with purchased acquisition used sparingly
  rather than as a default.
- Robust ingestion capable of handling varied archive formats (7z, RAR,
  ZIP), deduplication, parsing, and schema-aligned normalization.
- An analytical store optimized for OLAP-style aggregation queries across
  a large multi-dataset corpus (identity correlation across sources,
  infrastructure attribution, pattern detection).
- Resource efficiency suitable for homelab hardware that also supports
  other workloads including local LLM inference and detection telemetry.

---

## Collection Layer

Primary sources include criminal Telegram channels, monitored via custom
tooling built around the tdl API wrapper, successors to BreachForums and
RaidForums, and public BitTorrent DHT distributions following forum
takedowns.

Purchasing data is common in this field, particularly for practitioners
who are newer to the space or who haven't built deep access into the
channels where material circulates first. I avoid it where possible, not
out of a hard rule against it, but because criminal utility decays
quickly while analytical value for attribution and historical correlation
remains high regardless of how the data was obtained. Direct collection
from Telegram channels, forums, and DHT distributions, combined with
patience, generally surfaces the same material once it ages out of its
original criminal use.

Telegram collection runs continuously, exporting messages and attached
archives. Forum and DHT monitoring identifies new dumps for manual or
semi-automated download.

All incoming material is staged with metadata (source channel/forum,
download timestamp, original filename) recorded in a catalog database.

---

## Ingestion and Normalization Pipeline

The pipeline is implemented in Python and handles the full flow:

1. **Archive handling and deduplication**: SHA-256 hashing of raw archives
   for deduplication against the catalog. Previously seen material is
   logged and skipped.
2. **Extraction**: Support for common formats (7z, RAR, ZIP, plain text)
   with password extraction from accompanying channel/forum messages
   where provided.
3. **Parsing**: Format-specific parsers for breach dumps (JSON, CSV, SQL),
   stealer logs (highly structured but variable schemas), and ransomware
   leak packages.
4. **Normalization**: Fields are mapped to a common schema (email,
   username, password hash, IP, user agent, etc.). Entity resolution
   begins here with basic username and email canonicalization.
5. **Validation and enrichment**: Basic data quality checks, timestamp
   normalization, and initial tagging (source dataset, ingestion date).

The pipeline runs as scheduled workers with idempotent design; failed or
partial runs can be retried without data corruption.

---

## Analytical Backend: ClickHouse Migration

The corpus was originally stored in Elasticsearch. After evaluating
performance against real workloads (heavy aggregations across fields
like emails, passwords, and infrastructure), I migrated to ClickHouse.

**Key decision factors**:

- **Columnar storage and OLAP optimization**: Queries correlating
  identities across datasets touch only the necessary columns. A search
  for a specific email domain scans far less data than row-oriented
  stores.
- **Compression**: Native zstd at the column level achieves excellent
  ratios on repetitive breach data (common domains, password patterns,
  field distributions).
- **Resource efficiency**: Substantially lower RAM usage for aggregation
  workloads compared to Elasticsearch at equivalent scale. Critical when
  the same server supports detection telemetry and local inference.
- **Text search**: Recent ClickHouse full-text indexes cover the
  remaining use cases where Elasticsearch previously held an advantage.
- **Tiered storage**: Hot storage for recent and high-value datasets,
  warm storage for older partitions via TTL policies. Automatic
  migration reduces operational overhead.

The migration required redesigning data models for the columnar paradigm
and rewriting ingestion logic. The performance and efficiency gains
justified the effort.

---

## Operational Tooling and Search

- **fff**: A custom SIMD/AVX-optimized, multi-threaded C++ search tool
  ([github.com/therealartifex/fff](https://github.com/therealartifex/fff))
  I wrote for rapid scanning of stealer log files before ingestion. It
  outperforms standard grep on the consistent record structure of these
  logs.
- **ClickHouse queries**: Primary interface for analysis. Common
  patterns include cross-dataset identity resolution, password reuse
  detection, and temporal actor profiling.
- **Integration points**: Zeek and Suricata telemetry from the homelab
  detection VLAN also lands in ClickHouse, enabling unified queries
  across network behavior and breach data.

---

## Current State and Planned Extensions

The pipeline currently supports hundreds of datasets, with new material
ingested on an ongoing basis. I own collection, parsing, and loading
end-to-end.

Planned extensions include tighter integration with the entity graph
layer (Neo4j) for relationship traversal and the RAG pipeline (Qdrant
plus a local LLM) for natural language queries over both structured
breach data and unstructured Telegram and forum exports.

This infrastructure directly supports active investigative work and
demonstrates the engineering foundation required for scalable threat
intelligence operations.
