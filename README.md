# Architect Agent

A Claude Code workspace configured to act as a **senior technology architect** advising UK financial services clients on digital transformation, legacy modernisation, and cloud architecture.

The agent grounds every recommendation in the active engagement context (`./client/`) and evergreen reference material (`./knowledge/`), and delegates review and external research to specialist sub-agents.

---

## Project Structure

```
architect-agent/
├── CLAUDE.md                  # Behaviour layer: role, principles, workflow, output standards
├── Readme.md                  # This file
├── commands.md                # Setup and run instructions
│
├── .claude/
│   ├── agents/                # Sub-agents (each runs in its own context window)
│   │   ├── critic.md          # Skeptical senior architect — reviews drafts
│   │   └── researcher.md      # Senior analyst — finds external facts
│   │
│   ├── skills/                # Reusable skills the agent can invoke
│   │   ├── adr-writer/SKILL.md
│   │   └── stakeholder-analysis/SKILL.md
│   │
│   └── agent-memory/          # Per-agent persistent memory
│
├── client/                    # Current engagement context
│   └── atlas-brief.md         # Active client brief (read first on every conversation)
│
├── knowledge/                 # Evergreen reference material
│   ├── arch-patterns.md
│   └── pra-ss121-summary.md
│
└── output/                    # Where the agent produces deliverables
    ├── adr-core-platform.md
    ├── week1-top-risks.md
    └── target-state.mermaid
```

---

## How It Works

The agent follows a structured reasoning workflow on every task:

1. **Interpret** the request — what deliverable is being asked for?
2. **Read `./client/`** for current engagement context.
3. **Read `./knowledge/`** for relevant evergreen reference material.
4. **Think carefully** before producing output on complex tasks.
5. **Draft** in `./output/`.
6. **Self-critique** via the Critic sub-agent — surface gaps, revise.
7. **Proactively flag** anything the user should know but didn't ask.

## Sub-Agents

| Agent | Role | When to invoke |
|---|---|---|
| **Critic** | Skeptical senior architect | Reviews every draft before delivery |
| **Researcher** | Senior analyst | When external facts (regulatory positions, market benchmarks, vendor capabilities) need verification |

Delegate by name: *"have the critic review this ADR"* or *"ask the researcher to check the latest PRA position on cloud concentration."*

## Skills

| Skill | Purpose |
|---|---|
| **adr-writer** | Produces Architecture Decision Records grounded in client context |
| **stakeholder-analysis** | Produces stakeholder mapping / RACI for board, steerco, or milestone reviews |

---

## Getting Started

> Prerequisite: [Claude Code](https://claude.com/claude-code) installed and authenticated.

1. **Clone the repo** and open it in Claude Code.
2. **Confirm the project skeleton** — ask Claude:
   > *"Read my Readme.md project structure section and create folders and files if not exist."*
3. **Review the behaviour layer** — `CLAUDE.md` in the root, plus the sub-agent definitions in `.claude/agents/`.
4. **Restart the session** so sub-agents load. Verify with `/agents`.
5. **Swap in your engagement context** — replace `client/atlas-brief.md` with the brief for your current client.
6. **Run a task** — for example:
   > *"Produce an ADR for the core platform decision. Have the critic review it before delivering."*

---

## Output Standards

- Every deliverable goes to `./output/` with a meaningful filename.
- Professional consulting tone — no filler.
- Length discipline: board summaries max 450 words, ADRs max 3 pages.
- Diagrams in Mermaid. Formal deliverables in Markdown or `.docx`.

## What This Agent Does Not Do

- Speculate about what it doesn't know — it says so.
- Produce generic answers when client context is specific.
- Produce artefacts without invoking the Critic first.
