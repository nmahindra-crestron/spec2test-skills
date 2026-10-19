---
name: s2t-analyze
description: "Spec2Test pipeline stage 2 (analysis). Invoke on an approved intake to produce an evidence-backed analysis.md and run Gate 1."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - validate_memory
  - check_constitution
---

You are executing the **s2t-analyze** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

---
name: s2t-analyze
description: "Spec2Test pipeline stage 2 (analysis). Invoke on an approved intake to produce an evidence-backed analysis.md and run Gate 1."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - validate_memory
  - check_constitution
---

You are executing the **s2t-analyze** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

# s2t-analyze — Analyze (Stage 2 of the Spec2Test pipeline)

Use this skill after `s2t-new` completed intake and Gate 0. It transforms the active work unit's
approved `intake.md` into a structured, evidence-backed QE analysis (`analysis.md`) — the primary
input to test-case generation — then runs **Gate 1**.

Drive everything through the Spec2Test engine tools; do not hand-manage folders, slugs, or gates.

## Writing standard (required)

Follow this standard for everything you write and every question you ask the user.
Ask one short, plain question at a time.

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


## Step 1 — Read the approved intake

Read `intake.md` from the **active work unit** — **in full**, not a partial or truncated read. Before
proceeding, confirm the highest `DEX-###` id you read equals the highest id in the intake's
`### Design Extracts` table; if not, re-read. You do not need to re-check Gate 0: the engine
blocks `persist_artifact` for the `analysis` stage until the intake gate is PASS or an approved
CONDITIONAL_PASS (it will name `intake` if you try too early). If no unit is active, stop and tell
the user to run `s2t-new` or switch to a unit.

## Step 1A — Check the story against the constitution

Call `check_constitution`. If `unitConstitutionVersion` is not set, this is a format 1 unit. Skip
this step.

Otherwise, read `.spec2test/constitution.md` and compare it with the story:

1. Does the story name an application that is not the constitution's `Application:`?
2. Does the story name a platform that is not in the constitution's `Platforms:`?
3. List what a tester must set up before testing this story. Does any setup need have no matching
   row in Setup Actions?

Add one question to the Step 3 interview for each mismatch and each uncovered setup need. Ask it
like any other question, and record the answer in `clarifications.md` (Step 3B) with `Target` =
`general`. Don't change the constitution yourself. If the user wants it changed, tell them to run
`/s2t-init`.

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

## Step 2A — Design extract accounting (required when intake has Design Extracts)

Every intake `DEX-###` row must appear **exactly once** in `## Design Extract Accounting`. Gate 1
fails otherwise. For each row choose a disposition:

- `RESTATES-AC` — **only** when the row adds nothing beyond an existing Acceptance Criterion. Put
  the existing REQ/BR/VR/WF id(s) in **Mapped To**. No question needed.
- `IMPLEMENTATION-DETAIL` — the row is **purely** internal code design with no tester-observable
  effect (see *Question scope* in Step 3): class/interface names such as `IHelper`, design patterns,
  dependency injection, module/layer structure, internal data structures or threading. Not
  promoted; **Reason** required (`implementation detail — not tester-observable`);
  leave **CLR** empty. No question needed. If any part of the row affects observable behavior, it
  is a candidate instead.
