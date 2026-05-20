---
name: Atlas Insurance — Engagement Context for Critic
description: Key facts, stakeholder dynamics, and known gaps for the Atlas Insurance transformation engagement, as seen by the Critic agent
type: project
---

Atlas Insurance: UK GI insurer, £680M GWP, Manchester. Privately held with PE minority stake (5-7yr horizon). PRA/FCA dual-regulated. Lloyd's syndicate adds Franchise Board oversight.

Core risk: COBOL policy admin on iSeries, 25+ years undocumented customisation, knowledge held by long-tenured developers (avg 18yr tenure), Unite union representation active.

Hard constraints: £50M fixed budget over 3 years. CFO 6-month kill-threshold. 99.9% uptime SLA on core system. Solvency II returns non-negotiable. Blueprint Two API-readiness hard deadline.

**Why:** PE investor horizon and CEO ambition are in tension with the genuine complexity of the estate and the regulatory perimeter. The programme risks being mis-priced before it starts.

**How to apply:** Always stress-test budget and timeline claims against the concurrent workstream load. Never let "constraint" go unchallenged if no one has modelled what it actually means.

## Stakeholder dynamics to remember

- CTO David Chen: advocate but "politically constrained" — nature of constraint unspecified, treat as a live risk to escalation paths
- CRO Helen Whitaker: formal veto on anything touching solvency reporting — must be satisfied before architecture team can commit
- Head of Claims Morag Sinclair: shadow IT already live (unsanctioned SaaS on claims data) — live data integrity risk, not just governance irritant
- CISO James Okafor: alarmed, supportive if transformation centralises control
- HR Director Patricia Laing + Unite union: workforce transition is board-level risk; any knowledge-extraction workstream risks being read as pre-redundancy documentation
- CFO Raj Patel: phased investment gatekeeper, impatient, will kill programme at 6 months if no measurable value

## Regulatory perimeter

PRA SS1/21 operational resilience — service mapping prerequisite to migration. DORA named as material in the brief but frequently overlooked in drafts. FCA Consumer Duty. Lloyd's Blueprint Two (hard deadline, not phased).

## First review — week-one risk briefing (2026-04-29)

Critical gaps found:
1. £50M budget never stress-tested against four concurrent heavyweight workstreams
2. DORA entirely absent despite being named as material in the brief
3. Shadow IT incident (Sinclair/claims data) not connected to Solvency II data integrity risk

Weaknesses:
- Blueprint Two deadline cited but not scoped — "go find it in week one" is weak
- 40-60% underestimation figure unsourced

Political blind spots:
- CTO's political constraint not acknowledged when delivering risks he owns
- COBOL discovery sprint proposed without any workforce-relations framing (Unite risk)

## Second review — ADR-001 core platform architecture (2026-04-29)

Critical gaps found:
1. DORA absent again — Guidewire Cloud Suite is a critical third-party ICT provider under DORA Art.28; RTS requirements (audit rights, exit provisions, concentration risk) not addressed
2. Budget arithmetic not demonstrated — four concurrent cost centres (Guidewire, cloud-native build, dual-run COBOL, deferred data platform) with no allocation or contingency model
3. Morag Sinclair / shadow IT entirely absent — claims data estate governance during transition unaddressed; ACL on claims side unowned

Missing:
- FCA Consumer Duty: digital engagement layer is a direct conduct risk surface; no named owner for the FCA relationship; CRO veto covers solvency only

Weak reasoning:
- Guidewire Blueprint Two connector cited in rationale as resolved, but Open Questions section concedes it needs verifying at contract signature — internal contradiction

Kill scenario:
- Guidewire Phase 1 (motor) slips to 18 months; CFO's 6-month test met only by Blueprint Two plumbing (no revenue/efficiency metric); CFO kills programme; Atlas left with stranded Guidewire licensing and a frozen Strangler Fig

Political blind spot:
- Helen Whitaker (CRO) not listed as a named decider despite holding explicit veto; if she exercises it in the board ratification room rather than before it, the ADR is dead on arrival

## Third review — target-state component architecture diagram + design rationale (2026-04-29)

Critical gaps found:
1. Morag Sinclair's shadow IT SaaS tool is absent from the diagram entirely. It is a live data flow touching claims data — the same data the ACL is supposed to govern. The diagram shows the ACL owns the COBOL/legacy claims boundary, but there is an undrawn arrow from LCLM to an external SaaS system that the CISO already knows about. That is not a footnote; it is a current-state risk the architecture must actively close.
2. The event stream (Kafka/EventBridge) has no disaster recovery or failover representation. Rationale #4 claims the event stream provides SS1/21 replayability — but the diagram shows a single EVT node with no redundancy, no dead-letter queue, no fallback path. If EVT fails during migration, both the ACL→Guidewire path and the data platform feed are severed simultaneously. That is exactly the mid-migration resilience window the ADR flags as highest risk.
3. There is no regulatory reporting flow from MIRPT to anyone except REG. Management MI goes to the regulator but not to the board, CFO, or CRO. In a firm under PRA enhanced supervision, MI flows to internal governance consumers before they go anywhere near the regulator. The diagram inverts the governance chain.

Missing:
- GDPR/UK-DPA data flow boundary. The diagram carries personal data (customer portal, quote-to-bind, claims) across cloud-native services with no data residency or transfer indication. Price comparison websites (AGG) send Atlas personal data. Where does it land? Which component is the data processor? This is absent.
- The Guidewire-to-ACL reverse path. The diagram shows ACL→EVT but no path from Guidewire back to COBOL for mid-migration reconciliation. During Strangler Fig, some policies are in Guidewire, some in COBOL. How does COBOL know which policies it still owns? That coordination flow is not drawn.

Weak reasoning:
- Rationale #5 says the COBOL team owns the ACL as their "upskilling pathway." That is a workforce transition argument dressed as an architectural rationale. The ACL is a critical integration component. Ownership should be assigned based on capability and accountability, not as a managed redundancy programme. If the COBOL team's knowledge is genuinely required (and it is), say that — but do not conflate it with career transition.

Kill scenario:
- COBOL developer attrition during Phase 1. Average tenure is 18 years; these are retirement-age staff. If two or three key COBOL developers leave before the integration inventory is complete and the ACL translation logic is documented, the ACL cannot be built accurately. The Strangler Fig stalls. Motor migration stops. The CFO's 18-month gate is missed. The programme is reset — but now Atlas has Guidewire licensing cost running with no migrated lines of business.

Political blind spot:
- The diagram shows BRK (brokers) connecting both to the Broker Portal in the Digital Engagement Layer and directly to the API Gateway. That dual-path implies brokers can bypass the digital layer and hit the gateway directly. If the BPRT (Broker Portal) is the governed, audited path and the direct GW→BRK path is a machine-to-machine legacy accommodation, that distinction needs to be explicit — because Morag Sinclair's shadow IT started the same way: an ungoverned side-door that became a live data risk.
