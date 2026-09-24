---
title: "Designing a Segmented Security Research Lab"
date: 2026-09-24
draft: false
tags: ["homelab", "infrastructure", "network-security", "detection", "architecture"]
description: "A reference architecture for a segmented security research lab: isolating operational contexts, pointing visibility inward, and eliminating static credentials."
---

Running security research, data analysis, and tooling development in one flat
environment creates problems that compound quietly. A mistake in one workflow
can reach another. There's no visibility into what's actually talking to what.
And when everything shares a trust boundary, "assume breach" stops being a
threat model and becomes wishful thinking. This post walks through a segmented
lab design that treats each type of work as its own security context, and the
reasoning behind each boundary.

## Segmentation as the foundation

The core idea is to separate distinct operational contexts into isolated
network segments, with a default-deny policy between them and explicit rules
for only the flows the design actually needs. A useful way to decide where the
boundaries go is to assume any single segment is compromised and ask what that
compromise can reach. Wherever the answer is uncomfortable, there's a boundary.

A reasonable starting decomposition:

- **Management**: infrastructure and equipment administration, reachable only
  from a dedicated administrative context and never from the internet.
- **Ingestion**: a boundary zone where external data first lands, isolated so
  that handling untrusted input can't touch anything downstream. Data flows out
  of it in one direction only.
- **Data and analysis**: where structured data is stored and queried, with no
  direct internet exposure. Input arrives only through the ingestion boundary.
- **Detection**: sensor infrastructure that observes traffic without
  participating in it.
- **Services**: shared internal services such as a reverse proxy, DNS, and
  authentication.
- **Research**: an isolated environment for offensive tooling and untrusted
  samples, with no route to anything sensitive.

The specific segments matter less than the principle: each boundary is enforced
at the network layer by policy, not by application-level convention that's easy
to forget or misconfigure.

## Visibility that points inward

Most home setups watch the perimeter and go blind internally. A more useful
model gives a sensor a mirror of internal traffic so it can observe flows
between segments, including ones that never reach the internet. The sensor sits
off to the side: it can see everything and reach nothing, which means it adds
visibility without adding attack surface. Structured connection and protocol
logs plus signature-based detection feed a store you can query, so unexpected
movement across a boundary becomes something you can actually notice.

The honest caveat: visibility is only the substrate. The work that makes it
valuable is writing and tuning detection logic against your own baseline, and
that's ongoing rather than a switch you flip.

## Choosing an analytical store

For workloads that are aggregation-heavy, scanning specific fields across a lot
of rows rather than fetching whole records, a columnar store is a natural fit.
Reading only the columns a query touches, combined with strong column-level
compression on repetitive data, keeps both query cost and storage footprint
manageable on modest hardware. That's the reasoning behind the choice,
independent of any specific product: match the storage model to the shape of
the queries. If your access pattern were single-record lookups or full-text
retrieval instead, a different store would be the right call.

## Identity without static credentials

The goal worth aiming for is that no long-lived credential sits on any host. An
internal certificate authority issues short-lived, signed credentials, so
revoking access is a central action rather than a hunt across machines.
Centralized single sign-on with hardware-backed multi-factor authentication
fronts internal services, and per-device network authentication replaces shared
passphrases. The three properties that fall out of this: every authentication
is logged, every access decision is centralized, and there's nothing static for
an attacker to steal and reuse.

## A sensible starting point

If you're building something similar, the order that tends to work is: segment
first and get the boundaries right, then add inward-facing visibility, then
centralize identity, and only then layer on the heavier workloads. None of it
requires exotic hardware. It requires deciding, up front, what each part of
your environment is allowed to reach, and enforcing that decision at a layer
that doesn't depend on your remembering it later.
