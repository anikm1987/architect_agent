# Atlas Insurance — Week 1 Top Risks for the Architecture Team

> **Audience:** Architecture team, CTO (David Chen)
> **Prepared by:** Architecture Advisor
> **Date:** 2026-04-29
> **Status:** Draft — awaiting Critic review

---

## Context

Three risks that the architecture team must own from day one — before any design choices are made. Each has the potential to derail the programme, and each is grounded in Atlas's specific circumstances rather than generic transformation risk.

---

## Risk 1: Undocumented COBOL Complexity Makes the Programme Unscopeable

**The risk.** Atlas's core policy administration system carries 25+ years of undocumented customisation. The only authoritative knowledge of what the system actually does resides in the heads of a small, long-tenured team — average tenure 18 years. This is not merely a technical debt issue; it is a fundamental programme-scoping failure waiting to happen. You cannot price a migration, design an anti-corruption layer, or commit to a delivery timeline against a system whose true complexity is unknown.

**Why it matters in week one.** The CFO has set a six-month kill-threshold. Phase 1 commitments will be made in the next few weeks. Any commitments made before the COBOL estate is characterised are commitments made on false premises. COBOL customisation complexity is the single most common cause of transformation cost overruns in the UK insurance sector — firms consistently underestimate it by 40–60%.

**The compounding factor.** The knowledge holders are also the union-represented workforce whose future is uncertain. If any of them leave — through retirement, voluntary redundancy, or simply loss of morale — the programme loses irreplaceable capability that is not recoverable. The architecture team must treat knowledge externalisation from this cohort as a programme-critical workstream, not an HR concern.

**Immediate action needed.** Commission a COBOL discovery sprint in week two: structured domain-modelling workshops with the core development team to produce a written capability inventory, integration map, and customisation register. This is prerequisite to every subsequent design decision.

---

## Risk 2: Lloyd's Blueprint Two Deadline Exposes a Zero-to-API Gap

**The risk.** Atlas has no API capability whatsoever. Every integration with brokers, reinsurers, and the Lloyd's market operates via batch file transfer or point-to-point EDI. Lloyd's Blueprint Two requires API-readiness by the Lloyd's Franchise Board's published timeline. The gap from Atlas's current state to Blueprint Two compliance is not an incremental improvement — it is the construction of an API estate from scratch.

**Why it matters in week one.** Blueprint Two is not a transformation ambition — it is a hard compliance deadline. It is not subject to negotiation or phasing. If Atlas's syndicate misses the Blueprint Two deadline, the commercial and regulatory consequences fall on the Lloyd's operation specifically, putting a profitable specialty lines book at direct risk. The architecture team must treat this as a binding constraint that shapes the sequencing of everything else, not as one work-stream among many.

**The sequencing trap.** The natural tendency will be to sequence the API layer as a later phase — "we'll modernise the core first, then build APIs." This is wrong for Atlas. The API gateway and foundational connectivity layer must be scoped and designed in Phase 1 precisely because Blueprint Two has a fixed external deadline. If the core modernisation slips, the API capability cannot slip with it.

**Immediate action needed.** Obtain the exact Lloyd's Blueprint Two timeline and requirements document in week one. Map the specific technical obligations against Atlas's current state. This produces the hard constraint that Phase 1 design must be built around.

---

## Risk 3: Solvency II Reporting Integrity Under SS1/21 — No Service Map, No Safe Migration

**The risk.** Atlas is already under enhanced PRA supervisory attention following a near-miss on the Q3 Solvency II return — a data reconciliation failure between claims and policy systems that nearly caused a material misstatement. Under PRA SS1/21, the Solvency II quarterly return is an important business service. The PRA has explicitly warned Atlas that transformation must not compromise the accuracy or timeliness of regulatory returns. The CRO holds a formal veto over any initiative that affects solvency reporting.

**The specific architectural gap.** As of week one, Atlas has no documented service map for the Solvency II reporting pipeline — the chain of dependencies from policy admin system through claims system through ETL batch through data warehouse to the regulatory return. SS1/21 requires firms to map the people, processes, technology, facilities, and third parties that deliver each important business service before making design decisions that affect it. Without this map, the architecture team cannot demonstrate it can remain within impact tolerance during migration, cannot identify which dependencies it is allowed to disturb, and cannot get CRO sign-off on any design.

**The consequence of getting this wrong.** A regulatory incident during the transformation programme — a Solvency II misstatement, a late return, or a material data quality failure — would not merely cause reputational damage. It would trigger formal PRA enforcement action on a firm already under enhanced supervisory attention, and it would give the CRO grounds to suspend the transformation programme entirely.

**Immediate action needed.** Initiate a service-mapping exercise for the Solvency II important business service before any migration design work begins on the policy admin or claims systems. This is non-negotiable under SS1/21 and is prerequisite to obtaining CRO sign-off on the transformation architecture.

---

## Relationship Between the Three Risks

These risks are not independent. Undocumented COBOL complexity (Risk 1) directly undermines the ability to produce an accurate service map for Solvency II (Risk 3). The absence of any API foundation (Risk 2) means Blueprint Two cannot be met without a dedicated Phase 1 track, which competes for budget and attention with the discovery and service-mapping work that Risks 1 and 3 require. All three converge on the same constraint: the programme cannot be designed rationally until the true shape of the existing estate is understood.

---

*Draft for Critic review. Not for client distribution.*
