# ADR-001: Core Platform Architecture — Atlas Insurance Digital Transformation

**Status:** Proposed  
**Date:** 2026-04-29  
**Decision-makers:** CTO (David Chen) — primary; CEO (Sarah Hendricks) — programme sponsor  
**Mandatory approvers before board ratification:** CRO (Helen Whitaker) — Solvency II gate; CFO (Raj Patel) — budget gate  
**FCA conduct owner for digital engagement layer:** To be confirmed by CEO; recommended nominee is Chief Customer Officer or equivalent  
**Review trigger:** Guidewire contract signature; material change to Lloyd's Blueprint Two timeline; Phase 1 go/no-go gate at month 6

---

## Context

Atlas Insurance operates a core policy administration system built on IBM iSeries/COBOL, in continuous production since 1998 with no documented API surface. Claims is handled by an unsupported legacy package — acquired twice and now effectively end-of-life — with no clean integration to policy data. No service bus, no event stream, and no API gateway exist anywhere in the estate.

Three non-negotiable pressures define the transformation timeline:

1. **Lloyd's Blueprint Two.** Atlas's syndicate must be API-ready by Lloyd's published deadline. Atlas currently has zero API capability. This is a contractual market obligation.
2. **PRA enhanced supervisory attention.** Last year's near-miss on the Solvency II Q3 return — caused by manual claims-to-policy reconciliation — placed Atlas under heightened PRA scrutiny. The CRO has board-level veto on any programme decision that could affect the accuracy or timeliness of regulatory returns.
3. **PE investor horizon.** The majority investor's 5–7 year position is now approximately four years in. The transformation must produce auditable value before the exit window opens. The CFO has imposed a six-month value demonstration test for Phase 1; failure triggers a programme reset.

The transformation budget is £50M over three years. It is not adjustable upward. The core system's 99.9% availability SLA must be maintained throughout. Solvency II quarterly returns cannot be disrupted under any circumstances.

---

## Decision Drivers

| Driver | Weight | Rationale |
|---|---|---|
| Lloyd's Blueprint Two compliance | Must-have | Hard contractual deadline; non-compliance affects syndicate licence |
| Solvency II continuity | Must-have | PRA enhanced supervision; CRO holds board veto |
| 99.9% core availability during migration | Must-have | Explicit programme constraint; SS1/21 impact tolerance applies |
| CFO 6-month value test — business metric, not plumbing | High | Blueprint Two API delivery alone will not satisfy Raj Patel; a quantifiable business benefit is required |
| £50M budget — all-in, no upward flexibility | High | Includes licensing, implementation, dual-run, integration, programme management, and contingency |
| Workforce transition via upskilling and natural attrition | High | Union concerns are live; compulsory redundancy is off the table |
| Digital channel velocity | Medium | Aggregator conversion rate is 40% below industry median; CEO benchmark is Hastings |

---

## Options Considered

### Option A — Guidewire Cloud Suite (Full Replacement, Single Programme)

Deploy Guidewire PolicyCenter, ClaimCenter, and BillingCenter as an integrated hosted suite, replacing both the COBOL system and the legacy claims package on a single Guidewire-led programme timeline.

**What it offers:** Pre-built insurance domain logic covering home, motor, commercial property, and liability. Cloud-hosted — Guidewire carries infrastructure resilience obligations. Unified data model that structurally resolves the claims-to-policy reconciliation problem. Published connectors for Lloyd's Blueprint Two and broker market APIs.

**Why it is insufficient as a standalone choice:**

A Guidewire full-suite implementation for a firm of Atlas's scale and product complexity — three products, four lines of business, plus Lloyd's syndicate — carries a market-rate professional services spend of £18–28M before licensing, data migration, broker/reinsurer re-integration, and the testing programme the CRO will require for Solvency II sign-off. At that spend level, licensing and programme management absorb the majority of the £50M ceiling before the digital channel or data platform have been funded.