- **Every other row is a candidate** and gets a mandatory question in Step 3 ("Include as a
  requirement / context only / no?"). Do not decide on the user's behalf:
  - "include" → `EXTENSION`: create or extend REQ/BR/VR/WF items, tag each
    `[DERIVED: DEX-###]`, add an Evidence Register row (Source Type `Design Extract`, Source Location
    `DEX-###`), list the ids in **Mapped To**, and cite the answer's `CLR-###`.
  - "context only" → `CONTEXT-ONLY` (the user's decision only): **CLR** and **Reason** required.
  - "no" or `[SKIP]` → `DEFERRED`: not promoted; **CLR** and **Reason** required.
- A row that **partly** restates an AC and partly adds behavior is a candidate. If the user says
  "include", the whole row is `EXTENSION` and **Mapped To** lists both the existing item and the
  derived item(s). Never split a DEX row.
- Every item tagged `[DERIVED: DEX-###]` must appear in that row's **Mapped To** (the engine does
  not check this direction).
- **Per-type workflows**: when a workflow covers several item types and a DEX row (or an intake
  per-type workflow) describes type-specific behavior, create a separate `WF-###` with its own REQs
  rather than folding the difference into the shared workflow.
- **Conflicts**: DEX rows that contradict each other or the AC go to Conflict Analysis and are
  resolved in the Step 3 interview.
- Only mention `DEX-###` ids that exist in the intake; Gate 1 fails on unknown ids.

## Step 3 — Resolution interview loop

Detect gaps, conflicts, ambiguities, and unconfident assumptions. Interview the user to resolve them
(HIGH-severity gaps and conflicts first; group related questions; allow `[SKIP]`). After each round,
update the dependent sections (Atomic Requirements, Business/Validation Rules, Coverage Intent,
Traceability, Summary) and re-write `analysis.md` via `persist_artifact` (`force: true`). Continue
until items are resolved or explicitly `[SKIP]`-deferred.

**Question scope — tester-observable only**: these test cases are written and executed manually,
so only ask about what a tester can set up, do, or observe through the product (UI, inputs, outputs,
messages, saved/exported data, states, limits, timings, permissions). Never ask about internal
code design: class/interface/method names (e.g. `IHelper`), design patterns, dependency injection,
module or layer structure, internal data structures, threading/async models, or refactoring
choices. Test before asking: *would the answer change a test step, test data, or an expected
result?* If not, do not ask — record it as context (or `IMPLEMENTATION-DETAIL` for DEX rows). If an
internal detail does have an observable effect, ask about the effect, not the code. If the user
volunteers internal design details in an answer, record them verbatim as given but ask no follow-up
about them. When it is unclear whether a detail is internal or observable, ask — phrased only as the
visible effect (e.g. *"What does the user see when …?"*); use `IMPLEMENTATION-DETAIL` only for content
that is clearly internal. At the end of each interview round, show a short **Not asked
(implementation detail)** list of the items you did not ask about for this reason (omit it when
empty). If the user names an item, ask it as a normal question in the next round and update its
disposition from the answer (e.g. `IMPLEMENTATION-DETAIL` → `EXTENSION` / `CONTEXT-ONLY` /
`DEFERRED`, with the CLR recorded).

**Design extract candidates**: ask one mandatory question for every Step 2A candidate (every DEX
row that is not `RESTATES-AC` or `IMPLEMENTATION-DETAIL`). `CONTEXT-ONLY` is only ever the user's answer. A `[SKIP]` answer
makes the row `DEFERRED`.

**Unclear mandatory coverage rule**: all eight Coverage Intent Matrix test types (Functional,
Negative, Edge, Boundary, Performance, Stress, Exploratory, Interaction) are mandatory for every
requirement. If the intake/evidence does not make clear how a type applies to a requirement (no
performance target, no stress volume, no interaction partner, etc.), do not guess, assume, or
leave it as a silent `[GAP]` — add a mandatory question for it in this interview loop so the user
clarifies it explicitly.

**Questions MUST be asked using the `vscode_askQuestions` tool** — never stated as plain text.
Each item must be a distinct question entry. Every question is mandatory; the user must answer
explicitly or reply `[SKIP]`. Re-ask unanswered questions before proceeding.

Follow the Agent Question Rules in the writing standard: one short, plain question at a time.

**Image attachments are welcome**: for any question where a screenshot, diagram, or UX spec would
help, tell the user in the question text that they may attach an image directly in their chat reply.
After the user responds, inspect their chat message for attached images and extract relevant
information (UI elements, AC items, field names, flow diagrams, etc.) exactly as you would from a
typed answer. Treat image-derived content as `[SUPPLEMENT]` and note the source as
"user-attached screenshot".

## Step 3B — Record clarifications (required)

Every answer you collect in the Step 3 interview MUST be recorded verbatim in the shared **input**
artifact `clarifications.md` — never embedded as raw human prose in `analysis.md`. The derived
`analysis.md` (Evidence Register, Coverage Intent Matrix cells) instead **cites** the `CLR-###` id.

- If `clarifications.md` does not yet exist (unit started before this rule), create it from
  `clarifications-template.md` (included alongside this skill) via `persist_artifact` —
  `stageId: "analysis"`, `artifact: "clarifications.md"`. A zero-row log is valid.
- **Resolve `Recorded By`**: run `git config user.name` (fall back to `git config user.email`, then
  the literal `user`).
- **Append a row per answer** to the `## Clarification Log` table (columns in order): `ID`
  (`CLR-###`, unique, increasing across the whole file) · `Stage` = `analysis` · `Recorded At` =
  current ISO-8601 UTC timestamp · `Recorded By` · `Target` (`REQ-xxx / <TestType>`, `REQ-xxx`, or
  `general`) · `Resolution` (`Y`/`N`/`SKIP`/`ANSWERED`) · verbatim `Question`/`Answer` (encode
  newlines `<br>`, pipes `\|`) · `Source`.
- **`SKIP` discipline**: when the user explicitly authorizes deferring a mandatory test type, record
  the row with `Resolution: SKIP` and set the corresponding Coverage Intent Matrix cell to `SKIP`.
  Never self-assign `SKIP` — it requires an explicit user answer. Every matrix `SKIP` MUST have a
  backing `SKIP` row here or the gate fails (`cross_reference_backed`).
- In `analysis.md`, cite the clarification (e.g. Evidence Register source = `clarification CLR-003`)
  rather than restating the user's words.
- **Design extract answers**: record a `CLR-###` row for each disposition answer (`Target` =
  `DEX-###`) and cite that id in the Accounting row's **CLR** column.
- Re-write `clarifications.md` with `persist_artifact` (`artifact: "clarifications.md"`,
  `force: true`) after each round; keep the `## Summary` counts consistent.

## Step 3A — Feature interaction analysis (memory-backed)

Populate the single combined **Feature Interaction Analysis** table (one `INT-###` row per
interaction). Each interaction pairs this change's functional behavior with an existing project
feature drawn from the project memory tree (`docs/s2t-memory/`).

- If `docs/s2t-memory/index.md` exists, read the memory tree (start at the index; open the domain
  topic files most relevant to this change). For each atomic requirement, identify existing features
  it could touch, classify the interaction with exactly one of the twelve **Interaction Type** values
  (see the template), and add a row. Cite the backing fact in **Memory Source**
  (e.g. `auth-tokens (source: PROJ-123)`). Fill every column, including the scenario-outline columns
  (Objective, Pre-conditions, Interaction Steps, Expected Result) at a high level — do NOT author full
  test cases here (the generate stage expands each `INT-###` into exactly one interaction test case).
- Also note any existing behavior the change could regress; record these as
  `Regression/Backward-Compatibility` interactions (advisory risks) so downstream generation preserves
  compatibility.
- You may call `validate_memory` to confirm the memory tree is conforming before relying on it; treat
  its advisory findings as non-blocking.
- **When an interaction's existing partner feature or its interaction type cannot be determined from
  evidence or memory**, do not guess or silently omit it — add a mandatory question in the Step 3
  interview and record the answer in `clarifications.md` (`CLR-###`), then cite that clarification in
  the row's **Memory Source**.

**No-memory fallback**: if `docs/s2t-memory/index.md` is absent or the tree is empty, do NOT fabricate
existing features. Instead:

1. Write an advisory note as prose directly above the table (e.g. "No project memory found — run
   `s2t-hydrate` to enable memory-backed feature-interaction analysis; interactions below are
   user-sourced only.").
2. Raise ONE mandatory clarification (via `vscode_askQuestions`, recorded as a `CLR-###` row) offering
   the user the choice to (a) run `s2t-hydrate` first, or (b) manually name existing features to
   analyse for interaction.
3. For any interaction the user names, add a row citing `clarification CLR-###` in **Memory Source**.
4. A zero-row table (header only) is valid — interaction coverage is advisory and never FAILs the
   gate. Absent memory yields at most CONDITIONAL_PASS, never FAIL.

Interaction coverage is **advisory** throughout: it never blocks the gate.

## Final self-review (required)

Before you save, check your output against the Final Quality Check in the writing standard.
Fix what you can. Do not change text copied from Jira, Confluence, or user answers.
Count the rule breaks you could not fix.

Save the final `analysis.md` with `persist_artifact` (`force: true`), then run Step 4.

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
- `writingViolations` = the count of rule breaks from the Final self-review.
  Send the count as "writingViolations": { "value": <N> }. If it is not 0, or you leave it out,
  the result is CONDITIONAL_PASS.

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

  Design extract accounting failures name what to fix: a DEX id missing from (or duplicated in)
  `## Design Extract Accounting`, a **Mapped To** id with no matching REQ/BR/VR/WF row, a **CLR**
  id absent from `clarifications.md`, a missing conditional cell, or a `DEX-###` mentioned in
  `analysis.md` that does not exist in the intake. Fix the table or the referenced rows, re-write,
  and re-run Step 4.

Keep your recorded Gate Result section consistent with the metrics you pass to `check_gate`.

## Rules

- Operate only on the active unit; use `force: true` to update the `analysis.md` you are building.
- Requirements only from documented evidence; assumptions never become requirements.
- Do not advance to `s2t-generate` until Gate 1 is PASS, or CONDITIONAL_PASS with a recorded approval.
- No network access; use Jira relationships only if already present in `intake.md`.

