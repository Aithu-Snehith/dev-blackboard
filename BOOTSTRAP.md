# BOOTSTRAP — Setup Instructions for the Claude Agent

You are installing **dev-blackboard** — an in-repo blackboard architecture for AI coding agents — into a fresh project. Execute the steps below in order. Do not deviate unless something is already present and correctly configured.

The goal: by the end of this run, the project has a working blackboard (tasks, epics, handovers, specs, plans) that any agent — human or AI — reads from on session start and writes back to on session end.

---

## Step 0 — Sanity check

Before doing anything, verify:

```bash
pwd                      # confirm you're in the project root
git status               # confirm it's a git repo
ls .bootstrap/           # this kit should be here
```

If `.bootstrap/` is not present (kit was copied to a different path), ask the user where the kit lives. Otherwise continue.

---

## Step 1 — Create `.dev/` skeleton

```bash
mkdir -p .dev/tasks/active .dev/tasks/backlog .dev/tasks/done
mkdir -p .dev/epics .dev/handover
```

Copy templates:

```bash
cp .bootstrap/templates/TASK_TEMPLATE.md       .dev/TASK_TEMPLATE.md
cp .bootstrap/templates/EPIC_TEMPLATE.md       .dev/EPIC_TEMPLATE.md
cp .bootstrap/templates/HANDOVER_TEMPLATE.md   .dev/HANDOVER_TEMPLATE.md
cp .bootstrap/templates/DEV_README.md          .dev/README.md
```

Result: `.dev/` exists with three folder tiers + four template files.

---

## Step 2 — Create `docs/superpowers/` skeleton

```bash
mkdir -p docs/superpowers/plans docs/superpowers/specs
cp .bootstrap/templates/PLAN_TEMPLATE.md docs/superpowers/PLAN_TEMPLATE.md
cp .bootstrap/templates/SPEC_TEMPLATE.md docs/superpowers/SPEC_TEMPLATE.md
```

Result: a home for design docs (`specs/`) and step-by-step implementation plans (`plans/`).

---

## Step 3 — Install project-root `CLAUDE.md`

If `CLAUDE.md` already exists in the project root, **do not overwrite**. Instead:

1. Read the existing file
2. Read `.bootstrap/CLAUDE.md.template`
3. Merge: the *Task & Handover Workflow* section from the template MUST end up in the final `CLAUDE.md`. The rest (stack-specific instructions, build commands, gotchas) is project-owned and should be preserved.

If `CLAUDE.md` does **not** exist:

```bash
cp .bootstrap/CLAUDE.md.template CLAUDE.md
```

Then open it and replace these placeholders with project specifics. Ask the user only for things you can't infer from the repo:

- `<PROJECT_DESCRIPTION>` — 1-2 sentences. Infer from README.md / package.json / pyproject.toml if possible.
- `<DEV_COMMANDS>` — build/run/test commands. Infer from package.json scripts, Makefile, Justfile, etc.
- Domain-specific gotchas, architecture notes — leave empty placeholders; the user fills these as they go.

---

## Step 4 — Gitignore the short-term memory folder

The `remember` plugin writes to `.remember/` in the project root. Add to `.gitignore`:

```
.remember/
```

If `.gitignore` doesn't exist, create it. If `.remember/` is already listed, skip.

Note: the plugin auto-creates `.remember/` on its first save. Do not pre-create it.

---

## Step 5 — Tell the user how to enable optional pieces

Print this to the user (do not save it as a file):

```
Bootstrap complete. Optional next steps:

1. Short-term session memory (recommended):
     /plugin install remember@claude-plugins-official
     /plugin install superpowers@claude-plugins-official
     /plugin install skill-creator@claude-plugins-official
   (Once installed, they work in every project, not just this one.)

2. Disable auto-compact in Claude Code preferences (/config). The remember
   plugin needs the raw conversation; auto-compact discards it before save.

3. Long-term auto-memory: nothing to install. Claude Code writes to
     ~/.claude/projects/<project-slug>/memory/MEMORY.md
   on its own. See .bootstrap/MEMORY_GUIDE.md for the file conventions.

4. Review .bootstrap/REVIEW.md before doing your first task. It lists known
   rough edges and improvements worth applying on day one.
```

---

## Step 6 — Verify and clean up

```bash
ls -la .dev/                       # → 3 templates + README + tasks/ + epics/ + handover/
ls -la docs/superpowers/           # → plans/, specs/, 2 templates
grep -c "Task & Handover Workflow" CLAUDE.md   # → 1
grep -c "^\.remember/" .gitignore  # → 1
```

If all four checks pass, ask the user:

> "Bootstrap complete. Want me to delete `.bootstrap/` now? It's no longer needed."

Wait for confirmation before deleting.

---

## Step 7 (optional) — Seed the first task

If the user has a concrete first task in mind, offer to create the task file. Follow the standard task-creation flow described in `CLAUDE.md`:

1. Read `.dev/TASK_TEMPLATE.md`
2. Create `.dev/tasks/active/<slug>.md` with filled-in Goal / Context / Acceptance Criteria
3. Read it back to the user for review
4. Wait for confirmation

This double-purposes as a smoke test that the workflow works.

---

## What "done" looks like

- [x] `.dev/` exists with `tasks/{active,backlog,done}/`, `epics/`, `handover/`, and 4 template/README files
- [x] `docs/superpowers/{plans,specs}/` exists with two templates
- [x] `CLAUDE.md` contains the Task & Handover Workflow section
- [x] `.gitignore` ignores `.remember/`
- [x] User has been told how to enable optional plugins
- [x] `.bootstrap/` is deleted (after user confirms)

Hand off with a one-line summary: "Workflow shell installed. First task: pick an initial epic or drop your first task into `.dev/tasks/backlog/`."