More materially, a single large Guidewire programme exposes Atlas to programme-level concentration risk. Guidewire implementations are complex and routinely slip. A 12-month programme slip on a Guidewire-only model means missing both the CFO's six-month test and the Lloyd's deadline within the same programme, with no independent delivery mechanism to recover either.

Note on Blueprint Two connectors: Guidewire publishes Blueprint Two integration capabilities, but these are subject to version and certification verification at contract signature. They are a significant selection factor, not a resolved delivery guarantee.

**Verdict:** Guidewire is the right core engine. The programme structure around it must be designed by Atlas, not inherited from Guidewire's default delivery model.

---

### Option B — Composable Microservices (Greenfield Build)

Design and build a cloud-native, microservices-based policy administration, claims, and billing platform from scratch using domain-driven decomposition.

**Why this is not viable:**

Achieving feature parity with Atlas's COBOL estate — covering four lines of business with 25+ years of accumulated underwriting logic — via a greenfield microservices build represents a minimum of five to eight years of sustained engineering effort at a cost well above the £50M ceiling. The PE investor's exit horizon and the Lloyd's hard deadline are irreconcilable with this timeline.

Atlas has no cloud-native engineering capability today. Building it requires a separate hiring and capability programme running in parallel with maintaining the COBOL system — two teams, one budget. The COBOL team cannot design microservices. A cloud-native team hired externally cannot maintain the COBOL system. The budget cannot support both.

There is no credible six-month deliverable from a greenfield programme. At six months the output is infrastructure scaffolding and initial domain models — nothing Raj Patel would recognise as value.

**Verdict:** Rejected. The constraints are jointly sufficient to rule this out. Recommending it would be pattern-first thinking disconnected from Atlas's actual circumstances.

---

### Option C — Structured Hybrid: Guidewire Core + Purpose-Built Engagement Layer (Recommended)

Adopt Guidewire Cloud Suite as the authoritative system of record for policy administration, claims, and billing — scoped conservatively — and build a thin, cloud-native digital engagement and API integration layer as a separately governed, separately contracted parallel programme.

**Architecture decomposition:**

| Layer | Approach | Rationale |
|---|---|---|
| Core policy, claims, billing | Guidewire Cloud Suite (vendor-hosted) | Insurance domain logic; unified data model; Lloyd's connectivity |
| API gateway and Lloyd's integration | Cloud-native (AWS API Gateway or Azure APIM + bespoke adapters) | Blueprint Two compliance; broker and reinsurer connectivity; independent of Guidewire timeline |
| Digital customer channels | Cloud-native microservices | Quote-to-bind journey; aggregator integration; self-service portal |
| Data and regulatory reporting | Cloud data platform (independent of Guidewire) | Solvency II, bordereaux, management MI; replaces SQL Server ETL estate |
| COBOL system (during transition) | Maintained in place; progressively drained by line of business | Strangler Fig — system of record for each line until Guidewire-validated |
| Integration boundary | Anti-corruption layer on the COBOL perimeter | Prevents legacy data model from leaking into new services |

**Migration pattern:** Strangler Fig, sequenced by line of business. The COBOL system retains policy-of-record status for each line until that line has been fully validated in Guidewire, reconciled against the data platform, and signed off by the CRO. No cutover event whose worst-case duration exceeds the impact tolerance for any important business service. Each line migrates on a defined schedule with explicit, tested rollback provisions.

---

## Decision

**Option C is adopted.**

Atlas will deploy Guidewire Cloud Suite (PolicyCenter, ClaimCenter, BillingCenter) as its core insurance platform, replacing the COBOL policy administration system and the unsupported legacy claims package via a phased Strangler Fig migration. A cloud-native API and digital engagement layer will be delivered as an independent programme on a faster cadence, with the Blueprint Two API integration as its Phase 1 deliverable.

The two streams are separately governed, separately contracted, and have defined integration points owned by the CTO's office. Neither stream gates the other for Phase 1.

