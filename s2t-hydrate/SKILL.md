---
name: s2t-hydrate
description: "Spec2Test hydrate. Pipeline stage 5, plus ingest (folder / Jira) and backfill modes to build project memory at docs/s2t-memory/."
allowed-tools:
  - spec2test_info
  - validate_memory
  - generate_memory_index
  - backfill_memory
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
description: "Spec2Test hydrate. Build and maintain project memory at docs/s2t-memory/ in three modes — pipeline (stage 5), ingest (folder of markdown or a Jira ticket), and backfill (add routing frontmatter). Every run ends by validating the tree with validate_memory."
allowed-tools:
  - spec2test_info
  - validate_memory
  - generate_memory_index
  - backfill_memory
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

# s2t-hydrate — Hydrate Memory (docs/s2t-memory/)

Project memory is a hierarchical, topic-based tree following fab-kit's `docs-hydrate-memory` model:

- Path: `docs/s2t-memory/{domain}/{topic}.md`, optionally `docs/s2t-memory/{domain}/{sub-domain}/{topic}.md` (max depth 3).
- Every topic file leads with frontmatter: `type: memory` + a one-line `description:` (≤500 chars soft
  target, ≤1000 hard limit, no change-ids). The `description:` is the routing signal.
- Domains and topics are chosen by **semantic grouping** — there is no fixed taxonomy and no fixed
  category enum. You (the agent) author and merge the file **bodies** as present-truth prose.
- Reserved domains: `_shared/` (cross-cutting) and `_unsorted/` (staging for content you cannot
  confidently classify).
- `index.md` files at every tier are **generated** — never hand-edit them. Call `generate_memory_index`.
- Record provenance as a trailing inline citation in the prose, e.g. `(source: PROJ-123)`,
  `(source: docs/foo/bar.md)`, or `(source: <work-unit-slug>)`. Citations only — never in headings,
  never as change/transition narration. Bodies state present truth.

## Argument classification & mode routing

Route to exactly one mode from the invocation arguments:

| Argument | Mode |
|----------|------|
| The literal keyword `backfill` | **Backfill** |
| A folder path (an existing directory) | **Ingest (folder)** |
| A Jira ticket key (e.g. `PROJ-123`) | **Ingest (Jira)** |
| No arguments (active work unit present) | **Pipeline** (stage 5) |

Rules:
- Reject **mixed-mode** invocations (arguments that classify to more than one mode) with a clear
  explanation. All non-`backfill` arguments must classify to the same mode.
- `backfill` takes **no** further arguments — reject any extra positional argument: "backfill takes
  no arguments — it re-scans docs/s2t-memory/ itself."
- A folder path that does not exist → abort with `Folder not found: {path}`.

Every mode ends with the **Validation step** below.

---

## Ingest mode (folder) — build memory from a markdown dump

1. Recursively read all `.md` files under the folder. Ignore non-markdown files.
2. For each source, identify the **domains** (logical subject areas) and **topics** within them.
   Map to `docs/s2t-memory/{domain}/{topic}.md`. Split a multi-subject document across multiple
   topic files. When you cannot confidently classify a subject, place it under `_unsorted/`.
3. For each target topic file:
   - If it does not exist, create it from `assets/memory-file-template.md`: frontmatter
     (`type: memory` + a one-line `description:`) followed by present-truth prose.
   - If it exists, **merge** the affected section as current truth — do not duplicate content and do
     not delete unrelated content. Keep the `description:` accurate.
   - Add a trailing `(source: <relative-path>)` citation to each incorporated statement.
4. Run `generate_memory_index` to regenerate the indexes.
5. Run the **Validation step**.

## Ingest mode (Jira) — build memory from a Jira ticket

1. Fetch the ticket via the Jira MCP: use `jira_issues` for the ticket (summary, description,
   acceptance criteria, fields), `jira_comments` for discussion, and `jira_search` if you must
   resolve the key. If the Jira MCP is unavailable or the key is invalid/unreachable, report the
   error clearly and make **no** memory writes for that source.
2. Classify the extracted content (summary, description, acceptance criteria, comments) into
   `docs/s2t-memory/{domain}/{topic}.md` files exactly as in folder ingest. Split across topics as
   the subject matter dictates; use `_unsorted/` when unsure.
3. Author/merge present-truth prose and add a trailing `(source: <KEY>)` citation to each
   incorporated statement so every entry is traceable to the ticket.
4. Run `generate_memory_index`.
5. Run the **Validation step**.

---

## Backfill mode — add routing frontmatter to an existing tree

1. Call `backfill_memory`. It re-scans `docs/s2t-memory/`, adds `type: memory` + a synthesized
   one-line `description:` to every topic file missing one (preserving the body byte-for-byte),
   skips files that already have a `description:`, and creates missing `description:`-only index
   stubs. It is idempotent — a second run is a no-op.
2. Run `generate_memory_index` to regenerate the indexes from the (now-complete) frontmatter.
3. Run the **Validation step**.

Backfill never authors or merges prose bodies — it only adds leading frontmatter.

---

## Pipeline mode (stage 5) — hydrate from the active work unit

Use this after `s2t-export` to persist durable behavioral memory from the current change.

1. Read the active unit artifacts `analysis.md` and `test-cases.md`. If either is missing or
   predecessor gates are not advancement-permitting, stop and report the blocking reason.
2. Derive durable behaviors that materially affect future test design. Classify each into
   `docs/s2t-memory/{domain}/{topic}.md` (semantic grouping; `_unsorted/` when unsure), authoring or
   merging present-truth prose with a trailing `(source: <work-unit-slug>)` citation.
3. Run `generate_memory_index`.
4. Run the **Validation step** (below) and capture its result for the summary.
5. Build `hydration-summary.md` from `assets/hydration-summary-template.md` with the sections:
   `Hydration Summary`, `Files Created/Merged`, `Domains Touched`, `Validation Result`, `Warnings`.
6. Persist with `persist_artifact` using `stageId: "hydrate"` (use `force: true` on a rewrite).
7. Call `check_gate` with `stageId: "hydrate"`.
   - PASS: hydrate complete.
   - CONDITIONAL_PASS: present non-blocking issues and request explicit approval.
   - FAIL: fix the offending memory files and/or summary, then re-run. A non-conforming memory tree
     (blocking `memory_tree_valid`) FAILs the gate.

---

## Validation step (every mode — required final step)

Call `validate_memory`. It returns `{ conforming, filesScanned, blocking[], advisory[] }` and never
mutates files.

- If `blocking` is non-empty: surface the offending files + codes and **do not** report the run as
  complete. Fix each file (add/repair `type: memory` + `description:`, trim an over-cap description,
  remove a change-id from a description) and re-validate until `conforming` is true.
- If only `advisory` issues exist (e.g. a folder over ~12 files, a non-empty `_unsorted/`, an
  over-soft-cap description, a broken cross-link): the run completes; report the warnings to the user.

## Rules

- Memory path is fixed: `docs/s2t-memory/`.
- Index files are generated — never hand-edit them; always call `generate_memory_index`.
- Hydration is deterministic and idempotent: re-running with identical inputs against unchanged
  memory produces no diffs.
- Never auto-delete prior memory entries; a merge rewrites only the affected section to current truth.
- Bodies are present-truth prose — no change-id or transition narration; provenance is citation-only.

