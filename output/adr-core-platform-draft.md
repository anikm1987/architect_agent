# ADR-001: Core Platform Architecture for Atlas Insurance Digital Transformation

**Status:** Proposed  
**Date:** 2026-04-29  
**Deciders:** CTO (David Chen), CEO (Sarah Hendricks) — for board ratification  
**Review trigger:** Guidewire contract signature, or material change to Lloyd's Blueprint Two timeline

---

## Context

Atlas Insurance operates a core policy administration system built on IBM iSeries/COBOL, in continuous production since 1998 with no documented API surface. Claims is handled by an unsupported legacy package with no clean integration to policy data. No service bus, no event stream, no API gateway exists. The IT organisation of 140 people is predominantly skilled in mainframe and COBOL, with limited cloud-native experience.

Three pressures create a non-negotiable transformation timeline:

1. **Lloyd's Blueprint Two** mandates API-readiness for Atlas's syndicate operation on Lloyd's published schedule. Atlas currently has zero API capability. This is a contractual obligation, not a strategic ambition.
2. **PRA enhanced supervisory attention** following last year's near-miss on the Solvency II Q3 return — the direct consequence of manual reconciliation between claims and policy systems.
3. **PE investor exit horizon** of two to three years and a CFO-imposed six-month value demonstration test for Phase 1.

The transformation budget is capped at £50M over three years and is not adjustable upward. The core system's 99.9% availability SLA must be maintained throughout any migration. Solvency II quarterly returns cannot be disrupted under any circumstances.

---

## Decision Drivers

| Driver | Weight | Rationale |
|---|---|---|
| Lloyd's Blueprint Two compliance | Must-have | Hard contractual deadline; failure affects syndicate licence |
| Solvency II continuity | Must-have | PRA enhanced supervision; CRO has board veto |
| 99.9% core availability | Must-have | Explicit programme constraint |
| CFO 6-month value test | High | Programme reset risk if missed; PE investor visibility |
| £50M budget constraint | High | Non-negotiable ceiling |
| Workforce transition via upskilling | High | Union concerns live; board-level HR risk |
| Time to digital channel capability | Medium | Aggregator conversion rate is 40% below median |

---

## Options Considered

### Option A — Guidewire Cloud Suite (Full Replacement)

Deploy Guidewire PolicyCenter, ClaimCenter, and BillingCenter as a hosted cloud suite, replacing the COBOL system and the legacy claims package on a phased but Guidewire-led timeline.

**What this buys:** Pre-built insurance domain logic for home, motor, commercial property, and liability. Cloud-hosted — Guidewire bears infrastructure resilience. Lloyd's Blueprint Two connectors exist as certified integrations. Out-of-the-box API layer. Claims-to-policy data integration resolved by a single canonical data model.

**Why it is insufficient as a standalone choice:**

- A Guidewire full-suite implementation for a firm of Atlas's scale and product complexity typically runs £20–35M in professional services alone before licensing, integration with brokers/reinsurers, and data migration. This exhausts the budget before any digital channel investment.
- Guidewire implementations routinely slip 6–12 months beyond initial estimates. A single large-programme model creates material risk of missing the CFO's six-month test and the Lloyd's deadline simultaneously.
- Guidewire is deeply opinionated. Atlas has 25+ years of custom underwriting rules and rating factors embedded in COBOL. Porting or modelling these in Guidewire's configuration layer is a significant project in its own right, and the knowledge largely lives in the heads of long-tenured developers — not in documentation.
- Atlas's COBOL developers cannot operate a Guidewire system. The workforce transition problem is not solved; it is transformed into a different flavour of the same problem, with a vendor-proprietary language (Gosu) substituted for COBOL.

**Verdict:** Insufficient as a standalone. Guidewire is the right engine for the core; the programme structure must be designed around it, not left to Guidewire's default delivery model.

---

### Option B — Composable Microservices (Greenfield Build)

Design and build a cloud-native, microservices-based policy administration, claims, and billing platform from scratch, using a domain-driven decomposition aligned to Atlas's product lines.

**Why this is not viable:**

