# <Plan Title> — Implementation Plan

> **For agentic workers:** If the `superpowers` plugin is installed, use `superpowers:subagent-driven-development` or `superpowers:executing-plans` to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** <one-sentence outcome>

**Architecture:** <two-three sentences on the approach — what pattern, what's new, what's reused>

**Tech Stack:** <list the relevant frameworks / runtimes / tools the implementer needs to know>

**Spec:** `docs/superpowers/specs/YYYY-MM-DD-<slug>-design.md`

**Epic:** `.dev/epics/<slug>/epic.md` (if applicable)

**Key design constraints:**

- <constraint 1, e.g. "Workers do not hot-reload — restart after each change">
- <constraint 2, e.g. "Migrations must be idempotent with both up and down blocks">
- <constraint 3, e.g. "No backward-compat: clean cutover acceptable">

---

## File Structure

**Create:**
- `path/to/new/file.ext` — <one-line purpose>

**Modify:**
- `path/to/existing/file.ext:line` — <what changes>

**Delete:**
- `path/to/dead/file.ext` — <why>

---

## Tasks

<!-- Each top-level checkbox is one task. Sub-bullets are within-task notes
     for the implementer. Number tasks so they can be referenced from the
     epic manifest and from sub-task files via `## Plan Task: #N`. -->

### 1. <task title>

- [ ] <concrete step>
- [ ] <verification step — "import passes", "test added and green", etc.>

**Files:** `path/a`, `path/b`
**Verification:** `<command to run>`
**Commit:** `<scope>: <message>`

### 2. <task title>

- [ ] ...

<!-- Repeat. Aim for tasks small enough to fit in a single subagent dispatch
     (≤ 5 files, ≤ 200 lines of diff, ≤ 1 hour of human review). -->

---

## Acceptance Criteria

- [ ] All task checkboxes above are checked
- [ ] <plan-level rollup, e.g. "End-to-end smoke runs cleanly on demo org">
- [ ] <plan-level rollup, e.g. "No new lint or type errors">

---

## Risks / Open Questions

- <risk or unknown that could derail the plan and how you'd mitigate>
