# Review — Things to Improve on Day One

This is the honest "lessons learned" file. Read it before doing your first task. Most of these are small; together they sharpen the system. They all came from running an earlier version of this kit in a real production codebase.

## Things that work well (keep them)

- **Two-tier task structure** — `.dev/tasks/` for cross-cutting, `.dev/epics/<slug>/` for major workstreams. Scales from one task to a 100-task engagement.
- **Mandatory session-start protocol in `CLAUDE.md`** — kills the "what was I doing again?" tax.
- **Session ID** = `<author-hash>-<timestamp>`. Stable per contributor, no PII. Excellent forensic signal in session logs.
- **Spec → plan → tasks pipeline.** Specs answer "why & what", plans answer "how with checkboxes", tasks are the units of work. Each task can be one subagent dispatch.
- **Handover docs.** The single biggest cure for re-discovering context next session.
- **No-PII rule.** Repo stays portable and shareable.

## Rough edges worth fixing on day one

### 1. The session-ID command silently fails if git identity isn't set

A naive version:
```bash
AUTHOR_HASH=$(echo -n "$(git config user.name)$(git config user.email)" | shasum -a 256 | head -c 8)
```
If `git config user.email` is unset (fresh clone on a new machine), `AUTHOR_HASH` becomes the hash of an empty string and *every contributor looks identical*. The kit's `CLAUDE.md.template` already includes a fallback to `anon0000` and warns the user once. Don't drop that.

### 2. Two memory layers can drift without a written boundary

`.remember/` (auto-pipeline from the `remember` plugin) and `~/.claude/projects/.../memory/` (Claude Code's built-in auto-memory) both exist; nothing in the project explains which is which by default. The kit includes `MEMORY_GUIDE.md` — put it somewhere the user will actually read (link it from `.dev/README.md` and/or `CLAUDE.md`).

### 3. Plans live in `docs/`, tasks live in `.dev/`. Cross-links rot.

Epic-scoped tasks include `## Plan:` and `## Plan Task:` headers — great. But plan files don't link *back* to their tasks. When a task moves between `backlog/active/done`, the plan checkbox can fall out of sync.

**Rule worth adopting:** when a plan task is started, the implementer flips the checkbox in the plan AND moves the task file in the same commit. Add this to the executing-plans workflow.

### 4. `Status:` field on task files is ad-hoc

Tasks can carry a `## Status:` header with values like `backlog | active | ready-for-review | done`, but the canonical signal is which subfolder the file lives in. Two sources of truth = one will lie.

**Pick one of:** (a) drop the `## Status:` header and let the folder be canonical, or (b) keep the header and add a pre-commit hook that asserts the header matches the folder. The kit's `TASK_TEMPLATE.md` keeps the header for now (least disruptive); your call.

### 5. No "stale tasks" surveillance

A task can sit in `active/` for weeks because nobody noticed. Suggested: on session start, after the `ls active/` check, flag any task whose latest Session Log entry is > 14 days old. One line of output, no action required.

### 6. Ad-hoc tracking files have a way of sticking around

Things like a one-off `.dev/dead-code.md` tracking a migration's deletions tend to outlive their purpose. Either make them proper standing docs with a clear schema, or inline the tracking into the relevant task and delete the file when the task closes. Don't create them by reflex.

### 7. `CLAUDE.md` grows unbounded

Workflow rules + stack gotchas + architecture notes will all want to live in `CLAUDE.md`. Before long it's 280 lines and new readers have to scroll past 100+ lines of workflow before reaching "what is this codebase". The kit's template puts the workflow first (so the rule lands), but keep the *project description + dev commands + gotchas* portion lean. When `CLAUDE.md` grows past ~200 lines, split: keep `CLAUDE.md` for workflow + 10-line orientation, push the rest into `docs/architecture.md` and link.

### 8. No template for the `/remember` plugin handoff

The `remember` plugin's `/remember` command writes a handoff note, but there's no convention on what it should contain. If you want it to be useful, mirror the structure of `.dev/handover/`: "What Was Done / What's Left / Decisions / Gotchas". Tell Claude this in `CLAUDE.md` so `/remember` notes have shape.

### 9. Plans assume the `superpowers` plugin

The plan template references `superpowers:executing-plans` and `superpowers:subagent-driven-development`. If the plugin isn't installed, the references are meaningless. The kit's template phrases it as "if installed" — keep that hedge. The checkbox format itself is the durable part; the skill names are nice-to-haves.

### 10. Subagent owners should be traceable

When dispatching subagents for plan tasks, it's tempting to use generic aliases like `subagent-task-02`, `subagent-task-03`. These don't tie back to the session ID of the dispatching agent — forensic traceability suffers.

**Better:** set Owner to the *dispatching* session ID. Subagents inherit it. Drop the generic aliases.

## Things to NOT change

- The Session Log format. Concise, dated, session-tagged. It works.
- The Spec / Plan / Task three-layer separation. Looks heavyweight but pays off the second time you onboard someone to an in-flight feature.
- The `.gitignore` for `.remember/`. The plugin's auto-pipeline output is noisy and per-machine.

## Open question

Running *both* `.remember/` (compressed session log via the plugin) AND `.dev/handover/` (manual session summary) means they overlap. In a new project, you could:

- **Use both** — auto-pipeline for "what literally happened", manual handover for "what matters". The redundancy has saved real time at least once.
- **Drop manual handovers** — rely entirely on `/remember` + the auto-pipeline. Lower discipline cost but no curated handoff.
- **Drop the auto-pipeline** — keep manual handovers only. No Haiku spend, simpler.

Recommendation: keep both for the first month, then decide based on which one you actually read at the start of session N+1.