- A greenfield microservices policy administration system capable of replacing Atlas's COBOL estate — covering home, motor, commercial property, liability, and Lloyd's syndicate operations — represents a minimum of five to eight years of sustained engineering effort. Atlas's three-year budget horizon and PE exit constraints are irreconcilable with this timeline.
- Atlas has no cloud-native engineering capability today. Building it — hiring, training, establishing DevSecOps practice, Kubernetes operations, event streaming — is a multi-year journey in its own right. The COBOL team cannot design microservices; the microservices team, if hired, cannot maintain the COBOL system during coexistence. This creates two parallel teams, doubling the people cost within a fixed budget.
- Lloyd's Blueprint Two is a hard deadline. A greenfield build cannot credibly deliver certified Lloyd's API connectivity on Lloyd's timeline.
- The CFO's six-month value test has no plausible deliverable from a greenfield programme. The six-month output of a greenfield programme is typically infrastructure scaffolding, CI/CD pipelines, and initial domain modelling — none of which is visible to the business.

**Verdict:** Rejected. The constraints of budget, timeline, workforce, and regulatory deadline make this option non-viable. Pattern stacking on a team with no cloud-native experience is not a strategy.

---

### Option C — Structured Hybrid: Guidewire Core + Purpose-Built Digital Layer (Recommended)

Adopt Guidewire Cloud Suite as the authoritative system of record for policy administration, claims, and billing — but scope the Guidewire programme tightly to the core domain, and build a thin, cloud-native digital engagement and integration layer alongside it using composable microservices.

**Architecture decomposition:**

| Layer | Technology approach | Rationale |
|---|---|---|
| Core policy, claims, billing | Guidewire Cloud Suite (hosted) | Insurance domain logic, regulatory compliance, Lloyd's connectors |
| API gateway and Lloyd's integration | Cloud-native (AWS API Gateway or Azure APIM + custom adapters) | Lloyd's Blueprint Two compliance; broker and reinsurer connectivity |
| Digital customer channels | Cloud-native microservices | Quote-to-bind journey; aggregator integration; self-service |
| Data and regulatory reporting | Cloud data platform (separate from Guidewire) | Solvency II, bordereaux, management analytics |
| COBOL system (during transition) | Maintained in place, progressively drained | Strangler Fig; system of record until line of business migrated |
| Integration ACL | Anti-corruption layer on COBOL boundary | Prevents legacy data model leaking into new services |

**Migration pattern:** Strangler Fig, phased by line of business. The COBOL system retains policy-of-record status for each line until that line has been fully validated in Guidewire and reconciled with the data platform. No big-bang cutover. Each line migrates on a defined schedule with explicit rollback provisions.

---

## Decision

**Option C is adopted.**

Atlas will deploy Guidewire Cloud Suite (PolicyCenter, ClaimCenter, BillingCenter) as its core insurance platform, scoped to replace the COBOL policy admin system and the unsupported legacy claims package. Alongside the Guidewire programme, a cloud-native API and digital engagement layer will be built and delivered independently on a faster cadence.

The two streams are separately governed and separately contracted, with integration points defined upfront and owned by the CTO's office.

---

## Rationale

**Why Guidewire, not a custom core.** Insurance policy administration, rating, claims handling, and billing are well-understood domains with high regulatory specificity. Building this from scratch when a proven, insurer-adopted, Solvency-II-aware platform exists is not innovation — it is unnecessary risk within a constrained budget. Guidewire's Lloyd's Blueprint Two integration eliminates what would otherwise be a bespoke certification programme against a hard deadline.

**Why a separate digital layer, not Guidewire-only.** Guidewire is not designed to be a customer-facing digital experience platform or a high-velocity API development environment. Constraining Atlas's digital channel ambitions to what Guidewire ships in its standard portal would reproduce the current change-velocity problem with a more expensive vendor. The digital engagement layer needs to iterate at a rate Guidewire's release cadence cannot match.

**Why Strangler Fig, not cutover.** The COBOL system has an unblemished reliability record. The cost of a failed cutover — to the Solvency II return, to the PRA relationship, to Lloyd's obligations — exceeds any efficiency benefit from removing coexistence complexity. Lines of business migrate when Guidewire has been validated, not on a predetermined calendar date.

**Why this fits the budget.** The hybrid scoping is a deliberate budget discipline. Guidewire is scoped to replace two systems (COBOL policy admin, legacy claims). The digital engagement layer is cloud-native and built by a small team with modern tooling — not priced as a Guidewire implementation. The COBOL system is maintained in place throughout the transition, funded from the existing operating base, not the transformation budget.

**Why this addresses the six-month test.** The API gateway and Lloyd's Blueprint Two integration are discrete, time-bounded deliverables that can be contracted and delivered within six months independent of the Guidewire implementation timeline. This gives the CFO a visible, auditable, market-facing deliverable — Blueprint Two compliance — as the Phase 1 milestone.

