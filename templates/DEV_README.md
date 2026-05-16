# .dev/ — Task & Handover System

This folder is the shared workspace for all Claude Code sessions working on this repo. Humans can read it too — that's the point.

## Structure

```
.dev/
├── tasks/                     ← project-wide / cross-epic tasks
│   ├── active/                ← currently being worked on
│   ├── backlog/               ← defined but not started
│   └── done/                  ← completed
├── epics/                     ← per-epic dirs (customer/prospect or major workstream)
│   └── <epic-slug>/
│       ├── epic.md            ← overview, plans manifest, decisions, session log
│       └── tasks/{active,backlog,done}/
├── handover/                  ← session summaries (what was done, what's left)
├── TASK_TEMPLATE.md           ← copy this for new tasks
├── EPIC_TEMPLATE.md           ← copy this for new epics
└── HANDOVER_TEMPLATE.md       ← copy this on session-end
```

## How It Works

1. **Before starting work**, Claude reads active tasks (project-wide *and* under `epics/*/tasks/active/`) and the most recent handover.
2. **If no task exists** for what's being asked, Claude creates one first and gets it reviewed.
3. **During work**, Claude updates the task's Session Log.
4. **When wrapping up**, Claude writes a handover doc and updates the task.

Full workflow rules live in the project-root `CLAUDE.md`. The templates in this folder are the canonical shapes for tasks, epics, and handovers.

## When to create an epic vs. a top-level task

- **Epic** (`.dev/epics/<slug>/`): a customer/prospect, a multi-month workstream, or anything that will spawn 5+ tasks across multiple plans.
- **Top-level task** (`.dev/tasks/`): a single deliverable, a refactor, a bug fix, a one-off. If it grows into an epic, migrate it later.

## Related folders

- `docs/superpowers/specs/` — design docs (the "why and what")
- `docs/superpowers/plans/` — step-by-step implementation plans (the "how")
- `.remember/` — auto-generated short-term session memory (gitignored)
