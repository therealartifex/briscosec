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

My offensive security depth comes from four years of product security
research at Cisco and from independent research conducted through my
homelab and published work. I hold GXPN (GIAC Exploit Researcher and
Advanced Penetration Tester), GCPN (GIAC Cloud Penetration Tester), OSCP
(Offensive Security Certified Professional), and CRTO (Zero-Point Security
Certified Red Team Operator). I completed Sektor7's Red Team Operations
course covering malware development, EDR evasion, and Windows privilege
escalation.

Prior to Cisco, I worked as a software engineer at HOST Engineering, where
I identified vulnerabilities in SCADA protocol implementations. That
background in operational technology informs my understanding of why OT
environments present distinct defensive challenges relative to IT
infrastructure.

My data engineering background comes from building and operating the
breach intelligence infrastructure at Southwest Valley Research Group: the
full collection pipeline, the ClickHouse analytical backend, and the
custom tooling that supports operational queries across the corpus.

I write primarily in Python, C/C++, Go, and PowerShell. Published
open-source work includes
[fff](https://github.com/therealartifex/fff), a SIMD/AVX-optimized
multi-threaded search tool purpose-built for stealer log analysis, and a
[hashcat module](https://github.com/hashcat/hashcat/pull/2914) for
cracking Ruby on Rails RESTful Authentication hashes.

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