**Why this supports workforce transition.** The COBOL team has a defined and meaningful role during the transition period: they own the Anti-Corruption Layer between the COBOL system and new services, they own the ETL from COBOL into the data platform, and they are the subject-matter experts for the Guidewire business rule migration. This is not a courtesy role — it is a genuine dependency. Progressive upskilling paths (Guidewire configuration, cloud integration tooling, data engineering) replace the COBOL specialism on a natural attrition timeline.

---

## Consequences

### Accepted benefits

- Lloyd's Blueprint Two compliance deliverable is scoped to a six-month horizon and is independent of Guidewire implementation progress.
- Claims-to-policy data reconciliation problem is structurally resolved by Guidewire's unified data model — eliminating the near-miss that triggered PRA enhanced supervision.
- Digital channel velocity is decoupled from the Guidewire implementation timeline.
- Workforce transition has a credible and dignified pathway.
- The COBOL system's reliability is preserved as a fallback throughout the transition.

### Accepted costs and risks

- **Dual-running cost.** Running COBOL and Guidewire in parallel during the migration period has a material cost in infrastructure, licensing, and integration maintenance. This is the known cost of the Strangler Fig pattern. It must be explicitly budgeted, not treated as residual.
- **Guidewire implementation risk.** Guidewire programmes are complex. Atlas must contract for a fixed-scope Phase 1 and resist scope creep. The programme should be structured so that Guidewire replaces one line of business (likely motor, the highest-volume, most standardised) before committing to the full migration.
- **Integration complexity.** The Anti-Corruption Layer between COBOL and new services will be more complex than initial estimates suggest. Atlas's integration inventory is incomplete ("around 120 integrations" per the brief). A verified integration inventory must be produced before the Guidewire programme enters delivery. This is a named pre-condition, not an assumption.
- **Vendor concentration.** Guidewire becomes a critical third party for Atlas's most important business services. Under SS1/21, this requires documented impact tolerance analysis for Guidewire availability scenarios, and Guidewire's contractual SLAs must be mapped to Atlas's impact tolerances — not compared to generic availability percentages.

---

## Regulatory Implications

**PRA SS1/21 — Operational Resilience**

Policy issuance, claims payment, and Solvency II reporting are important business services under SS1/21. Migration of these services requires:

- End-to-end service maps for each service prior to migration commencement.
- Impact tolerance analysis for each disruption scenario during the Strangler Fig transition, not only in the target state.
- Explicit board sign-off on the impact tolerances applying to the Guidewire-hosted environment, including Guidewire as a critical third party.
- Testing scenarios that cover mid-migration states — the period when some policies are in COBOL and some are in Guidewire simultaneously is the highest-risk resilience window.

No line of business migrates to Guidewire until its service map has been approved by the CRO and the impact tolerance for the mid-migration state has been documented and tested.

**Lloyd's Blueprint Two**

The API gateway and Lloyd's integration stream must be confirmed as Blueprint Two-certified by Lloyd's Market Association before the six-month Phase 1 milestone is claimed. Guidewire's Lloyd's connector certifications must be verified against the current Blueprint Two technical specification at contract signature, not assumed from vendor marketing.

**Solvency II**

The data platform and regulatory reporting pipeline are explicitly out of scope for Phase 1. They migrate only after the COBOL-to-data-platform ETL has been validated against at least two consecutive Solvency II quarterly returns. The CRO holds sign-off authority on this gate.

---

## Open Questions

1. Which line of business is confirmed for Phase 1 Guidewire migration? (Motor is the working assumption; needs validation from the business.)
2. What is the verified Lloyd's Blueprint Two compliance deadline? Atlas must confirm the current date from Lloyd's Market Association directly — the brief references the published timeline but not the specific date.
3. Cloud provider decision (CTO preference: AWS; CISO preference: Azure) remains unresolved. This affects the API gateway technology choice and the data platform design. A decision is required before digital layer architecture commences — not before Guidewire contract signature, as Guidewire is cloud-hosted by the vendor.
4. Has the integration inventory been enumerated? The "approximately 120 integrations" estimate in the brief is not a planning basis. The Anti-Corruption Layer scope depends on this.

---

*Prepared by: Atlas Insurance Architecture Advisor*  
*Engagement week: 1*  
*Next review: On Guidewire vendor selection decision*
