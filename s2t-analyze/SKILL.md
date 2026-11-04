---
name: s2t-analyze
description: "Spec2Test pipeline stage 2 (analysis). Invoke on an approved intake to produce an evidence-backed analysis.md and run Gate 1."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - validate_output
  - check_gate
  - approve_gate
  - validate_memory
  - check_constitution
  - find_partner_coverage
  - jira_get_issue
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
  - validate_output
  - check_gate
  - approve_gate
  - validate_memory
  - check_constitution
  - find_partner_coverage
  - jira_get_issue
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

Also look at `unitMarkers` in the `check_constitution` result. If `unitMarkers.scoringV2` is set,
this is a scored unit. The scored-unit rules in Step 2, Step 2B, Step 3A, Step 3A-owners and Step 3C
apply to it.

## Step 1B — Read adopted lessons

If `.spec2test/lessons.md` exists, read its `Lesson Log` table and collect every row with
`Status: ADOPTED`. Each one is an extra checklist item for this analysis. Use it when you fill the
coverage tables and plan the interview. If a lesson needs a fact you do not have, add a question
for it to the Step 3 interview. Never edit `lessons.md` in this skill. If a lesson should become
an engine check, suggest that the tester opens a spec for it. Do not open one yourself.

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
- **Data lists**: Values that are handled the same way go in one Enumerated Value Inventory row and
  one REQ. Do not write one REQ per value. For example, 31 snippets or 15 token colors are one
  inventory row and one REQ each.
- **Business Rules**: Write one Business Rules row per rule. Do not split one rule into several
  rows. Do not copy a rule in just because it appears in another table.
- **Workflow Kind (scored units)**: In a scored unit (Step 1A), fill the Kind column of
  `## Workflow Analysis` for every workflow:
  - Kind is `User` when its steps differ from every other workflow.
  - Kind is `Variant of WF-###` when it has the same steps as the User workflow WF-###, with
    different data or a different trigger. A variant always points to a User workflow, never to
    another variant.
  - Kind is `Non-functional` when it checks speed, platform or layout.

  Examples: in a code snippet story, WF-002 to WF-004 are variants of WF-001, because each one
  inserts a different snippet with the same steps. In a keyboard navigation story, WF-009 is
  Non-functional, because it only checks speed, stress and macOS. In a unit that is not scored,
  you may leave the Kind column out.
- **Required analysis pattern sections**: every analysis must include `## Edge Case Decisions`,
  `## State Dependencies`, `## Comparison Method`, `## Performance Targets`, and `## Data
  Preservation`. These sections are part of the source-of-truth design record and Gate 1 blocks any
  unresolved edge, state, comparison, performance, or data-preservation gap.
- Hidden Test Opportunities are **advisory only** — never in the Traceability Matrix, never scored.
- Never leave a required section blank; mark unknowns `[GAP]`. Tag later additions `[SUPPLEMENT]`.

Call `validate_output` (`skillId: "s2t-analyze"`) on the analysis. Fix every violation before you
save. The engine picks the right contract for this unit. Do this before every save of `analysis.md`,
here and in Step 3.

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

## Step 2B — Performance targets (scored units only)

Skip this step unless Step 1A found `unitMarkers.scoringV2`. Fill `## Performance Targets` with
these rules:

- Write one row per load driver: one user action at one data size. Merge two rows with the same
  action and data size.
- Write at least one row. With no load in the story, use PERF-001 for the main user action.
- Data Size starts with a short test data name in backticks, such as `Large5k.cspm`, or is `Any`.
- Target is a number with no sign, and means "at most". Write `500`, not `< 500`. Or write
  "Same as or faster than <legacy product>". "No visible lag" alone is not a target.
- Each row's REQ must be in Atomic Requirements.
- Fill Stress Volume only when the row has its own stress load, with its test data name in
  backticks. Otherwise write None, or leave the column out.

Find each target in this order:

1. A Performance Baseline row that fits the action and the data size. Write its ID in Baseline and
   `None` in CLR.