---

## Rationale

**Why Guidewire, not a custom core.** Policy administration, rating, claims, and billing are well-understood insurance domains with high regulatory specificity. Building these from scratch within a £50M budget and a three-year horizon, on a team with no cloud-native experience, is not innovation — it is the highest-risk path. Guidewire's unified data model structurally resolves the claims-to-policy reconciliation problem that triggered PRA enhanced supervision. That is a direct regulatory response, not merely a technology preference.

**Why a separate digital layer.** Guidewire is not a high-velocity digital experience or API development platform. Tying Atlas's customer channel ambitions to Guidewire's release cadence would reproduce the current change-velocity problem at higher licensing cost. The digital engagement layer must iterate at a rate Guidewire cannot match. Separating it also means the Blueprint Two API deliverable is independent of Guidewire implementation progress — a critical separation given the Lloyd's hard deadline.

**Why Strangler Fig, not cutover.** The COBOL system has an unblemished reliability record. The SS1/21 impact tolerance for policy issuance and Solvency II reporting is non-negotiable. No migration approach that requires a single-event cutover can demonstrably remain within impact tolerance for both services simultaneously. Lines of business migrate when Guidewire has been validated and the CRO has signed off — not on a calendar date.

**Why this is financeable.** Guidewire is scoped to Phase 1 as a single line of business (motor — highest volume, most standardised). The digital engagement layer is a small cloud-native team, not a Guidewire-scale programme. The COBOL system's dual-run cost is largely existing operating expenditure, not additional transformation spend. The data platform is Phase 2/3.

---

## Budget Allocation Model

The following allocation is a planning basis only; it does not constitute a contracted commitment. It is presented to demonstrate the four workstreams are collectively within the £50M ceiling when appropriately scoped, and to surface the trade-offs that tighter scoping requires.

| Workstream | Phase 1 (£M) | Phase 2–3 (£M) | Total (£M) |
|---|---|---|---|
| Guidewire: licensing + implementation (motor Phase 1, then further LoB phases) | 8 | 12 | 20 |
| Digital engagement layer + API gateway (Blueprint Two, quote-to-bind, broker portal) | 3 | 4 | 7 |
| Data platform + regulatory reporting migration | — | 7 | 7 |
| Integration ACL, COBOL dual-run operating cost delta, programme mgmt | 2 | 3 | 5 |
| Architecture, security, change management, workforce transition support | 2 | 2 | 4 |
| **Contingency (15%)** | — | — | **7** |
| **Total** | **15** | **28** | **50** |

**Budget risk flags:**
- Guidewire scope creep is the primary budget threat. Phase 1 must be contracted on a fixed-scope basis with a defined line of business boundary. Extension to additional lines of business requires a separate change control gate, approved by the CFO.
- The contingency of £7M is materially thin for a programme of this complexity. If the integration inventory (currently estimated at "approximately 120" — not verified) proves significantly larger, contingency will be consumed by integration work alone. Verified integration inventory is a pre-condition for Phase 1 contracting, not a Phase 1 deliverable.
- The data platform is intentionally deferred to Phase 2/3. The CRO must accept that the SQL Server ETL pipeline — with its known data quality issues — remains the Solvency II reporting path through Phase 1. This is a managed risk, not an oversight.

---

## Phase 1 Success Criteria and Decision Gates

The CFO's six-month test requires a business-recognisable metric. Blueprint Two API connectivity alone is a technical deliverable; it is not the test.

**Phase 1 gate (Month 6) — all three criteria must be met:**

