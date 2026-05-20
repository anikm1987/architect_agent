# PRA SS1/21 — Operational Resilience

> **File purpose:** Condensed reference on PRA Supervisory Statement 1/21, "Operational resilience: Impact tolerances for important business services". When the agent discusses resilience, uptime, migration risk, or regulatory exposure for any UK-regulated client, this file supplies the language and structure to use.
>
> **Scope:** Evergreen. Applies to every UK-regulated financial services client in scope of PRA supervision — banks, building societies, PRA-designated investment firms, insurers, and Lloyd's. Does not reference any specific client.

---

## What it is

**Supervisory Statement 1/21** was issued by the Prudential Regulation Authority in March 2021 and came into full effect in March 2022. It sets the PRA's supervisory expectations for operational resilience across all PRA-regulated firms.

It runs in parallel with equivalent statements from the FCA (PS21/3) and the Bank of England Financial Market Infrastructures policy. The expectations are harmonised but each regulator enforces within its own remit.

## The core concept

SS1/21 requires firms to think about resilience through the lens of **important business services** — the services a firm provides to external customers or market participants that, if disrupted, could cause intolerable harm to consumers, market integrity, or the firm's safety and soundness.

Typical important business services vary by firm type:

- **Banks** — payments processing, account access, lending origination, card authorisation, regulatory reporting.
- **Insurers** — policy issuance and binding, claims payment, regulatory returns (Solvency II, bordereaux), policyholder record access, market messaging for Lloyd's participants.
- **Investment firms** — order execution, clearing and settlement, collateral management, client asset custody.
- **Market infrastructures** — clearing, settlement, messaging, reference data distribution.

Whatever the firm type, the regulatory obligations are the same:

1. **Identify** important business services.
2. Set an **impact tolerance** for each — the maximum tolerable length of disruption (measured in time, not availability percentage) before the harm becomes intolerable.
3. **Map** the people, processes, technology, facilities, and third parties that deliver each service end-to-end.
4. Test whether the firm could **remain within impact tolerance** during severe but plausible disruption scenarios.
5. Identify vulnerabilities and **remediate** them on a defined timeline.
6. Board-level ownership and **sign-off** of the impact tolerances.

## Why this matters for architecture decisions

Operational resilience under SS1/21 is **not the same thing as uptime targets or availability SLAs**. A 99.95% availability target says nothing about whether a 2-hour outage during a specific business-critical window would breach impact tolerance. The architecture has to be designed against the tolerance, not the average.

Practical implications to surface whenever relevant:

- **Service mapping is prerequisite to migration.** A firm cannot credibly migrate an important business service until it has mapped the service's end-to-end dependencies. SS1/21 expects this; regulators will ask for it.
- **Impact tolerance drives recovery design.** If the impact tolerance for a service is "4 hours", then recovery time objectives, failover design, and data replication architecture must demonstrably support it.
- **Third-party dependencies count.** Cloud providers, SaaS vendors, payment processors, managed service providers — all part of the resilience perimeter. Migration that introduces new third parties requires refreshed tolerance analysis.
- **Change itself is a risk event.** Migration, deployment, and configuration change are plausible disruption scenarios under SS1/21. Phased migration designs must explicitly address how resilience is maintained *during* transition, not just in the target state.
- **Testing is mandatory, not optional.** Firms must demonstrate they have tested scenarios. Architecture designs should enumerate the scenarios the design will be tested against.
- **Board accountability.** The board signs off tolerances. Anything that affects tolerance — including architectural choices — is ultimately a board-level concern, not purely a technology matter.

## Vocabulary to use fluently

- **Important business service** — A service provided externally, whose disruption could cause intolerable harm.
- **Impact tolerance** — Maximum tolerable length of disruption (hours or minutes), not availability percentage.
- **Severe but plausible scenario** — A disruption that is not the worst imaginable, but is realistic and stressing.
- **Service mapping** — End-to-end inventory of dependencies for a specific service.
- **Self-assessment** — The annual internal exercise required under the SS.
- **Lessons learned** — Post-incident learning, required to be fed back into tolerance and design.

## Interaction with DORA

For firms with European exposure or third-party dependencies in the EU, **DORA** (the EU Digital Operational Resilience Act) adds parallel obligations. The two frameworks are aligned in spirit but have distinct technical standards — particularly around ICT third-party risk, incident reporting thresholds, and threat-led penetration testing. Architecture for any UK firm with EU footprint should treat DORA and SS1/21 as a joint constraint, not an either/or.

## Red lines to flag

If any of these appear in proposed architecture, flag them immediately as SS1/21 risks:

- Migration approaches that do not explicitly address resilience *during* transition.
- Reliance on a single cloud region, single provider, or single third party for an important business service without documented tolerance analysis.
- Recovery time objectives defined without reference to mapped impact tolerances.
- "Cutover events" whose worst-case duration exceeds impact tolerance for the service being cut over.
- Absence of a documented service map for any important business service being re-platformed.
- Board papers that present transformation risk without explicit reference to SS1/21 impact tolerances.
