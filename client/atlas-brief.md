# Atlas Insurance — Engagement Brief

> **For use by:** Atlas Insurance Architecture Advisor agent
> **Purpose:** Primary client-context file. Ground all recommendations in this brief.
> **Last updated:** Start of engagement, Week 1

---

## Client Profile

**Atlas Insurance** is a UK general insurer headquartered in Manchester, founded in 1987. Mid-sized by UK market standards — gross written premium of approximately £680M per annum across retail and commercial lines. Predominantly home and motor in retail; commercial property and liability in SME commercial. A small but profitable Lloyd's syndicate operation handles specialty lines.

The business is privately held (majority stake held by a family investment vehicle, with a minority PE position taken four years ago). Not listed. No immediate plans for IPO but the PE investor has a typical 5–7 year horizon, creating implicit pressure for transformation to deliver within the next two to three years.

## Current Technology Estate

Atlas's core policy administration system is a custom-built COBOL application running on IBM midrange (iSeries), with DB2 as the underlying database. The system has been in continuous production since 1998 with significant customisation over 25+ years. Every change request goes through a small team of long-tenured developers (average tenure: 18 years) and typically takes 4–6 months to deliver. The system is reliable — there has not been an unplanned outage exceeding 2 hours in over a decade — but it is slow to change and expensive to maintain.

Claims is handled in a separate, older package (a legacy on-premise solution from a vendor that was acquired twice and is now effectively unsupported). Claims data does not integrate cleanly with policy data; reconciliation is a manual quarterly exercise.

There is **no meaningful API layer**. Integrations with brokers, reinsurers, and the Lloyd's market happen via batch file transfer (mostly SFTP) and a handful of point-to-point EDI feeds. There is no service bus, no event stream, no API gateway.

The data warehouse is a Microsoft SQL Server environment that receives nightly ETL batches from the policy admin system and the claims system. It serves regulatory reporting (Solvency II, bordereaux) and management information. Data quality is adequate for regulatory purposes but frustrating for business users who want self-service analytics.

## The Mandate

The CEO — appointed eighteen months ago from a larger UK insurer — has made digital transformation the centrepiece of the three-year strategy. The board has approved a transformation budget of **£50M over three years**. The PE investor has made it clear that this is not additional capital; it comes out of the operating plan and is expected to produce measurable efficiency gains within the investment horizon.

The CEO's articulated ambition is for Atlas to be "a top-quartile digital insurer in our segment" by the end of the programme. She has referenced **Lemonade** and **Hastings** as examples she admires, though the executive team is divided on whether either is a realistic benchmark.

## Strategic Drivers

Several pressures are converging simultaneously:

**Customer expectations.** Atlas's direct-to-consumer home and motor quote-to-bind journey currently takes an average of 11 minutes online and requires manual underwriter review for any non-standard risk. Comparison-site aggregators drop out after 3 minutes of inactivity; Atlas's conversion rate on aggregator traffic is running 40% below industry median as a result.

**Regulatory pressure.** The PRA flagged a near-miss on Atlas's Q3 Solvency II return last year — a data reconciliation issue between claims and policy systems nearly caused a material misstatement. Atlas is now under enhanced supervisory attention, and the PRA has explicitly warned that any transformation programme must not compromise the accuracy or timeliness of regulatory returns. **PRA SS1/21 on operational resilience** applies in full.

**Lloyd's Blueprint Two.** Atlas's syndicate is required to be API-ready for the Blueprint Two market infrastructure changes on Lloyd's published timeline. Atlas currently has no API capability whatsoever. This is a hard deadline, not a transformation ambition.

**Workforce transition.** Roughly half of Atlas's 140-person IT organisation is made up of long-tenured mainframe and COBOL developers. They are highly loyal and highly skilled within their domain but have limited cloud-native experience. The Unite union has been actively raising questions about the transformation programme's implications for this cohort, and the HR Director has flagged workforce transition as a board-level risk.

**Shadow IT.** The Head of Claims, frustrated by an 18-month IT backlog for a basic claims dashboard, has procured a SaaS analytics tool directly on her departmental budget and connected it — without central oversight — to claims data extracts. The CISO is aware and furious; this has become a governance flashpoint.

## Key Stakeholders

| Role | Name | Posture |
|---|---|---|
| CEO | Sarah Hendricks | Sponsor. Wants visible progress. Impatient with detail. |
| CFO | Raj Patel | Gatekeeper. Wants phased investment, value in 6 months, kill-threshold if missed. |
| CTO | David Chen | Advocate. Understands the complexity. Politically constrained. |
| CRO | Helen Whitaker | Skeptic. Regulatory focus. Veto on anything that affects solvency reporting. |
| Head of Claims | Morag Sinclair | Frustrated. Has gone shadow IT. Needs to be brought back into the tent. |
| CISO | James Okafor | Alarmed at governance gaps. Supportive of transformation if it centralises control. |
| HR Director | Patricia Laing | Focused on workforce transition. Union concerns are live. |

## The Regulatory Perimeter

Atlas operates under **PRA** prudential supervision (capital, solvency, operational resilience) and **FCA** conduct supervision (Consumer Duty, product governance). The Lloyd's syndicate operation adds Lloyd's Franchise Board oversight and Blueprint Two compliance obligations. **DORA** (Digital Operational Resilience Act) technical standards apply through UK equivalence arrangements and are material for the transformation design. GDPR / UK-DPA apply throughout. **Solvency II** reporting is the primary quarterly regulatory rhythm and the source of the CRO's veto concerns.

## Explicit Constraints

- Transformation budget capped at £50M over three years — not adjustable upward.
- CFO has signalled a "value in six months" test for Phase 1. Failure to demonstrate measurable benefit by Month 6 triggers programme reset.
- 99.9% availability SLA on the core policy admin system must be maintained throughout migration.
- Solvency II quarterly returns cannot be disrupted under any circumstances.
- Lloyd's Blueprint Two API-readiness is a hard deadline — not negotiable.
- Workforce transition must be handled via upskilling and natural attrition, not compulsory redundancy.

## Known Unknowns

- The exact depth of customisation inside the COBOL policy admin system is not documented. The developers who built it are still employed; the only knowledge exists in their heads.
- Integration inventory is incomplete. The best internal estimate is "around 120 integrations" but nobody has produced a verified list.
- Cloud provider strategy is unresolved. The CTO prefers AWS; the CISO is more comfortable with Azure for compliance reasons; no decision has been taken.