1. **Lloyd's Blueprint Two compliance certified.** Atlas's syndicate operation passes Blueprint Two certification on Lloyd's published schedule. Verified by Lloyd's Market Association sign-off, not Atlas's internal assertion.
2. **Claims-to-policy reconciliation effort reduction.** The quarterly manual reconciliation process — currently a known Solvency II data integrity risk — is replaced or materially reduced by data integration between the legacy claims system and the policy data estate via the ACL. Target: manual reconciliation effort reduced by at least 80%, documented and signed off by the CRO. This is a direct regulatory risk reduction that Raj Patel can describe to the board.
3. **Guidewire Phase 1 contract executed and implementation underway.** Motor line of business implementation in active delivery with a committed milestone plan. Not "in procurement."

**If Phase 1 gate is not met in full:** CFO holds programme reset authority. The ADR does not pre-authorise continuation. Any continuation beyond Month 6 without full gate sign-off requires a board decision, not a CTO decision.

**Programme continuation gate (Month 18):** Motor line fully live in Guidewire, COBOL motor policies decommissioned, first Solvency II return produced from Guidewire data without manual reconciliation, and CRO sign-off obtained. This gate controls access to Phase 2 budget.

---

## Regulatory Implications

### PRA SS1/21 — Operational Resilience

Policy issuance, claims payment, and Solvency II reporting are important business services under SS1/21. The following conditions are pre-conditions, not assumptions:

- End-to-end service maps for each important business service must exist before migration of that service commences. This work is the CTO's responsibility and the CRO's sign-off condition.
- Impact tolerance analysis must cover mid-migration states — the window during which some policies are in COBOL and some are in Guidewire is the highest-risk resilience window in the programme.
- Guidewire becomes a critical third party for Atlas's most important business services. Guidewire's contractual SLAs must be mapped to Atlas's SS1/21 impact tolerances at contract signature. A 99.9% uptime SLA is not equivalent to a defined impact tolerance; the mapping is Atlas's obligation, not Guidewire's.
- Board sign-off on the revised impact tolerances — reflecting the Guidewire-hosted environment — is required before the motor line of business goes live in Guidewire.

### DORA — ICT Third-Party Risk

Guidewire Cloud Suite, as a hosted platform for important business services, is a critical ICT third-party provider under DORA Article 28 and the associated Regulatory Technical Standards. This imposes specific contractual obligations that must be addressed at contract signature, not post-implementation:

- **Audit rights.** The contract must include Atlas's right to audit Guidewire's operational and security practices, either directly or via a third-party audit mechanism.
- **Exit provisions.** The contract must include data portability, migration assistance obligations, and a defined exit pathway. Vendor lock-in that leaves Atlas unable to exit within a defined timeframe is a DORA non-compliance, not merely a commercial risk.
- **Sub-contracting visibility.** Guidewire's use of sub-contractors for the hosted environment must be disclosed and subject to Atlas's approval for material changes.
- **Concentration risk.** Atlas must assess whether Guidewire, in combination with its cloud infrastructure provider (which Atlas must also identify), creates an ICT concentration risk requiring regulatory disclosure.

DORA obligations apply through UK equivalence arrangements. The CISO (James Okafor) should own the DORA ICT third-party risk register, with the CTO responsible for ensuring contractual obligations are met.

### Claims Data Governance and the Shadow IT Incident

The Head of Claims (Morag Sinclair) has procured a SaaS analytics tool connected to claims data extracts outside central governance. This creates an active Solvency II data integrity risk: claims data — which feeds the quarterly return — is now flowing through an uncontrolled external system.

This must be resolved before the Guidewire ClaimCenter migration commences. The recommended path:

1. The CISO formalises the claims data governance boundary: all claims data flows must be inventoried and approved before Phase 1 is complete.
2. Morag Sinclair is formally enrolled in the programme as the claims domain sponsor — not managed around. Her shadow IT reflects an unmet business need; the digital engagement layer should address that need within the governed architecture.
3. The ACL on the claims side has a named owner: the CTO. The data governance policy covering that boundary is owned by the CISO. These are not the same role and the distinction must be explicit in the programme governance structure.

### FCA Consumer Duty

