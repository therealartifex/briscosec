---
title: "Active Cyber Defense Comes of Age"
date: 2026-08-13
draft: false
tags: ["cybersecurity", "policy", "active-defense", "threat-intelligence", "critical-infrastructure"]
description: "An analysis of the White House memorandum authorizing private-sector participation in offensive cyber operations, and why it matters for a debate that has stalled for a decade."
---

Two Chinese state-sponsored hacking groups have confirmed persistent access inside US power
grids, water systems, and telecommunications infrastructure. Their presence spans years.
Their activity does not resemble bulk data exfiltration or the kind of disruption typically
associated with criminal ransomware. According to a CISA advisory published in February 2026,
the characterization from the intelligence community is pre-conflict positioning: establishing
persistent footholds that can be activated during a future military or geopolitical crisis.

This is the threat environment that existed when the debate over active cyber defense was
largely theoretical. It is no longer theoretical. And yesterday, for the first time, the US
government published a formal framework for incorporating the private sector into the response.

---

## A Decades-Old Question

In 2005, Shawn Carpenter, a network security analyst at Sandia National Laboratories, was
actively tracking a Chinese cyber espionage campaign the security community had named Titan
Rain. The campaign had compromised systems at NASA, Redstone Arsenal, the World Bank, and
Lockheed-Martin, extracting sensitive data in operations that took as little as ten minutes
from initial access to exfiltration. Carpenter had implanted tracking software on attacker
infrastructure and was assembling a detailed picture of the network behind the campaign. The
FBI, which had initially used him as a confidential informant, eventually opened an
investigation against him instead. The evidence he had collected was never used. Titan Rain's
operators were never charged.

The Carpenter case illustrates the structural problem that has defined this debate for two
decades. A capable defender, operating in good faith against a documented threat, had no legal
framework to operate within. The Computer Fraud and Abuse Act of 1986 prohibits unauthorized
access to systems outside one's own network. Defenders are legally confined to their own
perimeter regardless of what is happening beyond it.

The primary legislative attempt to address this gap was the Active Cyber Defense Certainty Act,
which proposed amending the CFAA to allow limited actions beyond a victim's network: tracking
attackers, deploying beaconing technology to establish attribution, retrieving or destroying
stolen data before it could be used, and disrupting ongoing attacks. The bill was introduced
in 2017, reintroduced in 2019, and never passed. The legal gap remained open.

On the government operations side, the 2018 National Security Presidential Memorandum 13
established a "defend forward" doctrine that gave US Cyber Command explicit authority to
conduct offensive operations outside US networks to disrupt threats before they materialize.
That authority applied to the military. It did not extend to private organizations defending
their own infrastructure or operating on behalf of law enforcement.

---

## Passive Defense Is Not Enough

Having moved from SCADA security engineering to years of operational threat intelligence work
tracking criminal networks, I've observed this dynamic from both ends of the problem:
adversaries with patience and resources consistently outpace defenders limited to detection
and containment.

Passive defense (hardening systems, reducing attack surface, improving detection coverage) is
absolutely necessary. The question is whether it is sufficient against the current threat
environment, and the evidence of the last several years makes that case difficult to sustain.

The defending side operates under a fundamental positional disadvantage. A defender must
protect every path into a system, and an attacker needs only one. A defender operates under
legal authority, compliance requirements, disclosure obligations, and organizational approval
chains, but a persistent adversary operates under none of them. When the adversary also has
nation-state resources and multi-year patience, the disadvantage of purely reactive defense
becomes acute. Passive defense is optimized for breadth and resilience against the bulk of
the threat distribution. The part of that distribution it struggles with is the tail: patient,
well-resourced actors who study the detection environment and operate specifically to avoid
triggering its thresholds.

Let's take a look at just the last several years. The SolarWinds supply chain compromise was
active for at least nine months before discovery, affecting roughly 18,000 organizations
including multiple federal agencies. The attackers operated patiently and used trusted software
update infrastructure specifically to blend into the traffic patterns that defenders rely on for
baselining. The Colonial Pipeline ransomware deployment shut down fuel supply to a significant
portion of the US East Coast within hours of execution, demonstrating that criminal
organizations can now achieve infrastructure-level effects that previously required nation-state
resources. Volt Typhoon maintained persistent access inside US critical infrastructure,
including operational technology networks in the energy and water sectors, for years, using
living-off-the-land techniques that produce minimal forensic artifacts and are deliberately
designed to be indistinguishable from normal administrative activity.

