# Memory System Guide

Claude Code has **three distinct memory layers**. Knowing what goes where keeps each useful.

```
┌─────────────────────────────────────────────────────────────────────┐
│ Layer 1: AUTO-MEMORY (long-term, per-project, automatic)             │
│   Location: ~/.claude/projects/<project-slug>/memory/                │
│   Files:    MEMORY.md (index) + per-topic files                      │
│   Lifetime: indefinite, persists across sessions                     │
│   Use for:  user identity, feedback, project facts, references       │
└─────────────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────────────┐
│ Layer 2: REMEMBER PLUGIN (short/medium-term, per-project, automatic) │
│   Location: <project-root>/.remember/                                │
│   Files:    now.md, today-YYYY-MM-DD.md, recent.md, archive.md       │
│   Lifetime: rolling window — buffer / daily / 7 days / older         │
│   Use for:  what you actually did in recent sessions (compressed)    │
└─────────────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────────────┐
│ Layer 3: TASK & HANDOVER FILES (durable, per-project, manual)        │
│   Location: <project-root>/.dev/                                     │
│   Files:    tasks/*.md, epics/<slug>/epic.md, handover/*.md          │
│   Lifetime: durable, checked into git, the source of truth on work   │
│   Use for:  scope, decisions, what was done, what's left             │
└─────────────────────────────────────────────────────────────────────┘
```

The layers do not compete — they answer different questions:

| Question | Layer |
|----------|-------|
| "What did I do yesterday?" | 2 (remember plugin) |
| "What did the user tell me to never do again?" | 1 (auto-memory feedback) |
| "What is the scope of feature X?" | 3 (task / spec / plan) |
| "Who is this user, what's their role?" | 1 (auto-memory user) |
| "Where do I find the auth code?" | the codebase (do not save) |
| "What's the active branch and last commit?" | git (do not save) |

---

## Layer 1: Auto-memory conventions

The auto-memory directory is created automatically by Claude Code at:

```
~/.claude/projects/<project-slug>/memory/
```

`<project-slug>` is derived from the absolute path of your project (Claude handles the slugging).

### File types

Four kinds of memory, each saved as its own file:

| Type | Purpose |
|------|---------|
| `user` | Who the user is, role, expertise, preferences |
| `feedback` | Guidance the user gave on how to approach work — both corrections and validated approaches |
| `project` | Facts about ongoing work that aren't derivable from code or git |
| `reference` | Pointers to external systems (Linear project, Grafana dashboard, etc.) |

### File naming

```
user_<topic>.md         e.g. user_role.md
feedback_<topic>.md     e.g. feedback_testing_must_hit_real_db.md
project_<topic>.md      e.g. project_q2_freeze_dates.md
reference_<topic>.md    e.g. reference_linear_pipeline_project.md
```

### File format

```markdown
---
name: short-kebab-case-slug
description: one-line summary — used to decide relevance in future conversations
metadata:
  type: user | feedback | project | reference
---

<memory body — for feedback/project, structure as: rule/fact + **Why:** + **How to apply:**>

Link related memories with [[other-name]].
```

### MEMORY.md index

`MEMORY.md` is the entry point. It is an *index*, not a memory:

```markdown
# <project-name> Project Memory

## Stack
- <one-line stack notes>

## User
- [User role](user_role.md) — <one-line hook>

## Feedback
- [No PII in repo files](feedback_no_pii_in_repo.md) — never write user's name/email into tracked files

## Project
- [Q2 freeze](project_q2_freeze.md) — merge freeze starts YYYY-MM-DD
```

Keep each line under ~150 chars. Lines past 200 get truncated when MEMORY.md is loaded into context.

### What NOT to save

These belong in code or git, not memory:

- Code patterns, conventions, architecture, file paths
- Git history, recent commits, who-changed-what
- Debugging solutions or fix recipes (fix lives in code; reason lives in commit message)
- Anything already in CLAUDE.md
- Ephemeral task state (use `.dev/tasks/` instead)

Even if the user says "save this PR list" — push back: ask what was *surprising* or *non-obvious*. That's the memory.

### Verification rule

A memory naming a specific function, file, or flag is a claim about a moment in time. Before recommending from memory:

- File path → check it still exists
- Function / flag → grep for it
- Architectural claim → confirm against current code

"The memory says X exists" ≠ "X exists now." Update or remove stale memories rather than acting on them.

---

## Layer 2: Remember plugin conventions

The `remember` plugin writes to `<project-root>/.remember/`. The files are auto-managed; you mostly don't touch them. But knowing the layout helps:

| File | Content |
|------|---------|
| `now.md` | Current session buffer (live) |
| `today-YYYY-MM-DD.md` | Compressed daily summary |
| `recent.md` | Last 7 days, consolidated |
| `archive.md` | Older history, consolidated |
| `core-memories.md` | Key moments worth preserving across compression |

When the user types `/remember`, the plugin writes a handoff note to `.remember/remember.md` that the next session reads on start. Use this when wrapping up a session — it's complementary to the manual handover at `.dev/handover/`.

**Required setup:**
1. Install the plugin: `/plugin install remember@claude-plugins-official`
2. Disable auto-compact in Claude Code (`/config`) — auto-compact discards the raw conversation before the save pipeline can capture it
3. Add `.remember/` to `.gitignore` (dev-blackboard's bootstrap does this for you)

---

## Layer 3: Tasks & handovers (manual, durable)

See `.dev/README.md` and the project-root `CLAUDE.md` for the full workflow. Key idea: **scope, decisions, and "what's left" go here, not in memory.**

When a memory entry starts to feel like a task ("we should do X next session"), it isn't a memory — create a task file in `.dev/tasks/backlog/` instead.

---

## Anti-patterns to avoid

1. **Duplicating between layers.** A fact lives in exactly one layer. If feedback ("don't mock the DB") is captured as a memory, don't also write it into a task file.
2. **Storing code in memory.** If it's code, it's in the repo. Memory references it; memory doesn't reproduce it.
3. **Letting MEMORY.md balloon.** It's an index. Every entry is one short line. If you want to summarize, write a per-topic file and link to it.
4. **Saving session activity as memory.** The remember plugin already does this. Auto-memory is for the *durable* lessons, not the daily diary.
5. **Treating memory as authoritative for live state.** Before acting on a remembered fact (file location, function name), verify it. Memory ages.