2. A target stated in the story, a comment or a design source. Show it and where you found it, and
   ask one Yes/No question, for example: "The story says the list must open in 2 seconds. Use this
   target?" On Yes, record the answer (Step 3B) and write its CLR ID. On No, go to 3.
3. Ask the user one question and wait, for example: "How fast must the list open in a 1,000-line
   module?" Show the closest baseline row as a suggestion. Record the answer and write its CLR ID.

Never answer these questions yourself, even when told to run on your own. Ask each one with
`vscode_askQuestions` and wait.

If the constitution has no `## Performance Baseline`, skip step 1. Tell the user once per run:
"Your constitution has no Performance Baseline. Run /s2t-init to add one, so most targets need no
question."

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
- **Source rule**: Source starts with `user` for a user answer, for example `user interview`.
  Source starts with `AI` for a row you wrote yourself. Gate 1 does not accept an AI row as the
  source of a performance target.
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
  In a scored unit, the generate stage writes one test case for each row this story owns.
- **Partner Story (scored units)**: In a scored unit (Step 1A), fill Partner Story from the row's
  Memory Source: the Jira key of the other story, or `None` when the Memory Source names no Jira
  key. Write one row per partner behavior, not one row per key. Keys that describe the same
  behavior (for example, the story that built it and a later bug fix) share one row. For now, use
  the key of the most recent story that changed the behavior, and keep every key in the Memory
  Source. Step 3A-owners may change the key and sets the Owner. Until then, write `This story`.
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

## Step 3A-owners — Interaction owners (scored units only)

Skip this step unless Step 1A found `unitMarkers.scoringV2`. A test between two stories is written
once. This step decides which story writes it, from what the shared repo already tests. Run it
right after Step 3A.

1. Run `git fetch origin` in the terminal. Stop it if it takes more than 60 seconds. If it fails
   or times out, go to step 7.
2. Call `find_partner_coverage` once:
   - `storyKey`: this story's Jira key.
   - `partnerKeys`: every Jira key cited in the Memory Source of every row that has a partner.
     Use every key, not only the Partner Story, so a case filed under an older story is found.
   - `featureTerms`: this story's feature names: the intake title and the Feature Area values.
3. Read the result. Count a partner case only when `deprecated` is false and `gate2Passed` is
   true. A case with `matchedBy: "feature"` does not name this story. Read it, and count it only
   when it tests the same pair. When unsure, do not count it. When a row cites several keys and
   one of them has a counted case, set Partner Story to that key.
4. Find the release of this story and each partner: call `jira_get_issue` with
   `fields: "fixVersions"`. If the tool is missing, a call fails, or either story has no fix
   version, ask the user one question that lists every unclear partner, for example: "Which
   release is each story in: CONSIM-6830, CONSIM-6032? (earlier, this or later)". Wait for the
   answer. Never answer it yourself, even when told to run on your own. Record the answer in
   `clarifications.md` (Step 3B) with `Target` = `general` and `Source` = `user interview`.
5. Set the Owner of each row with this table. The story analysed second owns the test.

   | Partner found | Owner |
   |---|---|
   | Delivered in an earlier release | This story |
   | Same release, its unit already has a case for this pair | Covered by <KEY> TC-### |
   | Same release, no case for this pair yet | This story |
   | Not delivered yet (a later release) | Deferred to <KEY> |
   | This story changes the partner's behavior | This story, always |

   A row with Partner Story `None` always has Owner `This story`. "A case for this pair" means a
   partner case you counted in step 3.
6. Under the table, write `Partner lookup: remote checked <YYYY-MM-DD>, <n> units read.` Use the
   date of `lastFetchAt` and the `unitsRead` number.
7. If the fetch failed, or `remoteChecked` is false, set every Owner to `This story` and write
   `Partner lookup: remote not reached` under the table. Keeping the test is the safe choice.
