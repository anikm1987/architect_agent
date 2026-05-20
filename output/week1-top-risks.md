# Atlas Insurance — Week 1 Top Risks for the Architecture Team

> **Audience:** Architecture team, CTO (David Chen)
> **Prepared by:** Architecture Advisor
> **Date:** 2026-04-29

---

## Context

Three risks the architecture team must own from day one — before any design choices are made. Each is grounded in Atlas's specific circumstances. Each carries the potential to derail the programme or trigger a regulatory incident.

---

## Risk 1: Undocumented COBOL Complexity Makes the Programme Unscopeable

**The risk.** Atlas's core policy administration system carries 25+ years of customisation with no documentation. The only authoritative knowledge of what the system actually does resides in the heads of a small, long-tenured development team. This is not merely technical debt. It is a fundamental programme-scoping gap. You cannot price a migration, design an anti-corruption layer, or commit to a delivery timeline against a system whose true complexity is unknown.

**Why it matters now.** The CFO has set a six-month kill-threshold. Phase 1 commitments will be made in the coming weeks. Any commitments made before the COBOL estate is characterised are commitments made on false premises. Scope surprises in legacy customisation are the most common cause of transformation overruns in UK insurance modernisation programmes.

**The compounding factor.** The knowledge holders are also the workforce cohort whose future is most uncertain. Knowledge externalisation must be framed — to them, to their union, and to HR — as role enrichment and programme contribution, not as documentation preceding redundancy. Patricia Laing and Unite will be watching how this workstream is positioned. If it is perceived as role documentation, it will be resisted at exactly the moment the programme is most dependent on co-operation.

**Immediate action.** Commission a discovery sprint in week two: structured domain-modelling workshops with the core development team to produce a written capability inventory, integration map, and customisation register. The engagement HR lead must be involved in scoping the workforce-relations framing before the first workshop is convened.

**A note on CTO positioning.** David Chen is an advocate for this programme but operates under political constraints. Risk 1 is implicitly a critique of the estate his organisation is responsible for. The architecture team should brief him privately on this risk first, and give him the framing he needs to carry it to the CEO without it landing as institutional failure. The discovery sprint should be positioned as diligence that strengthens the programme's credibility, not an exposure of past neglect.

---

## Risk 2: Lloyd's Blueprint Two Is a Hard Deadline Against a Zero-to-API Estate

**The risk.** Atlas has no API capability whatsoever — every integration operates via SFTP batch or point-to-point EDI. Lloyd's Blueprint Two requires API-readiness by a timeline set by the Lloyd's Franchise Board. The gap from Atlas's current state to compliance is not an incremental improvement. It is the construction of an API estate from scratch, against an external deadline that cannot be negotiated.

**Why it matters now.** Blueprint Two is a compliance obligation, not a transformation ambition. If the syndicate operation misses the Lloyd's deadline, Atlas puts a profitable specialty lines book at direct regulatory and commercial risk. The architecture team must treat Blueprint Two's deadline as the binding constraint that dictates Phase 1 sequencing — not as a parallel workstream to be addressed once the core modernisation is underway.

**The sequencing trap to avoid.** The natural instinct will be to build the API layer after the core is modernised. For Atlas, this sequence is wrong. The API gateway and foundational connectivity layer must be scoped in Phase 1 precisely because Blueprint Two has an immovable external deadline. The gap analysis — Atlas's current capability against the specific Blueprint Two technical requirements — must be produced in week one, using Lloyd's published specification, and presented to the CTO as the constraint that shapes Phase 1 design.

**Immediate action.** Assign an architect to produce the Blueprint Two gap analysis against Lloyd's published timeline and specification in week one. The output frames the non-negotiable scope that Phase 1 must contain.

---

## Risk 3: Solvency II Reporting Integrity — No Service Map, Active Data Integrity Threat, and DORA Exposure

**The risk.** Atlas is already under enhanced PRA supervisory attention following a near-miss on the Q3 Solvency II return — a data reconciliation failure between the claims and policy systems that nearly caused a material misstatement. Under PRA SS1/21, the Solvency II quarterly return is an important business service. The CRO holds a formal veto over any initiative affecting solvency reporting. The PRA has explicitly warned that transformation must not compromise the accuracy or timeliness of regulatory returns.

**The specific architectural gap.** Atlas has no documented service map for the Solvency II reporting pipeline — the end-to-end chain from policy admin through claims system, ETL batch, and data warehouse to the return itself. SS1/21 requires firms to map the people, processes, technology, facilities, and third parties that deliver each important business service before making design decisions that affect it. Without this map, the architecture team cannot demonstrate it can remain within impact tolerance during migration, cannot identify which dependencies it is permissible to disturb, and cannot obtain CRO sign-off.

**The shadow IT dimension.** The Head of Claims has connected an unsanctioned SaaS analytics tool to claims data extracts without central oversight. Claims data is a direct input to the Solvency II reporting pipeline. This is not a governance irritant — it is a live data integrity threat to the exact chain this risk is built around. The CISO is alarmed. The CRO is unaware of the full picture. The architecture team must escalate this to the CRO as part of the service-mapping exercise. Treating it as a separate governance matter leaves an active risk unresolved in the most sensitive part of the estate.

**DORA is a joint constraint here.** Atlas's reinsurance arrangements almost certainly create EU third-party dependencies. DORA (Digital Operational Resilience Act) applies through UK equivalence arrangements and its technical standards run in parallel with SS1/21 — particularly around ICT third-party risk and incident reporting. The PRA is likely to ask about DORA posture during any supervisory conversation about the transformation programme, given Atlas's current enhanced attention status. Architecture decisions affecting the regulatory reporting pipeline must be assessed against both frameworks, not SS1/21 alone.

**Immediate action.** Initiate a service-mapping exercise for the Solvency II important business service before any migration design work begins on the policy admin or claims systems. This exercise must explicitly scope the shadow IT exposure and produce a written account of current data integrity controls for CRO review.

---

## The Budget Constraint These Risks Create

The three risks above imply four concurrent pre-design workstreams before a single line of target-state architecture is committed: COBOL discovery, Blueprint Two gap analysis, Solvency II service mapping, and shadow IT remediation. This work is not optional — it is prerequisite to responsible design. But it consumes Phase 1 capacity, delays visible output, and creates exposure to the CFO's six-month kill-threshold.

The architecture team must surface this tension explicitly to the CTO and CEO in week one. The question is not whether this work is necessary — it is — but whether the current programme structure and budget allocation has accounted for it. If Phase 1 is currently scoped as "start building," it needs to be rescoped as "characterise first, then build." That is a board-level conversation, and it is better to have it in week one than at month four.

---

*Reviewed by Critic agent prior to delivery.*
