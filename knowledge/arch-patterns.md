# Architecture Patterns — Modernisation Reference

> **File purpose:** Condensed reference on the architectural patterns most relevant to legacy-to-cloud modernisation in regulated industries — financial services, insurance, healthcare, utilities. When you recommend a pattern, name it, explain why it fits the specific client context (found in `./client/`), and identify the trade-offs the client is accepting.
>
> **Scope:** Evergreen. This file does not reference any specific client. It applies equally to a bank, an insurer, a utility, or any large enterprise with significant legacy technology.
>
> **Pattern selection discipline:** Generic pattern invocation ("we'll use microservices") is the tell of a weak architect. Pattern selection grounded in specific client constraints is the hallmark of a good one. Every recommendation must (1) name the pattern, (2) ground it in the client's current circumstances, and (3) name the trade-off.

---

## Strangler Fig

**What it is.** Named after the Australian strangler fig, which grows around and eventually replaces its host tree. You build the new system alongside the legacy, incrementally redirecting traffic and functionality from old to new until the legacy can be decommissioned. The legacy is never "cut over" in a single event — it is slowly deprived of responsibility until nothing is left.

**When it fits.** Modernisation programmes where a single-shot cutover is too risky — which, in regulated environments with high availability expectations, is almost always. Particularly well-suited to mainframe and monolith replacement where full-system replatform would take years and carry unacceptable regulatory and operational risk.

**When it doesn't.** Situations with a genuine hard deadline forcing a full cutover (rare, but happens with end-of-vendor-support events). Situations where the legacy system is so unstable that prolonged coexistence is itself the biggest risk.

**Trade-offs.** You run two systems in parallel for an extended period — more expensive, more complex, more integration surface area. You need a strong facade or anti-corruption layer to prevent the legacy's data model from infecting the new services. Requires genuine discipline to actually decommission the legacy at the end; programmes that lose momentum often end up running both systems indefinitely.

**Key questions to ask the client.** What's the availability SLA that must be maintained during coexistence? Who owns the decommission commitment? What triggers the final cutover — a capability threshold, a date, a cost ceiling?

---

## Anti-Corruption Layer

**What it is.** A translation layer that sits between a new system and a legacy it must interact with, converting the legacy's data model and semantics into the new system's cleaner abstractions (and vice versa). Prevents the legacy's historical compromises from leaking into new designs.

**When it fits.** Anywhere a new system must integrate with a legacy whose data model is not worth carrying forward. Essential accompaniment to strangler fig patterns. Particularly valuable when the legacy has decades of accumulated semantic drift (field names that mean something different from what they say, status codes whose meanings have been redefined twice, etc.).

**Trade-offs.** Another layer of latency. Another surface of potential data loss or translation error. Requires discipline to keep the translation logic in the ACL and not let legacy concepts leak through to consumers. Often underestimated in complexity — mapping a real-world legacy data model tends to reveal assumptions nobody knew were being made.

**Key questions to ask the client.** Is the legacy data model documented, or does it live in developer memory? What's the testing strategy for semantic correctness — not just schema mapping, but meaning preservation?

---

## Event-Driven Architecture

**What it is.** Components communicate by publishing and consuming events on a durable event stream (Kafka, Kinesis, EventBridge, etc.) rather than by direct request/response API calls. Producers don't know about consumers; consumers don't know about producers. Events are a durable record of what happened.

**When it fits.** Domains where multiple consumers need to react to the same business event (a transaction is authorised → fraud scoring runs, customer notification fires, audit log records, reporting system captures). Also where audit, replay, or eventual consistency are more valuable than immediate synchronous consistency. Strong fit for domains where regulators require a replayable audit trail.

**When it doesn't.** Use cases requiring immediate, synchronous, transactional consistency — a payment authorisation that must return a single authoritative decision in a single request, for instance. EDA can handle this but it requires careful design and often adds complexity that a synchronous API doesn't.

**Trade-offs.** Eventual consistency is a real conceptual shift for teams used to database transactions. Debugging distributed event flows is harder than debugging a stack trace. Schema evolution on event streams is a discipline you must build, not assume. Operational maturity matters — event streams that are not monitored properly fail silently.

**Key questions to ask the client.** Does the operations team have experience with distributed event systems? What are the SLAs on event propagation, and are they compatible with the client's regulatory reporting windows?

---

## CQRS (Command Query Responsibility Segregation)

**What it is.** Separates the model used to update data (commands) from the model used to read data (queries). Often — though not always — paired with event sourcing. Allows the read side to be optimised independently of the write side: different data stores, different scaling, different schemas.

**When it fits.** Workloads with very asymmetric read/write patterns — high-volume reads (dashboards, analytics, customer-facing account views) against lower-volume writes. Workloads where the write model's normalised shape would make reads unacceptably slow. Also a strong fit when "the data is locked in the legacy" is a recurring business complaint — CQRS lets you materialise purpose-built read models without disturbing the legacy write path.