8. In the Coverage Intent Matrix, for each requirement whose `INT-###` rows all have Owner
   `Covered by` or `Deferred to`, write `REF INT-###` in its Interaction cell, naming one of those
   rows. When a row this story owns covers the same requirement, keep `Y`.

When `s2t-analyze` runs again (for example in a rework), run this step again and update the
matrix cells to match the new owners.

## Step 3C — Story complexity assessment

This step gives a scored unit (Step 1A found `unitMarkers.scoringV2`) its class. The class tells
`s2t-generate` how many test cases to write. For a unit with a constitution version but no
`scoringV2`, use Step 3C-legacy instead. Skip both for a format 1 unit.

The class comes from two counts. Requirements, Validation Rules, Edge Case Decisions, State
Dependencies and Platforms do not change the class. They are still tested.

1. Count. Count only rows whose first cell is an ID. A missing table counts as 0.
   - Logic: the `BR-###` rows in `## Business Rules`.
   - Breadth: the `WF-###` rows in `## Workflow Analysis` with Kind `User`.
   - Also count the `Variant of WF-###` rows and the `Non-functional` rows. You need them for the
     class question.
2. Look up the level for each count:

   | Logic level | Business rules |
   |---|---|
   | Low | 0–3 |
   | Medium | 4–8 |
   | High | 9+ |

   | Breadth level | User workflows |
   |---|---|
   | Narrow | 0–2 |
   | Moderate | 3–6 |
   | Wide | 7+ |

3. Find the class:

   | Logic \ Breadth | Narrow | Moderate | Wide |
   |---|---|---|---|
   | Low | SIMPLE | SIMPLE | MEDIUM |
   | Medium | MEDIUM | MEDIUM | HIGH |
   | High | MEDIUM | HIGH | COMPLEX |

4. Count the High-Risk Interactions: the `INT-###` rows in `## Feature Interaction Analysis` with
   Risk `High`. This count does not change the class.
5. Fill the `## Story Complexity Assessment` section: the Logic row and the Breadth row (Count and
   Level), then `Complexity Class:` and `High-Risk Interactions:`.
6. Confirm the class with the user. Do this for every scored story, before the risk question.
   - If `Class Confirmed:` already names a CLR row whose Answer has the same class, keep it. When
     `s2t-analyze` runs again (for example in a rework), ask again only if the class changed.
   - Otherwise ask one question with `vscode_askQuestions` and wait:
     "This story looks <CLASS>: <n> business rules, <n> user workflows (<n> variants, <n>
     non-functional). Does that sound right?" Options: Yes, No. Do not show the workflow list with
     this question.
   - On Yes, record the answer in `clarifications.md` (Step 3B) with `Stage` = `analysis`,
     `Target` = `class`, `Resolution` = `Y`, Answer `Y (<CLASS>)` and a Source that starts with
     `user`.
   - On No, show the workflow list (ID, name, Kind) and ask one follow-up: "What looks wrong: a
     workflow's kind, or the business rules?" Fix what the user names, count again, and ask the
     class question again.
   - If the user still says No, keep the class from the grid. Record `Resolution` = `N` and the
     Answer `N (<CLASS>): <the user's reason>`. Do not ask the user for a class. A risk raise is
     the only way up. No approval is needed.
   - Write `Class Confirmed: CLR-###` with the ID of that row, under the other labels.
   - Never answer this question yourself. Ask and wait, even when told to run on your own.
7. Check for a risk that needs more tests. Use the same risk signs and the same one question as
   Step 3C-legacy step 6. High-Risk Interactions of 1 or more is also a risk sign. Write
   `Risk Raise:` and `Raised Class:` the same way.

Gate 1 counts again. If a count, a level or the class does not match, Gate 1 fails and names it.
Gate 1 also fails when the `Class Confirmed:` row is missing, did not come from the user, or was
given for another class.

## Step 3C-legacy — Story complexity assessment (units without scoringV2)

Use this step only when Step 1A found a constitution version and no `unitMarkers.scoringV2`. It
gives the story a score and a class. Skip this step for a format 1 unit (Step 1A found no
`unitConstitutionVersion`).

