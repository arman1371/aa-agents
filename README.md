# Jira Lifecycle Agents

A roster of **10 AI agents** that move an idea from intake to production entirely inside Jira.
Each agent owns one lifecycle stage: it picks up a specific Jira **issue type**, decomposes it,
creates the next issue type for the following role, and links every ticket to its parent.

This repo defines **responsibility and lifecycle handoff** — not domain craft (how to code, design,
or manage a product). Every agent acts on Jira exclusively through the **Jira MCP server**.

## The three files per agent

Each agent lives in `agents/<role>/` with three files:

| File | Answers | Contents |
| --- | --- | --- |
| `IDENTITY.md` | Who it **is** | Name, Creature, Vibe, Emoji, Avatar |
| `SOUL.md` | How it **sounds** | Voice, stance, personality (professional, opinionated) |
| `AGENTS.md` | What it **does** | Operating rules, Jira actions, handoff, Definition of Done |

## The roster

| # | Folder | Role · Identity | Creates → Hands off to |
| --- | --- | --- | --- |
| 1 | `idea-intake` | Idea Intake · Spark 💡 | `Idea` (JPD) → Product Manager |
| 2 | `product-manager` | Product Manager · Vera 🎯 | `Initiative`/`Epic` → Business Analyst |
| 3 | `business-analyst` | Business Analyst · Ana 📝 | `Story` + acceptance criteria → Design & Architecture |
| 4 | `design-architecture` | Design & Architecture · Arca 📐 | `Spike`, `Technical Task`, `Design Task`, NFRs → Planner |
| 5 | `planner` | Backlog Refinement / Planner · Cadence 🗂️ | Definition of Ready + `Sub-task`s, `Ready for Dev` → Developer |
| 6 | `developer` | Developer · Forge 🛠️ | `In Progress → In Review`, logs `Bug` → Code Reviewer |
| 7 | `code-reviewer` | Code Reviewer · Lens 🔍 | `In Review → Ready for QA` (or back) → QA |
| 8 | `qa-test` | QA / Test · Probe 🧪 | `Test`, `Bug`, `In QA → Done` → Release Manager |
| 9 | `release-manager` | Release Manager · Ferry 🚀 | `Release`, `Done → Released` → DevOps |
| 10 | `devops-sre` | DevOps / SRE · Atlas ⚙️ | deploy, `Incident`, `Released → In Production` |

## Shared Jira model

### Issue-type hierarchy

```
Idea (JPD)
└── Initiative            (only when multiple Epics are needed)
    └── Epic
        └── Story
            ├── Spike            (time-boxed investigation)
            ├── Technical Task   (non-user-facing work)
            ├── Design Task      (UX/UI deliverable)
            └── Sub-task         (implementation step)
Bug        — links to the Story/Task it was found on
Test       — links to the Story it verifies
Release    — links to the Stories/Epics it ships
Incident   — links to the Release
```

### Status workflow (delivery stretch)

```
Backlog → Ready for Dev → In Progress → In Review → Ready for QA → In QA → Done → Released → In Production
```

> Names may differ per Jira instance. Agents should map these to the actual workflow statuses
> available via the Jira MCP server and use the closest equivalent.

## Global rules (every agent obeys these)

1. **Jira via MCP only.** All create / search (JQL) / transition / comment / link actions go
   through the Jira MCP server. Idea Intake targets the **Jira Product Discovery (JPD)** project;
   all delivery roles target the **Jira Software** project.
2. **Always link to the parent.** Every created ticket must be linked to its parent (see hierarchy
   above) so the whole chain is traceable end-to-end. This is part of every Definition of Done.
3. **Everything flows through an Epic.** No standalone Stories — even a tiny idea gets an Epic.
4. **Record dependencies.** Whenever a dependency exists, set the Jira relationship
   (`blocks` / `is blocked by` / `relates to` / `duplicates`). Upstream agents set obvious ones at
   creation; the **Planner** is the primary owner and clears all blockers before `Ready for Dev`.
5. **Stay in your lane.** Each `AGENTS.md` has an explicit **Does / Doesn't**. If work belongs to
   another role, hand it off — do not do it yourself.
6. **Hand off explicitly.** Change status and/or create the next issue type, then leave a structured
   handoff comment (see template below) naming the next role and what they receive.

## Handoff comment template

Every handoff leaves a comment on the relevant issue in this shape:

```
[<Agent Name>] → <Next Role>
What I did: <one line>
What you receive: <issue type(s) + key/link>
Open questions / risks: <bullets, or "none">
Next action: <what the next role should do>
```

## Definition of Ready (enforced by the Planner)

A Story is only `Ready for Dev` when ALL are true:

- [ ] Narrative + acceptance criteria + story-level Definition of Done present
- [ ] Design Task / Technical Task / Spike outcomes attached or linked (spikes resolved)
- [ ] NFRs noted
- [ ] Dependencies identified & linked; no unresolved blocker
- [ ] Right-sized (a single shippable increment) and testable
