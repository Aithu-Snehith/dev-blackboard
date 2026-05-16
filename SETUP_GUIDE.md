# Setup Guide — Copy this prompt into Claude in your new project

This is the literal prompt to paste once you've copied dev-blackboard into the new project. Steps below.

---

## Step 1 — Copy the kit

From a terminal:

```bash
cp -r /path/to/dev-blackboard /path/to/your-new-project/.bootstrap
```

(Replace both paths with real ones — the source is wherever you cloned this kit, the destination is the root of the project you want to set up.)

## Step 2 — Open Claude Code in the new project

```bash
cd /path/to/your-new-project
claude
```

## Step 3 — Paste this prompt

```
I want to install **dev-blackboard** — an in-repo blackboard architecture for AI coding agents — in this project. I've copied the kit to `.bootstrap/` in this repo.

Do the following:

1. Read `.bootstrap/README.md` and `.bootstrap/BOOTSTRAP.md` end-to-end so you have the full picture.
2. Read `.bootstrap/REVIEW.md` — those are improvements I want applied here, not rough edges to import.
3. Execute `BOOTSTRAP.md` steps 1 through 6 in order:
   - Create the `.dev/` skeleton with the templates
   - Create the `docs/superpowers/` skeleton with the templates
   - Install or merge `CLAUDE.md` at the project root (preserve any existing content; the Task & Handover Workflow section MUST end up in it)
   - Gitignore `.remember/`
   - Tell me how to enable optional plugins
   - Verify with the checks at the bottom of BOOTSTRAP.md

4. When filling the placeholders in `CLAUDE.md` (`<PROJECT_DESCRIPTION>`, `<DEV_COMMANDS>`):
   - Infer from README, package.json, pyproject.toml, Makefile, Justfile, etc.
   - Only ask me for things you genuinely can't infer
   - Leave the gotchas section empty — I'll fill it as I go

5. After verification passes, DO NOT delete `.bootstrap/` yet. Show me the diff (`git status` + `git diff --stat`) so I can review.

6. Once I confirm it looks good, delete `.bootstrap/` and commit everything with this message:

   chore: install dev-blackboard (tasks, handovers, decisions)

   - .dev/ blackboard with task / epic / handover templates
   - docs/superpowers/ for specs and plans
   - CLAUDE.md session-start protocol + session-id command
   - .remember/ gitignored for the remember plugin

   Co-Authored-By: Claude <noreply@anthropic.com>

Important rules to follow during this setup AND all future work in this project:
- No PII: never write my real name or email into any tracked file. Use the session-ID author hash for owner fields.
- No work without a task: from the next prompt onward, if my request doesn't match an existing task in `.dev/`, you create a task file first and show it to me before coding.
- Update Session Logs after each meaningful chunk of work.
- On session end, write a handover doc to `.dev/handover/`.

Start now. Tell me the session ID you generated for this setup session so I know what to look for in the logs.
```

---

## Step 4 — After setup completes

Optional but recommended, run these in Claude Code (in any project — they apply globally):

```
/plugin install remember@claude-plugins-official
/plugin install superpowers@claude-plugins-official
/plugin install skill-creator@claude-plugins-official
/config
```

In `/config`, disable auto-compact. (The `remember` plugin needs the raw conversation to summarize it.)

## Step 5 — Try a first task

Paste this to verify the workflow:

```
I want to add <feature X>. Walk me through it using the task workflow we just set up — create a task file first, show it, and don't start coding until I confirm.
```

If Claude creates a `.dev/tasks/active/<slug>.md` and reads it back to you before touching code, the workflow is live.

---

## What you'll have after Step 3

```
your-new-project/
├── CLAUDE.md                         ← workflow + your project specifics
├── .gitignore                        ← .remember/ added
├── .dev/
│   ├── README.md
│   ├── TASK_TEMPLATE.md
│   ├── EPIC_TEMPLATE.md
│   ├── HANDOVER_TEMPLATE.md
│   ├── tasks/{active,backlog,done}/  (empty, ready)
│   ├── epics/                        (empty, ready)
│   └── handover/                     (empty, ready)
└── docs/
    └── superpowers/
        ├── PLAN_TEMPLATE.md
        ├── SPEC_TEMPLATE.md
        ├── plans/                    (empty, ready)
        └── specs/                    (empty, ready)
```

Plus `.bootstrap/` until you tell Claude to delete it.