1. Count the rows in each table. Count only rows whose first cell is an ID. A missing table counts
   as 0.
   - Requirements: the `REQ-###` rows in `## Atomic Requirements`.
   - Workflows: the `WF-###` rows in `## Workflow Analysis`.
   - Business Rules: the `BR-###` rows in `## Business Rules`.
   - Validation Rules: the `VR-###` rows in `## Validation Rules`.
   - Interactions: the `INT-###` rows in `## Feature Interaction Analysis`.
2. Count the platforms: the comma-separated items in the constitution's `Platforms:`. If there is
   no constitution, use 1.
3. Look up the points for each count:

   | Points | Requirements | Workflows | Business Rules | Validation Rules | Interactions |
   |---|---|---|---|---|---|
   | 0 | 0 | 0 | – | – | 0 |
   | 1 | 1–3 | 1 | 0–2 | 0–2 | – |
   | 3 | 4–10 | 2–3 | 3–5 | 3–5 | 1–2 |
   | 5 | 11–20 | 4–5 | 6–10 | 6–10 | 3–4 |
   | 8 | 21+ | 6+ | 11+ | 11+ | 5+ |

   | Platforms | Points |
   |---|---|
   | 1 | 0 |
   | 2 | 1 |
   | 3+ | 2 |

4. Add the points to get the total score. Find the class:

   | Total score | Class |
   |---|---|
   | 0–10 | SIMPLE |
   | 11–20 | MEDIUM |
   | 21–30 | HIGH |
   | 31+ | COMPLEX |

5. Fill the `## Story Complexity Assessment` section with this table and these lines. The template
   shows the scored-unit layout, so use this one instead. Keep the six rows in this order:

   ```markdown
   | Dimension | Count | Points |
   |-----------|-------|--------|
   | Requirements | 0 | 0 |
   | Workflows | 0 | 0 |
   | Business Rules | 0 | 1 |
   | Validation Rules | 0 | 1 |
   | Interactions | 0 | 0 |
   | Platforms | 1 | 0 |

   Total Score: 2
   Complexity Class: SIMPLE
   Risk Raise: None
   Raised Class: None
   ```
6. Check for a risk that needs more tests. The score counts size, not risk. Look for these risk
   signs: data loss (for example, saved files can be deleted or overwritten), security, money, or
   an area that often breaks (for example, a `Regression/Backward-Compatibility` interaction or a
   High-risk row).
   - If there is no risk sign, or the class is already COMPLEX, write `Risk Raise: None` and
     `Raised Class: None`.
   - Otherwise, ask one question in the Step 3 interview, for example: "This story can delete saved
     files. Should it get more tests?" Ask one question only, even when there are several signs.
   - Record the answer in `clarifications.md` (Step 3B) with `Target` = `general` and
     `Resolution` = `Y` for Yes or `N` for No.
   - For Yes, write `Risk Raise: <the risk> (CLR-###)`, using the ID of that row, and set
     `Raised Class:` to the next class up (SIMPLE → MEDIUM, MEDIUM → HIGH, HIGH → COMPLEX).
   - For No, write `Risk Raise: None` and `Raised Class: None`.
   - Never raise more than one level. Never raise without the user's Yes.

Gate 1 counts again. If a number does not match, Gate 1 fails and names the row to fix. Gate 1 also
checks that a raise is one level up and that its `CLR-###` row says `Y`.

## Final self-review (required)

Before you save, check your output against the Final Quality Check in the writing standard.
Fix what you can. Do not change text copied from Jira, Confluence, or user answers.
Count the rule breaks you could not fix.

In a scored unit, also check:

- Does every Performance Targets row have a Baseline ID or a CLR with a user answer?
- Do any two Performance Targets rows have the same action and the same data size? Merge them into
  one row.
- Do any two User workflows follow the same steps? Make one a `Variant of` the other.
- Is each business rule one row, not split and not copied from another table? Merge split rows and
  remove copies.
