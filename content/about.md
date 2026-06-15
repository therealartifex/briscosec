---
title: "About"
layout: "page"
url: "/about/"
summary: "about"
draft: false
---

## Background

I am a security researcher and threat intelligence practitioner based in
Northeast Tennessee. My work spans offensive security research, operational
threat intelligence, digital forensics, and investigative OSINT.

From April 2022 through March 2026 I was a Security Research Engineer at
Cisco's Advanced Security Initiatives Group (ASIG), where I conducted
crystal-box product security evaluations across cloud, network, web
application, and AI attack surfaces. That work involved constructing
adversary-perspective attack models, conducting active testing in staging
environments, tracing complete exploit chains from initial vulnerability to
maximum product exposure, and delivering findings with remediation
recommendations integrated into Cisco's Secure Development Lifecycle and
Product Security Baseline.

Selected findings from that period include co-authorship of
[CVE-2022-38060](https://www.talosintelligence.com/vulnerability_reports/TALOS-2022-1589)
(CVSS 3.1: 7.8, High), a local privilege escalation in OpenStack Kolla
published by Cisco Talos; identification of a complete JWT signature
verification bypass enabling authentication bypass and arbitrary privilege
escalation across all protected endpoints of an internal web application;
a transport security survey of a distributed network security platform
identifying unauthenticated RPC channels confirmed via passive traffic
capture; and evaluation of a production LLM-integrated product via prompt
injection, demonstrating unauthorized data access paths.

---

## Southwest Valley Research Group

I founded Southwest Valley Research Group in April 2021 as an independent
security consultancy and intelligence operation. The work has two primary
tracks.

The first is operational threat intelligence: primary-source collection
and analysis across criminal Telegram channels, dark web markets, and
breach data ecosystems. I operate a large-scale breach intelligence corpus
spanning hundreds of datasets including traditional breach dumps, stealer
logs, and ransomware leak data, with a fully custom collection, ingestion,
parsing, normalization, and analysis pipeline built in Python and
ClickHouse. Intelligence products from this work have contributed to
active federal investigations and direct law enforcement referrals to the
FBI and ATF.

The second is forensic OSINT consulting for legal professionals. I provide
digital investigations, breach data analysis, identity attribution, and
subject location services in support of legal proceedings. I am in active
preparation for qualification as a court-recognized subject matter expert
in digital intelligence and breach data analysis.

---

## Technical Background

My offensive security work spans exploit research, dynamic instrumentation,
malware development, and EDR evasion. At Cisco ASIG, evaluation methodology
started with constructing adversary-perspective attack models before any
active testing began, and included static code analysis, reverse engineering,
active testing in staging environments, and proof-of-concept exploit
development. PoCs from that period were written in Go, C++, and PowerShell
depending on the target environment. The Duo Winlogon evaluation involved
hooking Windows cryptographic APIs at runtime using Frida to trace cleartext
credentials through a two-stage TPM decryption architecture during live
authentication. The Hypershield evaluation required writing Go exploits
demonstrating unauthenticated firewall policy injection into
hardware-accelerated enforcement on AMD Pensando DPUs.

Independent offensive security work has included building a working shellcode
loader in C++ that pulls ASCII hex-encoded payloads from a network source,
decodes them in memory, writes to an executable page, and executes without
touching disk. That loader was tested against Comodo AV using a plain
Meterpreter binary and evaded detection through the in-memory execution model.

The Sektor7 Red Team Operations course covered malware development,
Windows privilege escalation, and EDR evasion at an implementation level.
I hold GXPN (GIAC Exploit Researcher and Advanced Penetration Tester, 2024),
GCPN (GIAC Cloud Penetration Tester, 2022), OSCP (Offensive Security
Certified Professional, 2022), and CRTO (Zero-Point Security Certified Red
Team Operator, 2021).

On the data engineering side, I own the full pipeline for a large-scale
breach intelligence corpus: collection from criminal forums, Telegram
channels, and BitTorrent DHT; custom Python tooling for ingestion, parsing,
and normalization; and a ClickHouse analytical backend designed around the
columnar paradigm with native zstd compression, tiered hot/warm storage with
TTL-based partition migration, and text indexes for full-text queries across
the corpus. The ingestion tooling handles breach dumps, stealer logs, and
ransomware leak data across hundreds of datasets. I also wrote
[fff](https://github.com/therealartifex/fff), a SIMD/AVX-optimized
multi-threaded file search tool in C++ built specifically for the structural
consistency of stealer log formats, which outperforms grep at all tested
file sizes by distributing file chunks equally across cores and using SIMD
intrinsics where appropriate.

The intelligence analysis platform currently under development (SCIF) uses
a multi-backend architecture: ClickHouse for structured analytical queries
across ten dataset classes, Qdrant for semantic vector search over
unstructured collection data, Neo4j for provenance graph tracking and entity
relationship traversal, Redis for async job queue management, and a FastAPI
backend exposing a natural language query interface. Local LLM inference via
Ollama is a hard requirement for this platform — sensitive case data does
not leave the infrastructure.

I contributed a [native cracking module](https://github.com/hashcat/hashcat/pull/2914)
for Ruby on Rails RESTful Authentication hashes to the official hashcat
project, eliminating the manual preprocessing previously required for
this hash type.

My primary languages are Python, C/C++, Go, PowerShell, and C#. I have
production experience with Python for data pipeline work, C++ for
performance-sensitive tooling (SIMD/AVX, Windows API, in-memory execution),
Go for proof-of-concept exploit development, PowerShell for Active Directory
enumeration and Windows automation, and C# from several years of backend
development work including an ASP.NET/Informix DB stack at LeTourneau
University. I can read and work in most languages in active use without
difficulty.

Forensic and analytical tooling in regular use includes XWays Forensics,
Maltego, Burp Suite, Nuclei, Semgrep, Trufflehog, Wireshark, and the
standard Kubernetes and AWS CLI toolchains. For network analysis I work
with Zeek and Suricata output directly in ClickHouse. R is available for
statistical analysis of large datasets where Python's pandas is insufficient.

My ICS and SCADA background comes from four years at HOST Engineering, where
I developed PLC programming software in MFC and C++ for the Do-more H2/BRX,
DirectLogic DL205, and Terminator T1H product lines. Products from that
period were deployed in modular water treatment facilities and wind turbine
vibration analysis platforms. During that time I identified broadcast
amplification and packet replay vulnerabilities in HOST's proprietary Host
Automation Protocol through Wireshark analysis, leading to remediation in
deployed systems.

My senior capstone at LeTourneau was a role-based access control gateway
for industrial controllers: a Raspberry Pi hosting a Python Flask application
that triggered iptables rules granting authenticated users access to assigned
PLC ports on a local network. I led the three-person team, procured the
hardware, and configured the PLC, switch, and firewall.

---

## Education

**Bachelor of Science, Computer Science**
Network Security Concentration, Minor in Applied Sciences
LeTourneau University, Longview, TX (2018)

Concurrent with my degree I worked as a web developer in the university's
Business Systems Management department, maintaining backend systems for
the student portal and faculty web tools in C#/ASP.NET, Perl, and
Informix DB.

During my time at LeTourneau I competed in the TexSAW CTF hosted by UT
Dallas for three of four years. The team placed first in 2017.

---

## Contact

[hello@briscosec.io](mailto:hello@briscosec.io)

For professional inquiries related to Southwest Valley Research Group,
investigative consulting, or expert witness matters:
[brian@southwestvalley.us](mailto:brian@southwestvalley.us)
