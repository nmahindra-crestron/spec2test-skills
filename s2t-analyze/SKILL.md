---
name: s2t-analyze
description: "Spec2Test pipeline stage 2 (analysis). Invoke on an approved intake to produce an evidence-backed analysis.md and run Gate 1."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - check_memory_conflicts
---

You are executing the **s2t-analyze** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

---
name: s2t-analyze
description: "Spec2Test pipeline stage 2 (analysis). Invoke on an approved intake to produce an evidence-backed analysis.md and run Gate 1."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - check_memory_conflicts
---

You are executing the **s2t-analyze** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

# s2t-analyze — Analyze (Stage 2 of the Spec2Test pipeline)

Use this skill after `s2t-new` completed intake and Gate 0. It transforms the active work unit's
approved `intake.md` into a structured, evidence-backed QE analysis (`analysis.md`) — the primary
input to test-case generation — then runs **Gate 1**.

Drive everything through the Spec2Test engine tools; do not hand-manage folders, slugs, or gates.

## Step 1 — Read the approved intake

Read `intake.md` from the **active work unit**. You do not need to re-check Gate 0: the engine
blocks `persist_artifact` for the `analysis` stage until the intake gate is PASS or an approved
CONDITIONAL_PASS (it will name `intake` if you try too early). If no unit is active, stop and tell
the user to run `s2t-new` or switch to a unit.

## Step 2 — Produce analysis.md

Build `analysis.md` from this template, filling it from the intake plus documented evidence:

(Use the template in `analysis-template.md`, included alongside this skill.)

The template includes headings such as `## Evidence Register`, `## Atomic Requirements`, and
`## Gate Result`.

Core discipline (authoritative):

- **Evidence Register**: every Requirement (REQ), Business Rule (BR), and Validation Rule (VR) must
  trace to documented evidence (source type + location).
- **Assumptions Register (ASM)**: keep assumptions separate; they NEVER promote to REQ/BR/VR.
  Requirements come only from documented evidence; unsupported statements move to Assumptions.
- Populate the mandatory inventories (Enumerated Value Inventory `INV`, Behavioral Coverage
  Inventory `BHV`) and Coverage Hardening Signals; missing evidence creates a Requirement Gap and a
  Clarification Question, not a Requirement.
- Hidden Test Opportunities are **advisory only** — never in the Traceability Matrix, never scored.
- Never leave a required section blank; mark unknowns `[GAP]`. Tag later additions `[SUPPLEMENT]`.

Write it with `persist_artifact` — `stageId: "analysis"`, `content: <the filled markdown>`. When you
rewrite `analysis.md` on later turns, pass **`force: true`** so the engine accepts the update instead
of returning `reconcile-required`.

## Step 3 — Resolution interview loop

Detect gaps, conflicts, ambiguities, and unconfident assumptions. Interview the user to resolve them
(HIGH-severity gaps and conflicts first; group related questions; allow `[SKIP]`). After each round,
update the dependent sections (Atomic Requirements, Business/Validation Rules, Coverage Intent,
Traceability, Summary) and re-write `analysis.md` via `persist_artifact` (`force: true`). Continue
until items are resolved or explicitly `[SKIP]`-deferred.

**Questions MUST be asked using the `vscode_askQuestions` tool** — never stated as plain text.
Each item must be a distinct question entry. Every question is mandatory; the user must answer
explicitly or reply `[SKIP]`. Re-ask unanswered questions before proceeding.

**Image attachments are welcome**: for any question where a screenshot, diagram, or UX spec would
help, tell the user in the question text that they may attach an image directly in their chat reply.
After the user responds, inspect their chat message for attached images and extract relevant
information (UI elements, AC items, field names, flow diagrams, etc.) exactly as you would from a
typed answer. Treat image-derived content as `[SUPPLEMENT]` and note the source as
"user-attached screenshot".

## Step 3A — Memory regression check (recommended)

If `docs/s2t-memory/index.md` exists, derive candidate domain patterns from the current analysis and
call `check_memory_conflicts` before finalizing Gate 1. Record any contradictions or interaction
risks in the analysis output as advisory risks so downstream generation can preserve compatibility.

## Step 4 — Run Gate 1

Complete the **Gate Result** section (Status, Quality Score 0–100, Blocking/Non-Blocking Issues),
then compute these metrics from the current `analysis.md` and hand them to the gate:

- `unsupportedItems` = count of REQ/BR/VR **not** backed by an Evidence Register row.
- `openConflicts` = Conflict entries with status `OPEN` (exclude `RESOLVED`/`SKIP`).
- `highGaps` = Requirement Gaps with severity `HIGH` and status `OPEN`.
- `qualityScore` = your 0–100 Gate Result score (90-100 excellent / 75-89 good / 50-74 moderate /
  0-49 poor).
- `openAdvisoryItems` = remaining non-critical items = MEDIUM/LOW `OPEN` gaps + `UNRESOLVED`
  ambiguities + non-`CONFIDENT` assumptions + `[SKIP]`-deferred items.

Call `check_gate` — `stageId: "analysis"`, `metrics: { "unsupportedItems": {"value": N}, ... }`.
Interpret the returned status:

- **PASS** — all blocking checks satisfied and no advisory issue. Hand off to `s2t-generate`.
- **CONDITIONAL_PASS** — evidence is intact but the analysis is imperfect (moderate score, or
  remaining non-critical items). The pipeline does **not** advance until you record human sign-off:
  resolve items and re-run Step 4, or call `approve_gate` (`stageId: "analysis"`, `approved: true`)
  once the user accepts the non-blocking issues. A decline (`approved: false`) does not advance.
- **FAIL** — a blocking check failed (unsupported REQ/BR/VR, an open conflict, an open HIGH gap, or
  a poor quality score < 50). Fix it, re-write `analysis.md`, and re-run Step 4. Approval cannot lift
  a FAIL.

Keep your recorded Gate Result section consistent with the metrics you pass to `check_gate`.

## Rules

- Operate only on the active unit; use `force: true` to update the `analysis.md` you are building.
- Requirements only from documented evidence; assumptions never become requirements.
- Do not advance to `s2t-generate` until Gate 1 is PASS, or CONDITIONAL_PASS with a recorded approval.
- No network access; use Jira relationships only if already present in `intake.md`.