The cloud-native digital engagement layer — specifically the quote-to-bind journey, product presentation, and customer-facing self-service portal — is a direct FCA Consumer Duty exposure. Conduct risk is not covered by the CRO's solvency remit. A named FCA conduct owner for the digital engagement layer must be confirmed before that stream enters delivery. If Atlas does not have a Chief Customer Officer or equivalent, the CEO should name an interim owner. Absence of a named owner is a governance gap the FCA could observe.

### Solvency II

The data platform and Solvency II reporting pipeline are Phase 2/3. The current SQL Server ETL pipeline from COBOL — despite its known data quality issues — remains the regulatory reporting path through Phase 1. The CRO must formally accept this as a managed risk on the Phase 1 risk register. It is not acceptable for this to be an implicit assumption.

---

## Consequences

### Accepted benefits

- Lloyd's Blueprint Two compliance is deliverable on an independent track, not gated by Guidewire implementation progress.
- The claims-to-policy reconciliation problem — the source of last year's Solvency II near-miss — is structurally resolved by Guidewire's unified data model.
- Digital channel velocity is decoupled from core platform migration.
- The workforce transition has a credible, non-redundancy pathway: the COBOL team owns the ACL, integration work, and business rule elicitation for Guidewire — a genuine dependency, not a courtesy role.
- The Strangler Fig pattern preserves the COBOL system's reliability as a fallback throughout transition.

### Accepted costs and risks

- **Dual-running cost.** Running COBOL and Guidewire in parallel is materially more expensive than either alone. The budget allocation above treats the COBOL operating cost delta — not the full existing cost — as programme budget. This assumption must be validated with the CFO before Phase 1 contracting.
- **Guidewire programme risk.** Phase 1 must be contracted as a fixed-scope engagement for the motor line of business. Any Guidewire vendor proposal that bundles all lines of business into a single programme should be rejected; it reintroduces the concentration risk that the hybrid model is designed to avoid.
- **Integration inventory.** The ACL scope depends on a verified integration count. The current estimate of "approximately 120 integrations" is not a planning basis. The integration inventory must be produced — by the COBOL team, who know where the feeds are — before Phase 1 contracting. This is the single highest-probability source of budget overrun.
- **Cloud provider decision.** The CTO prefers AWS; the CISO prefers Azure. This decision gates the API gateway technology choice and affects the DORA concentration risk assessment. A decision is required before the digital engagement layer enters design — it does not gate Guidewire contract signature, as Guidewire is vendor-hosted.

---

## Open Questions — Required Before Phase 1 Contracting

1. **Lloyd's Blueprint Two deadline.** Atlas must obtain the current certified compliance date directly from Lloyd's Market Association. The brief references the published timeline but not the specific date. The Phase 1 gate cannot be set without it.
2. **Integration inventory.** A verified count and classification of the approximately 120 broker, reinsurer, Lloyd's, and internal integrations currently on SFTP/EDI. Owner: COBOL team lead, sponsored by CTO. Due: prior to issuing Guidewire RFP.
3. **Cloud provider decision.** AWS vs Azure for the digital engagement layer and data platform. Owner: CTO with CISO input. Due: prior to digital engagement layer architecture sprint.
4. **Motor line validation.** Confirmation that motor is the correct Phase 1 Guidewire line of business. Must include commercial sign-off from the motor underwriting lead. Working assumption only until validated.
5. **Guidewire Blueprint Two connector version.** Verification at RFP stage that the current Guidewire Cloud Suite version includes certified Blueprint Two connectors at the specification version Lloyd's requires. This is a commercial contract condition, not an assumption.
6. **FCA conduct owner for digital layer.** Named individual with conduct accountability for the customer-facing digital channels. Due: before digital engagement layer design sprint commences.

---

*Prepared by: Atlas Insurance Architecture Advisor*  
*Engagement week: 1*  
*Critic review completed: 2026-04-29*  
*Next milestone: CRO and CFO pre-read before board ratification*
