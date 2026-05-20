---
name: stakeholder-analysis
description: Produces a stakeholder analysis grounded in the current client context. Use when the user asks for stakeholder mapping, a "who do I need on side" analysis, a RACI, or when preparing for a board, steerco, or milestone review. Also use proactively when a major decision is being written up and the stakeholder implications haven't been thought through.
allowed-tools: Read, Glob, Grep, Write
---

# Stakeholder Analysis

Produce a stakeholder analysis that would hold up if the engagement lead took it straight into a senior client meeting. Generic stakeholder analyses do not hold up — they signal a junior consultant. This skill produces the kind a senior architect would actually use.

## Structure

### 1. Stakeholder inventory

Read `./client/` stakeholder section. If it's absent, flag that as a gap and produce a tentative analysis noting which stakeholders you inferred versus had documented.

For each stakeholder, capture:

- **Name & role** — formal title plus informal power position
- **Posture** — Sponsor / Advocate / Skeptic / Gatekeeper / Veto-holder / Blocker
- **Primary concern** — the one thing they care most about in this engagement
- **What they'd kill the programme over** — the specific failure that would trigger withdrawal of support
- **What they'd celebrate** — the specific success that would make them champion the programme

### 2. Power × posture grid

A 2×2 matrix. Axis 1: Power (low → high). Axis 2: Posture (against → for). Place each stakeholder. The interesting quadrants:

- **High power, against** — must be converted or neutralised before they block
- **High power, for** — must be kept informed and reinforced
- **Low power, against** — monitor but rarely invest time
- **Low power, for** — cultivate; may become high-power allies

### 3. Engagement strategy

For each stakeholder in the "high power, against" or "high power, gatekeeper" category, produce a specific plan: first conversation, what would shift posture, who else needs to be in the room, what failure mode to avoid.

"Schedule a 1:1" is not a strategy. "Use the CFO's next budget review as the moment to present Phase 1 ROI evidence, led by the programme director, with the CTO present to answer technical questions" is a strategy.

### 4. The stakeholder no one has asked about

Every real programme has a stakeholder who matters but hasn't been named. Ask it explicitly: *"who are we not talking about?"* Common candidates: the regulator, the auditor, the union (in workforce-affecting programmes), the PE investor or board investment committee, the outgoing incumbent vendor. Flag the candidate(s) most likely to matter for this specific client based on `./client/`.

### 5. The stakeholder map at milestones

For each of the next three major programme milestones, name the 3 stakeholders whose alignment is most critical *at that moment*. The list should change across milestones — alignment is shifting, not static.

## Review discipline

Before producing the final version:

1. Ask the critic sub-agent to review. It reads the analysis and `./client/` in its own context.
2. Challenge any stakeholder described in bland or generic terms — real people have edges, and the analysis should capture them.
3. Check political realism: would a client-side senior person reading this recognise their own organisation? Or does it read like a consultancy template?

## Output

Save to `./output/stakeholder-analysis-{milestone-or-date}.md`.

Example: `./output/stakeholder-analysis-phase1-kickoff.md`

## Edge cases

- **Limited client information.** If `./client/` is thin, produce the analysis as far as the information allows, then name explicitly the stakeholder questions the engagement lead should ask in the next client conversation.
- **Asked during conflict.** Sometimes stakeholder analysis is requested because the programme is in trouble. Handle sensitively — the analysis should help the user think, not retrofit blame.
- **Named individuals.** When `./client/` names specific individuals, use their names. When it doesn't, use roles. Never invent names.
