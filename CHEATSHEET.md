# Cheat Sheet — Daily Workflow

Print this. Stick it next to your editor. One page.

---

## Session start (every time)

```bash
# 1. Get a session ID
AUTHOR_HASH=$( { git config user.name; git config user.email; } | tr -d '\n' | shasum -a 256 | head -c 8 )
echo "SESSION: ${AUTHOR_HASH}-$(date -u +%Y%m%dT%H%M%SZ)"

# 2. See what's in flight
ls .dev/tasks/active/
ls .dev/epics/*/tasks/active/ 2>/dev/null
ls -t .dev/handover/ | head -3
```

Match the user's request to an active task. **No match → create a task file FIRST. Don't code yet.**

---

## Creating a new task

```bash
# Project-wide
cp .dev/TASK_TEMPLATE.md .dev/tasks/active/<slug>.md

# Inside an epic
cp .dev/TASK_TEMPLATE.md .dev/epics/<epic>/tasks/active/<slug>.md
```

Fill in: Goal, Context, Relevant Files, Acceptance Criteria. Show it to the user. Wait for confirmation.

---

## Creating a new epic

```bash
mkdir -p .dev/epics/<slug>/tasks/{active,backlog,done}
cp .dev/EPIC_TEMPLATE.md .dev/epics/<slug>/epic.md
```

Fill in: Goal, Scope (in/out), Plans, Tasks manifest, Acceptance Criteria.

---

## Spec → Plan → Tasks (for multi-step work)

1. `docs/superpowers/specs/YYYY-MM-DD-<slug>-design.md` — context, goals, architecture, decisions
2. `docs/superpowers/plans/YYYY-MM-DD-<slug>.md` — file structure + checkbox tasks
3. One task file per plan task, headers cross-link to plan + epic

---

## During work

- Update the task's **Session Log** after each meaningful chunk
- Session Log entry format:
  ```markdown
  ### YYYY-MM-DD — <session-id>
  - What was done
  - What's left
  - Blockers
  ```
- If you discover a footgun: add it to `CLAUDE.md` "Important Gotchas" so the *next* session doesn't repeat it

---

## Session end ("wrap up", "handover", "stop")

```bash
# 1. Append final Session Log entry to the task file
# 2. If task is done: git mv .dev/tasks/active/<slug>.md .dev/tasks/done/
# 3. Write handover
cp .dev/HANDOVER_TEMPLATE.md .dev/handover/$(date -u +%Y-%m-%d)-<topic>.md
# Fill: What Was Done / What's Left / Decisions / Gotchas / Commits
```

If `remember` plugin is installed, also run `/remember` for the auto-pipeline handoff note.

---

## Folder cheat sheet

```
.dev/
├── tasks/{active,backlog,done}/      ← cross-cutting tasks
├── epics/<slug>/                      ← major workstream
│   ├── epic.md
│   └── tasks/{active,backlog,done}/
├── handover/YYYY-MM-DD-<topic>.md     ← session summaries
└── *_TEMPLATE.md                       ← copy these

docs/superpowers/
├── specs/YYYY-MM-DD-<slug>-design.md   ← why & what
└── plans/YYYY-MM-DD-<slug>.md          ← how, with checkboxes

.remember/                              ← short-term, auto, gitignored
~/.claude/projects/<slug>/memory/       ← long-term, auto-loaded, NOT in repo
```

---

## Memory rules

| Question | Where it goes |
|----------|---------------|
| What did I do yesterday? | `.remember/` (auto) |
| Don't do X again | `~/.claude/projects/<slug>/memory/feedback_*.md` |
| Scope of feature X | `.dev/tasks/active/<slug>.md` |
| Why we picked approach Y | spec Decisions Log or epic Decisions Log |
| Where is auth code | the codebase — do NOT save |
| Active branch / last commit | git — do NOT save |

---

## Three rules to never break

1. **No PII in tracked files.** Use the session-ID author hash. Never the user's name/email.
2. **No code in memory.** Memory points at the code; it doesn't reproduce it.
3. **No work without a task.** If the request doesn't match an existing task, create one and get it reviewed before touching code.

---

## Commit message convention

```
<scope>: <short imperative summary>

<optional body — the "why">

Co-Authored-By: Claude Opus 4.X (1M context) <noreply@anthropic.com>
```

Never add a second human Co-Authored-By with the user's name.

---

## Common slash commands (if plugins installed)

| Command | Purpose |
|---------|---------|
| `/remember` | Write a handoff note for the next session (remember plugin) |
| `/loop <interval> <command>` | Run a task on a recurring interval |
| `/plugin` | Install / list / update plugins |
| `/config` | Claude Code preferences — disable auto-compact for the `remember` plugin to work |
| `/statusline` | Show context usage — when high, save and start new session |
