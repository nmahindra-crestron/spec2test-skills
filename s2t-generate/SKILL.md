---
name: s2t-generate
description: "Spec2Test pipeline stage 3 (generate). Invoke on an approved analysis to produce traceable test-cases.md and run Gate 2."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-generate** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

# s2t-generate — Design Tests (Stage 3 of the Spec2Test pipeline)

Use this skill after `s2t-analyze` produced an approved `analysis.md`. It turns the QE analysis into
meaningful, workflow-oriented, fully traceable test cases (`test-cases.md`), then runs **Gate 2**.

Drive everything through the engine tools; do not hand-manage folders or gates.

## Step 1 — Read the approved analysis

Read `analysis.md` from the **active work unit**. You need not re-check Gate 1: the engine blocks
`persist_artifact` for the `generate` stage until the analysis gate is PASS or approved
CONDITIONAL_PASS (it names `analysis` if you try too early).

## Step 2 — Design and write test-cases.md

Build `test-cases.md` from this template:

(Use the template in `test-cases-template.md`, included alongside this skill.)

Design discipline (authoritative):

- Prefer **workflow-oriented** test cases that combine related REQ/BR/VR; minimize count while
  maximizing coverage. Do not generate one test case per requirement.
- Every test case traces to at least one REQ/BR/VR/approved source; every referenced `REQ-xxx`
  exists in `analysis.md`. Requirements are never invented.
- Cover Coverage Intent per the analysis matrix; satisfy Enumerated Value Inventory (FULL) and
  Behavioral Coverage Inventory; generate boundary coverage for validation limits.
- **Exploratory / Hidden coverage** is tagged `EXPLORATORY`, never counts toward mandatory coverage,
  and never appears in the Traceability Matrix.
- Test data must be concrete; expected results observable; steps reproducible.
- Preserve the authored fields needed by the approved xlsx export: `User Story`, `Repository Path`,
  `Test Type`, `Feature Area`, `Requirement(s)`, `Setup Details`, `Pre-requisite`, `Purpose`, and
  a `Steps` table that keeps `Action` and `Expected Result` as distinct columns.

**Formatting rule (parser-critical):** write each test case title as a plain `Name: <value>` at the
**start of the line, with no bold markers** (never `**Name:**`). The export runner parses this label;
a bold or indented `Name` fails the contract and Gate 2 cannot pass.

Write it with `persist_artifact` — `stageId: "generate"`, `content: <the filled markdown>`. On
regeneration/interview rewrites, pass **`force: true`**.

## Step 3 — Run Gate 2

Complete the Gate 2 Result + Coverage Validation sections, then compute these metrics and hand them
to the gate:

- `untracedTestCases` = test cases not tracing to any REQ/BR/VR/approved source.
- `requirementCoverage`, `businessRuleCoverage`, `validationRuleCoverage` = % of each covered
  (report `100` when none of that kind exist).
- `evidenceTraceability` = % of coverage items backed by analysis evidence.
- `inventoryCoverage` = % of FULL Enumerated/Behavioral inventory items covered.
- `openGenerationWarnings` = count of carried-over open gaps/conflicts/ambiguities, remaining
  assumptions, or partially-covered inventories recorded as Generation Warnings.

Call `check_gate` — `stageId: "generate"`, `metrics: { "untracedTestCases": {"value": N}, ... }`.
Interpret the status:

- **PASS** — full coverage, full traceability, no warnings, formatting valid. Hand off to
  `s2t-export`.
- **CONDITIONAL_PASS** — coverage complete but generation warnings remain. Present them; advance only
  after `approve_gate` (`stageId: "generate"`, `approved: true`), or resolve and re-run.
- **FAIL** — missing mandatory coverage, an untraced test case, a dangling requirement reference, or
  a contract/`Name:`-formatting violation. Fix and re-run; approval cannot lift a FAIL.

## Rules

- Operate only on the active unit; use `force: true` to update `test-cases.md`.
- Never invent requirements; never let exploratory coverage satisfy mandatory coverage.
- Do not advance to `s2t-export` until Gate 2 is PASS, or CONDITIONAL_PASS with a recorded approval.
- No network access.