- Do two `INT-###` rows have the same Existing Feature, the same requirement and the same
  Interaction Type? Merge them into one row, and keep every Jira key in its Memory Source.
- Does every `Covered by` row name a case that `find_partner_coverage` returned?
- Does every `REF INT-###` cell point to a row this story does not own?

Count each problem you cannot fix in `openAdvisoryItems` (Step 4).

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

  A Story Complexity Assessment failure names the row or label to fix. Count again, fix the
  section, and run Step 4 again.

  In a scored unit, a Performance Targets failure names the row to fix:

  - **No source**: the row has no Baseline ID and no CLR. Pick a Performance Baseline row, or ask
    the user for the target (Step 2B).
  - **Missing baseline row**: the Baseline ID is not in the constitution. Pick a row that exists, or
    tell the user to run `/s2t-init` to add it.
  - **AI answer**: the CLR row's Source does not start with `user`. Ask the user the question, record
    the answer, and use its CLR ID.
  - **REQ not in Atomic Requirements**: add the REQ to Atomic Requirements, or fix the ID in
    Performance Targets.

  In a scored unit, a Workflow Analysis or Story Complexity Assessment failure names the row to fix:

  - **Kind value**: Kind is not `User`, `Non-functional` or `Variant of WF-###`. Use one of these.
  - **Variant link**: a variant points to a workflow that is missing, or that is not a User
    workflow. Point it to the User workflow whose steps it repeats, or set its Kind to `User`.
  - **Count, level or class**: count the rows again (Step 3C), look up the level and the class, and
    fix the row or the `Complexity Class:` line. If you changed the class, ask the class question
    again.
  - **High-Risk Interactions**: count the `INT-###` rows with Risk `High` and fix the line.
  - **Class confirmation**: the `Class Confirmed:` line is missing, its CLR row is missing, the row's
    Target is not `class`, its Resolution is not Y or N, or its Source does not start with `user`.
    Ask the user the class question (Step 3C step 6), record the answer, and write its ID.
  - **Class changed**: the class in the CLR Answer is not the current class. Ask the user to
    confirm the new class, record a new row, and update `Class Confirmed:`.
  - **Partner Story or Owner value**: Partner Story must be a Jira key or `None`. Owner must be
    `This story`, `Covered by <KEY> TC-###` or `Deferred to <KEY>`. A row with Partner Story
    `None` must have Owner `This story`. Fix the row (Step 3A-owners).
  - **REF cell owned by this story** ("INT-### is owned by this story. Write its test case."): the
    Interaction cell says `REF INT-###`, but that row has Owner `This story`. Change the cell to
    `Y`. The generate stage then writes the case.
  - **REF cell with no row**: the Interaction cell names an `INT-###` row that does not exist. Fix
    the ID, or write `Y`.

  These give CONDITIONAL_PASS. Fix them, or ask the user to approve with a reason:

  - **Non-functional workflow with a functional REQ**: move the REQ to a User workflow, or set the
    row's Kind to `User`.
  - **User workflow with only speed or platform REQs**: set its Kind to `Non-functional`, or add the
    user steps it covers.
  - **Variant in another Feature Area**: check that the variant really repeats the steps of its
    parent. If it does not, set its Kind to `User`.
  - **Near-repeat business rules**: keep one row per rule. Merge the two rows, or reword one so the
    difference is clear.

Keep your recorded Gate Result section consistent with the metrics you pass to `check_gate`.

## Rules

- Operate only on the active unit; use `force: true` to update the `analysis.md` you are building.
- Requirements only from documented evidence; assumptions never become requirements.
- Do not advance to `s2t-generate` until Gate 1 is PASS, or CONDITIONAL_PASS with a recorded approval.
- No network access, except in Step 3A-owners: `git fetch origin` in the terminal and
  `jira_get_issue` for fix versions. Otherwise use Jira relationships only if already present in
  `intake.md`.

