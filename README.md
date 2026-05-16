# dev-blackboard

> A markdown-native **blackboard architecture** for AI coding agents. Shared in-repo substrate where humans and AI agents coordinate work, decisions, and handovers.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Built for AI dev agents](https://img.shields.io/badge/built%20for-AI%20dev%20agents-orange)](#)

---

## The problem

An AI coding agent like Claude Code, Cursor, Copilot, or Aider starts every session **blank**. It doesn't know:

- What's currently being worked on
- What was decided last week and why
- Which approaches were already tried and rejected
- What the next session is supposed to pick up
- What other agents (or humans) are touching at the same time

So you re-explain everything every session. Context evaporates. Decisions get re-litigated. Multi-agent setups drift because there's no shared substrate for them to coordinate through. Work gets half-done because no one wrote down the plan.

**dev-blackboard** is the missing piece: an opinionated, in-repo, markdown-native project management substrate designed for AI agents to read from and write to as first-class participants.

---

## What is a "blackboard"?

In multi-agent AI systems, a **blackboard architecture** is a coordination pattern: a shared knowledge surface that any number of independent agents can read from and write to. Agents don't talk to each other directly — they coordinate *indirectly*, through state written on the shared blackboard. The blackboard accumulates context over time and survives any individual agent's lifetime.

The pattern dates back to Hayes-Roth and the Hearsay-II speech recognition system in the 1970s. It's the conceptual ancestor of modern multi-agent frameworks (LangGraph state, AutoGen group memory, CrewAI shared context).

**dev-blackboard makes that pattern concrete as files in your repo.** The `.dev/` folder *is* the blackboard. Tasks, handovers, specs, and decisions are entries posted to it. A short `CLAUDE.md` protocol enforces the read-on-start / write-on-end contract that any agent — human or AI — must follow.

No daemon. No database. No API. Just markdown files you can grep, version, and review in a PR.

---

## Quick start

```bash
# 1. Clone this repo
git clone https://github.com/Aithu-Snehith/dev-blackboard.git
cd dev-blackboard

# 2. Copy it into your project as `.bootstrap/`
cp -r . /path/to/your-project/.bootstrap

# 3. Open Claude Code in your project
cd /path/to/your-project
claude

# 4. Paste the prompt from SETUP_GUIDE.md — Claude does the rest
```

Full step-by-step (with the exact prompt to paste) lives in [`SETUP_GUIDE.md`](./SETUP_GUIDE.md).

---

## What gets installed in your project

After the bootstrap runs, your project gains:

```
your-project/
├── CLAUDE.md                          ← protocol: read blackboard on start, write on end
├── .gitignore                         ← .remember/ added
├── .dev/                              ← THE BLACKBOARD (tracked in git)
│   ├── README.md
│   ├── *_TEMPLATE.md                  ← copy these for new entries
│   ├── tasks/{active,backlog,done}/   ← atomic units of work
│   ├── epics/<slug>/                  ← major workstreams (customer, feature, initiative)
│   │   ├── epic.md
│   │   └── tasks/{active,backlog,done}/
│   └── handover/YYYY-MM-DD-<topic>.md ← session-end records: what was done, what's left
├── docs/superpowers/                  ← design artifacts (tracked in git)
│   ├── specs/                         ← why & what (with decisions log)
│   └── plans/                         ← how (with checkbox tasks)
└── .remember/                         ← optional short-term session buffer (gitignored)
```

Every entry is signed (session ID), dated, and append-only. The folder structure encodes the state machine (`backlog → active → done`). The `CLAUDE.md` protocol makes every agent that touches the repo check the blackboard before acting and post to it after.

---

## How agents use the blackboard

### On session start

```
1. Generate a session ID (author-hash + timestamp — stable, no PII)
2. Read .dev/tasks/active/ and .dev/epics/*/tasks/active/
3. Read the latest .dev/handover/ to see where the last session stopped
4. Match the user's request to an existing task — or stop and create one first
```

### During work

- Append to the task's Session Log after each meaningful chunk
- Record non-obvious decisions in the epic's or spec's Decisions Log
- Note discovered gotchas

### On session end

- Update the task's Session Log with what was done / what's left / blockers
- Move the task file to `done/` if complete
- Write a handover to `.dev/handover/YYYY-MM-DD-<topic>.md`

Next session — same agent, different agent, human teammate, whoever — reads that handover first and picks up cold.

---

## Why this matters for multi-agent setups

If you're running parallel Claude sessions, dispatching sub-agents, or coordinating multiple specialized agents (one writes code, one reviews, one tests), they all need a place to:

- See what each other is doing
- Avoid stepping on each other's work
- Inherit the same set of decisions
- Hand off cleanly when one finishes

The blackboard *is* that place. No agent-to-agent messaging protocol needed. Agents coordinate by reading and writing files — the same files a human reviewer can read in a PR.

---

## Files in this kit

| File | Purpose |
|------|---------|
| [`BOOTSTRAP.md`](./BOOTSTRAP.md)                                   | Setup instruction sheet a Claude agent reads to install dev-blackboard into a project |
| [`SETUP_GUIDE.md`](./SETUP_GUIDE.md)                               | The literal prompt to paste into Claude in your new project |
| [`CLAUDE.md.template`](./CLAUDE.md.template)                       | The project-root `CLAUDE.md` to install — session-start protocol, session-ID command, task tiers |
| [`MEMORY_GUIDE.md`](./MEMORY_GUIDE.md)                             | The three memory layers (auto-memory, `.remember/`, the blackboard) and what goes where |
| [`CHEATSHEET.md`](./CHEATSHEET.md)                                 | One-page daily workflow reference. Print it. |
| [`REVIEW.md`](./REVIEW.md)                                         | Lessons learned + improvements to apply on day one |
| [`templates/TASK_TEMPLATE.md`](./templates/TASK_TEMPLATE.md)       | Atomic unit of work (goal, context, files, acceptance criteria, session log) |
| [`templates/EPIC_TEMPLATE.md`](./templates/EPIC_TEMPLATE.md)       | Major workstream (scope, plans, tasks manifest, decisions log) |
| [`templates/HANDOVER_TEMPLATE.md`](./templates/HANDOVER_TEMPLATE.md) | Session-end summary (what was done, what's left, decisions, gotchas) |
| [`templates/PLAN_TEMPLATE.md`](./templates/PLAN_TEMPLATE.md)       | Step-by-step implementation plan with checkbox tasks |
| [`templates/SPEC_TEMPLATE.md`](./templates/SPEC_TEMPLATE.md)       | Design doc (context, goals, architecture, decisions log) |
| [`templates/DEV_README.md`](./templates/DEV_README.md)             | The `.dev/README.md` that ships in the new repo to orient teammates |

---

## What's intentionally NOT in this kit

- **No runtime, no daemon, no database.** The blackboard is markdown files. Read them with `cat`, edit them with any editor, review them in a PR.
- **No tool lock-in.** Designed for Claude Code, but the protocol works with Cursor, Aider, Copilot, or any AI coding agent that can read/write files.
- **No `remember` plugin** — that's a separate install (`/plugin install remember@claude-plugins-official`) for short-term session buffer. dev-blackboard works without it.
- **No domain-specific tools, skills, or code** — those belong to your project, not the workflow shell.

---

## Requirements

- A git repository (the blackboard lives in it)
- [Claude Code](https://claude.com/claude-code) CLI or any AI coding agent that reads/writes files
- Bash / zsh — the session-ID command uses `shasum`

Optional:

- The [`remember`](https://github.com/Digital-Process-Tools/claude-remember) plugin for short-term auto-memory
- The `superpowers` plugin for the `brainstorming → writing-plans → executing-plans` skill chain

---

## Contributing / feedback

This kit is opinionated but not finished. If you run it in your project and something doesn't fit, [open an issue](https://github.com/Aithu-Snehith/dev-blackboard/issues) or send a PR. Particularly interested in:

- Use with non-Claude agents (Cursor, Aider, Copilot, Codex) — what adapts, what breaks
- Multi-agent coordination patterns the kit accidentally enables or blocks
- Plan ↔ task synchronization tricks
- Anti-patterns the kit accidentally encourages

See [`REVIEW.md`](./REVIEW.md) for the rough edges I already know about.

---

## License

MIT — see [`LICENSE`](./LICENSE). Do what you want with it. If you adapt it, a link back is appreciated but not required.