**When it doesn't.** Simple CRUD domains where read and write patterns are symmetric and low-volume. CQRS adds complexity; symmetric domains don't benefit from the complexity.

**Trade-offs.** Eventual consistency between write and read sides. More moving parts. Requires genuine investment in the read-model materialisation pipeline — it is not free.

**Key questions to ask the client.** Where are the current data bottlenecks — read latency, query flexibility, or both? Who owns the read-model definitions over time, and how will schema change be governed?

---

## API Gateway

**What it is.** A single, managed entry point for external API consumers. Handles authentication, authorisation, rate limiting, routing, observability, and schema enforcement. The clients see the gateway; the gateway routes to the appropriate internal service.

**When it fits.** Any architecture that exposes APIs to external consumers — partners, fintechs, Open Banking / Open Finance participants, market counterparties, regulatory data exchanges. Gives you a central point to enforce security, compliance, and operational controls. Particularly valuable in regulated industries where authentication standards and audit trails are non-negotiable.

**Trade-offs.** One more piece of infrastructure that must itself be highly available and well-operated. If poorly designed it becomes a dumb proxy that adds latency without adding value. If over-designed it becomes a bottleneck that every team has to queue behind. Choice of gateway (AWS API Gateway, Kong, Apigee, Azure APIM, etc.) is more about operational preference and existing cloud posture than functional fit.

**Key questions to ask the client.** What's the authentication standard the gateway must enforce (OAuth 2.0, mTLS, both)? Who operates the gateway — a central platform team, or each service team?

---

## Service Mesh

**What it is.** Infrastructure layer that handles service-to-service communication — identity, encryption, routing, observability, retries — without requiring each service to implement these concerns itself. Istio and Linkerd are the common implementations on Kubernetes.

**When it fits.** Microservices-heavy architectures running on Kubernetes where you have enough services that the operational cost of per-service cross-cutting concerns is material. Particularly valuable in regulated environments where mutual TLS and audit are non-negotiable. Rule of thumb: becomes valuable past roughly 15 services in production.

**When it doesn't.** Small architectures where a service mesh's complexity exceeds the complexity it removes. Teams without Kubernetes operational maturity will find service mesh a further cost, not a saving.

**Trade-offs.** Significant operational learning curve. Additional failure surface. Observability gets richer but also more complex. Upgrade management of the mesh itself is a real ongoing cost.

**Key questions to ask the client.** How mature is the Kubernetes operations capability today? How many services are actually in production now, and how many in the 12-month plan?

---

## Data Mesh

**What it is.** A decentralised approach to data architecture where domain teams own their data as products, exposed through self-service interfaces, with federated governance. Moves away from central data lakes owned by a central data team.

**When it fits.** Large organisations with strong domain boundaries, many data consumers, and a data engineering team that has become a bottleneck. Requires organisational maturity — it is as much an operating-model pattern as a technology pattern.

**When it doesn't.** Smaller organisations where a single analytics team can reasonably serve all internal consumers. Organisations without clear domain ownership where "data mesh" becomes a fashionable label for abandoning central governance without replacing it. Organisations still fighting to establish basic data quality — data mesh amplifies governance weakness rather than solving it.

**Trade-offs.** Organisational change is the hard part, not the technology. Federated governance is harder to run than central governance. Data product ownership requires teams to accept responsibilities many of them currently push onto a central team.

**Key questions to ask the client.** Do product-oriented domain teams actually exist, or is this aspiration? Who will own cross-domain governance when the central team is dissolved?

---

## Pattern selection discipline

When recommending an architecture pattern, always do three things:

1. **Name the pattern** clearly.
2. **Ground the recommendation** in the specific client context — why this pattern fits *this* client's circumstances from `./client/`, not just "good architecture in general".
3. **Name the trade-off** — what the client is explicitly accepting by choosing this pattern.

A recommendation that does only the first is a junior architect's answer. A recommendation that does all three is a senior architect's.

## Common anti-patterns to flag

- **Pattern-first thinking.** Choosing a pattern because it's fashionable, then back-filling justification. Always start with the client's constraints and pick the pattern that fits.
- **Pattern stacking without purpose.** Microservices + EDA + CQRS + service mesh + data mesh, all at once, is a sign of enthusiasm rather than design. Each pattern should have a named problem it is solving.
- **Pattern invocation without trade-off.** "We'll use event-driven architecture" without naming what the client is giving up (synchronous consistency, debuggability, operational complexity) is incomplete advice.
- **Ignoring the existing skill base.** A beautiful target-state architecture that the client's team cannot operate is a failure, not a success. Pattern choice must be calibrated to realistic capability uplift.
