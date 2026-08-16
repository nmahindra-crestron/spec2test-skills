---
name: s2t-hydrate
description: "Spec2Test pipeline stage 5 (hydrate). Invoke on approved artifacts to update project memory and record hydration-summary.md."
allowed-tools:
  - spec2test_info
  - check_memory_conflicts
  - hydrate_memory
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-hydrate** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

---
name: s2t-hydrate
description: "Spec2Test pipeline stage 5 (hydrate). Invoke on approved artifacts to update project memory and record hydration-summary.md."
allowed-tools:
  - spec2test_info
  - check_memory_conflicts
  - hydrate_memory
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-hydrate** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call `spec2test_info`. If unavailable, stop and ask the user to install/update Spec2Test. If
version is below `0.2.0`, stop and ask for upgrade.

---

# s2t-hydrate — Hydrate Memory (Stage 5 of the Spec2Test pipeline)

Use this skill after `s2t-export` to persist durable behavioral memory at `docs/s2t-memory/`.
This stage is manual and user-invoked. It must never auto-trigger.

## Step 1 — Read stage artifacts

Read the active unit artifacts:
- `analysis.md`
- `test-cases.md`

If either is missing or predecessor gates are not advancement-permitting, stop and report the
blocking reason.

## Step 2 — Derive candidate patterns

From the current artifacts, derive behavior entries in this structure:
- `domain`
- `category` in `Functional|Boundary|Negative|Interaction`
- `pattern`
- `sourceSlug` (active unit slug)
- optional `relatedDomains`

Focus on behaviors that materially impact future test design and interaction risk.

## Step 3 — Check conflicts before merge

Call `check_memory_conflicts` with the candidate patterns.

Conflict policy is **non-blocking**:
- Contradictions, overlaps, and interaction risks must be recorded.
- Do **not** stop hydration because of conflicts.

## Step 4 — Hydrate memory

Call `hydrate_memory` with the same candidate patterns.

Expected side effects:
- Creates/updates `docs/s2t-memory/behaviors/*.md`
- Creates/updates canonical interaction files in `docs/s2t-memory/interactions/`
- Updates `docs/s2t-memory/coverage/coverage-map.md`
- Regenerates `docs/s2t-memory/index.md`

## Step 5 — Produce hydration-summary.md

Build `hydration-summary.md` from the template `hydration-summary-template.md` and include:
- domains updated
- added/updated/skipped counters
- contradictions/overlaps/interaction risk counts
- warnings (including any >500 line domain warning)

Persist with `persist_artifact` using:
- `stageId: "hydrate"`
- `content: <hydration-summary.md>`

If this is a rewrite, use `force: true`.

## Step 6 — Run gate

Call `check_gate` with `stageId: "hydrate"`.
- PASS: hydrate complete
- CONDITIONAL_PASS: present non-blocking issues and request explicit approval
- FAIL: fix summary/contract issues, rewrite, and re-run

## Rules

- Memory path is fixed: `docs/s2t-memory/`.
- Keep interaction filenames canonical and alphabetically ordered.
- Hydration is deterministic and idempotent for equivalent input.
- Do not delete prior memory entries automatically.