In OT environments specifically, the defensive challenge compounds. Industrial control systems
were designed for reliability and deterministic behavior, properties that are in direct tension
with the security monitoring and patching cadences that are standard in IT environments. The
result is that OT networks frequently have longer mean-times-to-detection and harder
remediation paths once a compromise is confirmed. An adversary who understands OT architecture
can operate in that environment for extended periods with limited risk of disruption.

---

## The August 2026 Memorandum

Yesterday the White House published a presidential memorandum titled "Expanding Capabilities to
Combat Transnational Cyber-Enabled Crime." The memo establishes a formal Program through the
National Coordination Center (NCC), co-directed by the Department of Justice and the Department
of Homeland Security, under which vetted private US companies can be authorized to conduct two
categories of cyber operations against foreign criminal organizations.

The first is Cyber Surveillance Operations: accessing adversary systems without authorization
to collect intelligence, including intelligence that can support future operations. The second
is Cyber Effects Operations: actions that result in manipulation, disruption, denial,
degradation, or destruction of adversary systems, infrastructure controlled by those systems,
or data resident on them.

The program applies to a precisely defined class of adversary: Cyber-Enabled Transnational
Criminal Organizations (CE-TCOs). Put simply, these are foreign groups conducting cyber-enabled
crime against the US government, US persons, or US interests that are not institutional parts
of a foreign government and are not wholly operated under foreign government direction. LockBit,
the criminal ransomware operation disrupted by international law enforcement in 2024, fits that
definition. Volt Typhoon, with its documented ties to China's People's Liberation Army, does
not. The program's authority deliberately draws a line between criminal actors and state actors.

Every operation requires written approval from the Program Executive Directors before any
action is taken, with oversight layered across multiple review points. Operations likely to
generate "Critical Outcomes" (defined as actions likely to cause loss of life, serious injury,
or an armed attack under international law) cannot be approved at the program director level
at all. Participating Companies undergo rigorous vetting and must maintain a minimum $1 million
bond, subject to forfeiture for non-compliance. Annual evaluation determines continued
participation. Companies must immediately notify the NCC if an operation inadvertently targets
a US person, touches a US-based information system, or if they discover that an approved
operation may result in Critical Outcomes. Any operation implicating US persons or
constitutional obligations requires DOJ review and appropriate authorization before the
operation is approved.

The memo states that all Program activities will comply with section 1030 of title 18 (the
CFAA), and that language deserves careful reading. The statute has not been amended. The legal
basis is that Participating Companies operate under the direction and legal authority of the
federal government, functioning as government contractors rather than as independent actors.
Their authorization flows from government authority, not from any new private-sector right.
That distinction defines what the program enables and what falls outside its scope when
companies act independently.

This memorandum builds on Executive Order 14390, signed in March 2026, which directed the
federal government to take coordinated action against cyber-enabled crime. The August
memorandum is the mechanism through which private sector capabilities are incorporated into
that effort.

---

## Accountability by Design

The accountability concerns that have defined opposition to active cyber defense for a decade
are directly addressed in the structure of this program.

The Carpenter problem, a capable defender operating alone without authorization or oversight,
is specifically precluded. Before any operation is undertaken, the memo requires that "the
Program Executive Directors review every cyber operations package and provide written approval
and direction to the Participating Company before action may be taken," creating a documented
record of what was authorized, under what authority, and within what scope. The
DOJ/DHS co-director structure creates cross-agency accountability that no single agency can
override unilaterally. The $1 million bond creates financial consequence for non-compliance.
The minimization and notification requirements for inadvertent US person contact are explicitly
mandated rather than left to company discretion.

The concern about abuse of power, that entities given substantial legal authority will use it
against parties who are not the intended targets, is addressed through the CE-TCO scope
limitation, the Critical Outcomes prohibition, and the DOJ review requirement for operations
implicating US persons. The program establishes multiple independent review points before
action is authorized at any stage.

The memo's eligibility requirements are worth calling out specifically. The implementation
procedures must develop standards enabling, in the memo's words, "large companies, which may
be well-suited to provide significant capacity across multiple operational areas, and smaller,
more agile companies, which may be well-suited to perform specialized or discrete tasks." The
60-day implementation guidance will show whether that intent translates into actual practice,
and it is the clearest signal of whether the program draws on the full depth of US
private-sector security expertise or concentrates in familiar contractors.

