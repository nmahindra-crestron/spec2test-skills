---
name: s2t-new
description: "Spec2Test pipeline stage 1 (intake). Invoke to start a new change: interview the user, produce a structured intake.md, and run Gate 0."
allowed-tools:
  - spec2test_info
  - create_work_unit
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-new** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

# s2t-new — Collect (Stage 1 of the Spec2Test pipeline)

Use this skill to start the Spec2Test pipeline for a new user story or feature. It creates a
**work unit** and produces a structured `intake.md`, then runs **Gate 0**.

You drive this through the Spec2Test engine tools — do not hand-create folders or slugs.

## What this skill does

1. Create a work unit (the engine mints the slug and folder, and makes it active).
2. Produce `intake.md` from the template in one pass, marking every missing field `[GAP]`.
3. Interview the user to resolve `[GAP]`s (critical ones first).
4. Run **Gate 0** and interpret its tri-state result.

## Step 1 — Create the work unit

Summarize the user's request into a short **change name** (3–6 words, lowercase-kebab-case).

Call `create_work_unit` with that name as the `title`. The engine allocates a unique slug of the
form `YYYY-MM-DD-<6×[A-Z0-9]>`, creates `.spec2test/changes/<slug>/`, and sets it active. All
subsequent tools act on this active unit. **Never invent a slug or path yourself.**

## Step 2 — Draft intake.md

Fill this template from whatever the user provided, then write it:

(Use the template in `intake-template.md`, included alongside this skill.)

Rules:

- Populate every section from the user's text. Set `Date` to today and `Slug` to the unit's slug.
- Any field or section with no information → write `[GAP]`. **Never leave a section blank.**
- Jira: if the user pastes Jira content, map it into the sections; otherwise leave **Jira Metadata**
  as `[GAP]`. (Live Jira fetch is not available — do not attempt network calls.)

Persist it with `persist_artifact` — `stageId: "intake"`, `content: <the filled markdown>`. When you
re-write `intake.md` on later turns (after interview answers), pass **`force: true`** so the engine
accepts the update instead of returning `reconcile-required`.

## Step 3 — Intake completeness interview

After the first write, inspect the sections still marked `[GAP]` and interview the user to resolve
them. Ask **critical** items first, then optional ones. Group related questions. Let the user reply
`[SKIP]` for anything unavailable. Update `intake.md` via `persist_artifact` (`force: true`) after
each round.

Critical sections (must be populated to pass Gate 0):

- **User Story**
- **Description**
- **Acceptance Criteria**

All other sections are non-critical: leaving them `[GAP]` does not fail the gate, but yields a
CONDITIONAL result that needs human sign-off.

Tag information the user supplies later with `[SUPPLEMENT]`.

## Step 4 — Run Gate 0

Count the remaining gaps in the current `intake.md`:

- `criticalGaps` = number of the three critical sections still unpopulated (`[GAP]`/empty).
- `nonCriticalGaps` = number of other sections still `[GAP]` (a section the user marked `[SKIP]`
  does not count).

Call `check_gate` — `stageId: "intake"`, `metrics: { "criticalGaps": { "value": <N> },
"nonCriticalGaps": { "value": <M> } }`. Interpret the returned status:

- **PASS** — critical sections populated and no non-critical gaps. The pipeline may advance; hand off
  to `s2t-analyze`.
- **CONDITIONAL_PASS** — non-critical gaps remain. Present them to the user. To advance, either
  resolve them (update intake, re-run Step 4) or record explicit sign-off with `approve_gate`
  (`stageId: "intake"`, `approved: true`). The pipeline does not advance until approval is recorded.
- **FAIL** — a critical section is still a gap. Resolve it, re-write `intake.md`, and re-run Step 4.
  Approval cannot lift a FAIL.

## Rules

- Never overwrite another unit's `intake.md`; always operate on the active unit and use `force: true`
  to update the intake you are building.
- Every section contains captured information or `[GAP]` — never blank.
- Do not advance to `s2t-analyze` until Gate 0 is PASS, or CONDITIONAL_PASS with a recorded approval.

