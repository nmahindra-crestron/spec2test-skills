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
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

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

Call `spec2test_info`. If unavailable, stop and ask the user to install Spec2Test.

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

## Writing standard (required)

Follow this standard for everything you write and every question you ask the user.
Ask one short, plain question at a time.
Follow the Agent Question Rules in the writing standard: one short, plain question at a time.

### Who the rules apply to

These rules apply to everything you write and every question you ask:

- Markdown files and plan files
- Requirements and user stories
- Test cases, test steps, and expected results
- Purpose, Setup Details, and Pre-requisite sections
- Documentation, bug reports, and release notes
- Any other project artifact

### Keep It Simple

Keep everything simple and direct.

- Use plain, easy English. Aim for about an 8th-grade reading level.
- Write for new testers, developers, product owners, and business users.
- Choose clear words over technical ones.
- Do not sound corporate, academic, or machine-written.

Use the simpler word:

| Use | Instead of |
|-----|------------|
| use | utilize |
| start | initiate |
| find | identify |
| change | modify |
| help | facilitate |
| check | validate |

"Validate" is fine at the start of a test case title. In other text, prefer "check".

Avoid buzzwords, hard words, long sentences, needless jargon, and filler text.

### General Writing Rules

1. Use active voice.
2. Keep sentences short and direct.
3. Explain a technical term when you must use it.
4. Write as if you are explaining to a new team member.
5. Give enough detail, but never make it complicated.
6. Make every instruction something the reader can act on.
7. Do not assume the reader knows the product.
8. Do not be vague. Say exactly what you mean.

### Agent Question Rules

Every question you ask the user must:

- Be short and clear.
- Ask only one thing at a time.
- Avoid jargon unless it is needed.
- Make sense to a non-technical user.

Good:

- Which module should be tested?
- Should valid or invalid data be used?

Avoid:

- Please clarify the desired validation scenario for the target workflow implementation.

### Documentation Rules

For Purpose, Setup Details, Pre-requisite, guides, and other documentation, always explain:

1. What it does.
2. Why it is needed.
3. How to use it.

Use clear headings, bullet points where they help, and step-by-step instructions. Assume the reader is new to the product.

### Test Case Title Standard

A title says what the test checks. The `Name:` value must start with one of these words:

- Verify
- Validate
- Check
- Confirm

Do not write a title as a user action. The gate fails a title that does not start with one of these words.

Good:

- Verify user login with valid credentials
- Validate successful module compilation
- Check project build completes without errors
- Confirm output file is generated successfully

Avoid:

- Login
- Create User
- Build Project
- Upload File

### Test Step Standard

1. Make each step detailed but simple.
2. Use one action per step. Do not join two actions in one step.
3. Give enough detail for a new tester to run the test.
4. Name the screen, the input, the choice, and the action.
5. Use plain English.
6. A test case has 6 to 18 steps.

Example:

1. Open the project.
2. Select the target module.
3. Enter valid values in all required fields.
4. Save the changes.
5. Click Build.

### Expected Result Standard

An expected result must:

- Describe behavior the tester can see.
- State exactly what should happen.
- Be easy to check.
- Have at least 5 words.

Avoid:

- Works correctly
- Successful
- No issues found

Good:

- The build completes without errors.
- A success message is displayed.
- The output file is generated.
- The generated file opens without errors.

### Copied Text

Keep text copied from Jira, Confluence, or user answers exactly as it is. Do not rewrite it, and do not count it as a rule break.

### Final Quality Check

Before you save any output, check each item:

- Can a new tester understand the content?
- Can any sentence be made simpler?
- Did you remove needless technical terms?
- Does every test case title start with Verify, Validate, Check, or Confirm?
- Can every step be run without guessing?
- Does every expected result say exactly what the tester will see?
- Do Purpose, Setup Details, and Pre-requisite say what, why, and how?
- Is the whole output simple and easy to follow?

If any answer is No, rewrite that part. Count the rule breaks you could not fix.


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

## Final self-review (required)

Before you save, check your output against the Final Quality Check in the writing standard.
Fix what you can. Do not change text copied from Jira, Confluence, or user answers.
Count the rule breaks you could not fix.

In every mode, do this before you write a memory file or `hydration-summary.md`.

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

Use this after `/s2t-rework` reports the review gate passed to persist durable behavioral memory from the current change.

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
   - PASS: hydrate complete. Next: `/s2t-export`.
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

