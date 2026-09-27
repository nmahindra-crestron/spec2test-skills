---
name: s2t-export
description: "Spec2Test pipeline stage 4 (export). Invoke on approved test-cases to validate export readiness and deliver the xlsx; records export-summary.md."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - run_export
---

You are executing the **s2t-export** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

---
name: s2t-export
description: "Spec2Test pipeline stage 4 (export). Invoke on approved test-cases to validate export readiness and deliver the xlsx; records export-summary.md."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - run_export
---

You are executing the **s2t-export** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

# s2t-export — Deliver (Stage 4 of the Spec2Test pipeline)

Use this skill after `s2t-generate` produced approved test cases. It validates export readiness and
delivers an **xlsx** export. It NEVER creates, modifies, or adds test cases or coverage — validation,
transformation, and export only.

Drive everything through the engine tools.

## Step 1 — Validate readiness

Confirm the active unit's `test-cases.md` is ready to export:

- Gate 2 is PASS or approved CONDITIONAL_PASS (the engine also blocks stage 4 and the export tool
  until this holds).
- Every test case has the required export-facing data for the approved xlsx layout (Test Case ID,
  Name, User Story, Test Repository Path, Test Type, Feature Area, Requirement ID, Setup Details,
  Pre-requisite, Purpose, Step Action, Expected Results). For interaction test cases (those whose
  Test Type includes `Interaction`), also confirm `Interaction Type`, `Interaction Ref`, and
  `Existing Feature(s)` are present; these export into dedicated columns (blank for non-interaction
  cases). Export never creates or alters coverage — it only validates and transforms.
- Traceability is intact (each test case traces to REQ/BR/VR/approved source) and Coverage Intent is
  valid and matches its source.

If readiness fails, stop and report the specific failures — do not export.

## Step 2 — Export to xlsx

Call the `run_export` tool with the format spec `test-cases-xlsx@1`. The engine:

- blocks if `test-cases.md` violates its contract (Principle III),
- blocks unless the `generate` (Gate 2) gate permits advancement,
- otherwise parses `test-cases.md` and writes `export/test-cases-xlsx@1.xlsx` — a derived,
  **gitignored** deliverable (never committed).

The column set lives entirely in the format spec (content) — changing columns is a content edit.

### Step 2A — Rename the exported xlsx to include the story identifier

After the `run_export` tool succeeds, rename the output file so it is identifiable by story. Derive
`$storyId` from the intake's `Number` field (e.g. `CONSIM-2617` in Jira-key mode, or the change
title in manual mode). Run these terminal commands (substitute actual values):

```
$exportDir = ".spec2test\changes\<slug>\export"
$storyId   = "<Jira issue key or change title>"    # e.g. CONSIM-2617
$src  = "$exportDir\test-cases-xlsx@1.xlsx"
$dest = "$exportDir\test-cases-$storyId.xlsx"

Rename-Item $src $dest
```

Use `$dest` as the output path in the export-summary.md `Output File` field.

## Step 3 — Record the export summary

Build `export-summary.md` from this template and persist it as the stage-4 artifact (the committed
record of the run; the xlsx itself stays gitignored):

(Use the template in `export-summary-template.md`, included alongside this skill.)

Write it with `persist_artifact` — `stageId: "export"` — then `check_gate(stageId: "export")` to
record the export gate (artifact present + contract valid).

## Rules

- Never create new test cases, add coverage, or alter test intent or traceability.
- Never silently ignore validation failures.
- Require human approval when Gate 2 is CONDITIONAL_PASS or warnings exist.
- Exports are derived and gitignored; only the markdown artifacts (`test-cases.md`,
  `export-summary.md`) are the committed source of truth.
- No network access.

