---
name: adr-writer
description: Produces Architecture Decision Records grounded in the current client context. Use when the user asks for an ADR, a decision document, or says "write this up formally so we can review it." Also use proactively when a significant architectural trade-off has been discussed and should be captured.
allowed-tools: Read, Glob, Grep, Write
---

# ADR Writer

Produce an Architecture Decision Record that would survive senior client review. Generic ADRs are the tell of a junior architect; this skill produces the kind grounded in specific client context.

## Structure

Every ADR follows this shape. Do not deviate without explicit user instruction.

1. **Title** — `ADR-{NNN}: {Decision, stated as an assertion}`
   Example: `ADR-007: Adopt strangler fig migration over single-shot replatform`

2. **Status** — Proposed · Accepted · Rejected · Deprecated · Superseded. Include date and decision owner.

3. **Context** — What's the forcing function? What constraints are in play? Read `./client/` to ground this in the specific engagement. 1–2 paragraphs, no fluff.

4. **Options considered** — Minimum three options. Each with: one-line description, key benefit, key cost, why it was seriously considered. If you cannot generate three genuine options, stop and flag that the decision may not be a real decision.

5. **Decision** — The chosen option, stated as an assertion. Single paragraph. Grounded in client-specific constraints.

6. **Rationale** — Must reference at least two client-specific constraints from `./client/`. Generic rationale ("this is industry best practice") is a red flag — reject it.

7. **Consequences** — Positive (what becomes possible), negative (what the client is accepting), neutral (what changes without being better or worse). A decision with no negative consequences has not been properly analysed.

8. **Review triggers** — 2–3 specific future conditions that would cause this decision to be revisited. Avoid "review annually" — that's a calendar, not a trigger. Use concrete conditions like "when migration cost tracking exceeds 130% of Phase 1 budget."

## Review discipline

Before producing the final version:

1. Ask the critic sub-agent to review the draft. It reads the draft and `./client/` in its own context, returns specific weaknesses.
2. Address each weakness the Critic raises.
3. Check length. ADRs exceed 3 pages only if genuinely warranted. If the draft is longer, cut before reviewing — length signals weak reasoning.
4. Check stakeholder blind spots. Read `./client/` stakeholder section. For each stakeholder, ask: "would they have a concern about this decision that isn't addressed?" Flag any that aren't.
5. Name the test that would kill this decision — every decision has an implicit failure scenario. Surface it in the consequences.

## Output

Save to `./output/adr-{NNN}-{slug}.md` with a meaningful filename.

Good: `adr-007-strangler-fig-migration.md`
Bad: `adr-007-decision.md` (too generic)
Bad: `adr-007-architecture-decision-record.md` (redundant)

## Edge cases

- **Multiple decisions in one request.** If the request contains 2–3 decisions, produce 2–3 separate ADRs. One decision per record.
- **Decision already made.** Sometimes an ADR documents something already committed. Still produce the full analysis — future readers need to understand *why*, not just *what*.
- **Decision premature.** If client context makes clear a decision is premature, flag this and produce a "decision deferral note" instead, naming what information is missing.
