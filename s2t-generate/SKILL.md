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
- **All eight test types are mandatory for every requirement — no skipping**: Functional,
  Negative, Edge, Boundary, Performance, Stress, Exploratory, Interaction. Generate at least one
  test case of each type per requirement (workflow-combined test cases may satisfy several
  requirements and several types at once, but every type must appear somewhere in the traced
  coverage for every requirement).
- **Record each test case's types in the multi-value `Test Types:` field** (comma-separated) and its
  covered requirements in the `Requirement(s):` field. Gate 2 cross-references the `analysis.md`
  Coverage Intent Matrix against these fields: any requirement marked `Y` for a test type with no
  test case declaring that type and referencing that requirement is a **blocking** Gate 2 failure.
  A single workflow-combined case may declare several of the eight types to cover multiple matrix
  cells at once.
- If `analysis.md` does not make clear how a mandatory test type applies to a requirement (e.g. no
  performance target, no stress volume, no defined interaction partner), do not guess or invent
  test data — use `vscode_askQuestions` to ask the user for the missing detail before writing that
  test case, then persist the clarified answer into the relevant field.
- **Record every interview answer in `clarifications.md`** (the shared input log), never as raw prose
  in `test-cases.md`. Append a row to its `## Clarification Log` table via `persist_artifact`
  (`stageId: "generate"`, `artifact: "clarifications.md"`, `force: true`): `ID` (`CLR-###`, unique,
  increasing across the file) · `Stage` = `generate` · `Recorded At` (ISO-8601 UTC) · `Recorded By`
  (from `git config user.name`, fallback `user.email`, fallback `user`) · `Target`
  (`REQ-xxx / <TestType>`) · `Resolution` (`Y`/`N`/`SKIP`/`ANSWERED`) · verbatim `Question`/`Answer`
  (encode newlines `<br>`, pipes `\|`) · `Source`. If `clarifications.md` is absent, create it first
  from `clarifications-template.md` (included alongside this skill).
- **Never self-assign a coverage deferral.** You may only record a matrix cell as `SKIP` after the
  user explicitly authorizes deferring that requirement/test-type combination; record it back to the
  `analysis.md` Coverage Intent Matrix (via `s2t-analyze`) AND as a `Resolution: SKIP` row in
  `clarifications.md` (whose `Target` names the requirement/test-type), never silently in
  `test-cases.md`. Every matrix `SKIP` MUST have a backing `SKIP` clarification row or the gate fails
  (`cross_reference_backed`). A `SKIP` cell surfaces at Gate 2 as an advisory item and downgrades the
  gate to CONDITIONAL_PASS, which requires a recorded approval to advance.
- **Exploratory / Interaction coverage still requires traceability**: tag exploratory cases
  `EXPLORATORY` and interaction cases `INTERACTION`; both must still trace to at least one
  REQ/BR/VR/approved source and appear in the Traceability Matrix — they are mandatory coverage
  now, not advisory-only.
- **Expand feature-interaction outlines (strict 1:1)**: for every `INT-###` row in the `analysis.md`
  **Feature Interaction Analysis** table, author exactly one interaction test case (one test case per
  `INT-###`, one `Interaction Ref` per test case). Set `Test Types` to include `Interaction`, copy the
  row's classification into `Interaction Type` (one of the twelve values), set `Interaction Ref` to the
  originating `INT-###`, and set `Existing Feature(s)` from the row's Existing Feature. Seed the case's
  `Purpose` from the row's Objective, `Pre-requisite` from its Pre-conditions, and the `Steps` from its
  Interaction Steps / Expected Result (expanded to the 6–15 step discipline below). Write these three
  fields at line start with no bold markers (parser-critical, like `Name:`). This carry-through is
  **advisory**: a missing expansion never FAILs Gate 2 (see Step 3).
- Test data must be concrete; expected results observable; steps reproducible.
- **Steps count rule**: every test case's `### Steps` table must have a **hard minimum of 6 data
  rows and a maximum of 15 data rows** (aim for 6-7; the header and separator rows are not counted).
  Gate 2 enforces these bounds and fails below 6 or above 15. Each row is one `Action` paired with a
  non-empty, observable `Expected Result` — never leave `Expected Result` blank (a blank cell is a
  blocking Gate 2 failure). If a scenario is too small to reach 6 steps on its own, combine it with
  related steps/assertions from the same workflow rather than padding with trivial actions; if it
  would exceed 15, split into multiple test cases instead of cramming more rows in.
  Preserve the authored fields needed by the approved xlsx export and Gate 2 coverage check:
  `User Story`, `Repository Path`, `Test Types`, `Feature Area`, `Requirement(s)`, `Setup Details`,
  `Pre-requisite`, `Purpose`, and a `Steps` table that keeps `Action` and `Expected Result` as
  distinct columns.

**Setup Details rule**: the `Setup Details` field in every test case must contain a human-readable
inline description of the setup steps — never just a reference token such as `SETUP-001`. Copy or
summarise the relevant steps from the Common Setup Library inline. The export parser reads this
field directly; a bare token is not useful to a tester.

**Build/version number rule**: do not include specific build numbers, version strings, or release
identifiers (e.g. "build 1.1500.0021") in setup steps, pre-requisites, or any test case field.
State the application name only (e.g. "Launch SIMPL Windows"). Version requirements belong in the
Caveats section of the Generation Summary, not in individual test steps.

**Post Conditions export rule**: include `### Post Conditions` sections in `test-cases.md` for
reviewer clarity, but append the HTML comment `<!-- export:exclude -->` on the same line as the
heading so the export stage omits them from the output file:
`### Post Conditions <!-- export:exclude -->`

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

**Feature-interaction carry-through (advisory)**: Gate 2 also runs an advisory `reference_chain` that
checks every `INT-###` row in `analysis.md` is referenced by an `Interaction Ref` in `test-cases.md`
(and, optionally, that no `Interaction Ref` is dangling). A missing or dangling interaction reference
is a **non-blocking** issue that yields CONDITIONAL_PASS (requiring recorded approval to advance) — it
NEVER causes a FAIL. It coexists with the blocking `REQ-###` `reference_chain` and the `matrix_coverage`
check for the Interaction test-type column, which are unchanged.

## Rules

- Operate only on the active unit; use `force: true` to update `test-cases.md`.
- Never invent requirements; every test case (including Exploratory and Interaction) must trace to
  a REQ/BR/VR/approved source — no untraceable coverage.
- All eight test types (Functional, Negative, Edge, Boundary, Performance, Stress, Exploratory,
  Interaction) are mandatory coverage — none may be skipped or omitted.
- Do not advance to `s2t-export` until Gate 2 is PASS, or CONDITIONAL_PASS with a recorded approval.
- No network access.