The memo also explicitly preserves other lawful defensive operations that Participating
Companies conduct outside the program. Participation is additive to existing defensive activity.

---

## The State Actor Boundary

The CE-TCO definition explicitly excludes groups that are institutional parts of a foreign
government or wholly operated under foreign government direction. Volt Typhoon and Salt Typhoon,
whose pre-positioning opened this article, fall outside this program's authority. Operations
against state-directed cyber actors remain in the domain of CYBERCOM's defend-forward doctrine,
intelligence community authorities, and diplomatic and sanctions tools.

I believe that scope limitation is the best call right now. The accountability structures
appropriate for law enforcement contractor operations are different from those suited to
activities that could constitute use of force under international law. Extending criminal
enforcement authority to state-actor operations would create legal and escalation risks the
program is right to avoid.

The harder case is the middle tier. Groups like Lazarus Group (North Korea) and historically
groups like REvil (Russia) operate with apparent state protection or direction but are
structured as criminal organizations. The memo states that "a foreign group will be assumed not to be an institutional part of a
foreign government or wholly operated under a foreign government's direction unless clear
intelligence exists establishing such connection." How that determination
is made, who makes it, and what evidentiary threshold that standard represents are all addressed
in the classified annex to the memorandum. The public cannot evaluate those determinations
directly. While that is reasonable right now given the operational sensitivity of the question,
this gap in public accountability should be addressed over time.

---

## Considerations and Limitations

**Attribution.** Attribution for cyber operations has improved substantially over the past
decade through combined intelligence community and private sector work. Placing attribution
responsibility in government hands before any operation is approved is the appropriate design.
The tradeoff is latency: adversaries can move infrastructure, erase logs, and pivot to new
footholds faster than a sequential approval workflow can respond. AI-assisted attack tooling is
sharpening that problem further, incorporating automated obfuscation and polymorphic delivery
that raise the confidence threshold required for a sound approval decision. AI-driven analysis
has improved infrastructure pattern recognition and actor fingerprinting at scale; the net
balance between those countervailing forces is still developing. Whether the implementation
guidance builds in processes fast enough to keep pace is an open question the 60-day deadline
should address.

**International law and third-country presence.** CE-TCO infrastructure frequently resides in
third-country jurisdiction regardless of where the criminal organization is based. Operations
against that infrastructure implicate the laws of those countries, not only US law. The memo
references compliance with US international obligations, and the classified annex addresses the
operational workflow, but the specific framework for third-country legal analysis is not
publicly available.

**Unintended effects.** Criminal infrastructure is often shared across multiple criminal
operations simultaneously, and compromised legitimate hosts frequently serve as relays. The
minimization and notification requirements in the memo address the US person dimension of this
risk. Avoiding collateral effects to legitimate hosts in adversary infrastructure requires
sound targeting and operational discipline that the implementation guidance will need to specify.

**Program continuity.** This program is established by presidential memorandum, not
legislation. A subsequent administration can modify or revoke it. For private companies
investing in vetting procedures, operational infrastructure, and the bond requirement,
long-term program stability is a material consideration. Legislation that codifies the
framework would provide durability that a presidential memorandum cannot. The ACDC Act spent
nearly a decade failing to achieve that outcome; whether this program creates the operational
track record that builds congressional support for a statutory foundation remains to be seen.

---

## A Framework, Finally

The argument for active cyber defense has been made consistently since at least the Titan Rain
era. The counterarguments have been consistent as well: attribution errors lead to misidentified
targets, unchecked authority gets abused, and escalation risk rises when defenders move beyond
their own perimeter. For a decade, those arguments produced repeated legislative attempts and
no durable framework.

Yesterday's memorandum is the first formal structure that directly addresses those failure
modes at the design level: mandatory pre-approval, layered government oversight, financial
accountability, explicit scope limitations, and minimization requirements embedded in the
operational workflow. The program targets criminal organizations under law enforcement
authority, drawing a clear boundary at state-directed actors and at operations that would
constitute armed conflict under international law.

Whether the implementation delivers on that design intent will take longer to evaluate.
The eligibility criteria, the approval tempo, and the attribution confidence thresholds are
all determined in the 60-day implementation guidance rather than in the public text of the
memo. The architecture, at minimum, reflects a decade of accumulated understanding about what
the objections to active defense actually require. Twenty years ago, Shawn Carpenter had the
will to act but no legal ground to act on; that ground now exists, and the work ahead is to
make sure it holds.
