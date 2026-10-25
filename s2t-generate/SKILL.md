---
name: s2t-generate
description: "Spec2Test pipeline stage 3 (generate). Invoke on an approved analysis to produce traceable test-cases.md and run Gate 2."
allowed-tools:
  - spec2test_info
  - check_constitution
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-generate** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

---
name: s2t-generate
description: "Spec2Test pipeline stage 3 (generate). Invoke on an approved analysis to produce traceable test-cases.md and run Gate 2."
allowed-tools:
  - spec2test_info
  - check_constitution
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-generate** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

# s2t-generate — Design Tests (Stage 3 of the Spec2Test pipeline)

Use this skill after `s2t-analyze` produced an approved `analysis.md`. It turns the QE analysis into
meaningful, workflow-oriented, fully traceable test cases (`test-cases.md`), then runs **Gate 2**.

Drive everything through the engine tools; do not hand-manage folders or gates.

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


## Logic standard (required)

Use this checklist for every generated case and every later logic review. Review each step against
these rules before the gate can pass.

# Test Logic Standard

Use this checklist for the generate self-review and for later logic review. Review each test case one step at a time. Count each unresolved issue as one logic violation.

## LOGIC-01 — State walk must be explicit

Each step must follow the state that existed before it. The next step must make sense from the previous action and the expected result, not from a hidden assumption.

- If a test jumps from one state to another without showing how, mark it.
- If a step expects a value or screen that the prior step never created, mark it.
- If the setup or action leaves the system in an unknown state, mark it.

## LOGIC-02 — The step must create a visible change

Every action should produce an observable result. If the step does nothing visible, or the expected result is just a restatement of the action, the step is weak.

- A good step checks a user-visible effect.
- A weak step says the same thing twice without adding evidence.
- A step that only re-labels the action, clicks a button with no visible outcome, or checks the same state again is a logic problem.

## LOGIC-03 — Created before used

Any item the tester uses must exist before it is used. If the test depends on a record, state, value, or screen that is not created earlier in the flow, it breaks this rule.

- If the test expects a saved file, record, or screen state before the setup or prior steps create it, mark it.
- If the step assumes data was already set without showing that setup, mark it.

## LOGIC-04 — One failing condition at a time

Each step should check one clear condition or outcome. Do not mix unrelated assertions into the same step.

- A step should not check both success and failure at once unless the test specifically intends a branch.
- A step should not demand several unrelated changes in the same action.
- If one step tries to prove too many outcomes, it is a logic violation.

## LOGIC-05 — Safe setup only

The setup must support the test without changing something under test or leaving another item incomplete or in conflict.

- Do not add setup that changes the product state being tested.
- Do not create setup that leaves a related item unfinished, conflicting, or hidden from the test.
- If the setup is not needed for the scenario, drop it or count it as a setup violation.

## LOGIC-06 — Analysis decisions must carry through

Each step must match the approved analysis and the story context. Do not invent values, states, limits, or workflow order that the analysis did not support.

- Use the real requirement, business rule, validation rule, and edge-case decisions from analysis.md.
- Do not guess a performance target, state transition, or interaction detail.
- If the analysis is vague or missing, ask the user instead of inventing the logic.

## Review rule

Fix a logic issue when the fix keeps the scenario intent and does not hide a real defect. If it cannot be fixed safely, record a Generation Warning with the test case, step, and the rule ID.


Before generating or re-checking a test case, read `.spec2test/lessons.md` if it exists and collect
all rows whose `Status: ADOPTED`. Treat each adopted lesson as an extra checklist item during the
logic self-review and generation warnings. A lesson is not a replacement for the standard rules; it
adds another required rule for this unit.

## Step 1 — Read the approved analysis

Read `analysis.md` from the **active work unit**. You need not re-check Gate 1: the engine blocks
`persist_artifact` for the `generate` stage until the analysis gate is PASS or approved
CONDITIONAL_PASS (it names `analysis` if you try too early).

## Step 1A — Pick the test-case format

Call `check_constitution`. Look at `unitConstitutionVersion` in the result.

- **No `unitConstitutionVersion`**: this is a format 1 unit. Skip Step 1B. In Step 2, use the
  format 1 template and the format 1 rules. Don't add a Story Setup.
- **`unitConstitutionVersion` is set**: this is a format 2 unit. If `exists` is false or `valid`
  is false, stop. Tell the user: "Run /s2t-init first." Otherwise go on to Step 1B.

Look at `unitMarkers` in the same result. If `unitMarkers.suiteSizing` is set, this unit is sized.
Run Step 1C before Step 2. Otherwise skip Step 1C and leave out the `## Test Suite Sizing` section.

## Step 1B — Agree on the Story Setup (format 2 only)

If `test-cases.md` already has a `## Story Setup` section, show it to the user and ask:
**Keep or Edit?** On Keep, use it as it is and go to Step 2. On Edit, build a new one as below.

To build the Story Setup:

1. Read `.spec2test/constitution.md`.
2. Start from `Setup Details:`, `Pre-requisite:` and `Default Actions:` in Story Setup Defaults.
3. For each action in `Default Actions`, take its row from Setup Actions. Fill each `<placeholder>`
   with its value from Placeholder Defaults. Fill the rest from the story.
4. Fit the setup to the story. Add a Setup Action only if the story needs it. Drop what the story
   doesn't need.
5. If `version` is not the same as `unitConstitutionVersion`, show this note: "The constitution
   changed after this unit was made (unit version X, now version Y). I used the current version."
   Use the current constitution. Don't change the unit's version.
6. Show the Story Setup (Setup Details and Pre-requisite) and ask: **Yes, Edit or Not applicable?**
   - **Yes**: use it.
   - **Edit**: ask what to change, change only that part, show it again and ask again. Repeat
     until the user picks Yes or Not applicable.
   - **Not applicable**: set `Setup Details: None`. Keep the usual Pre-requisite from the
     constitution. Write no Setup Steps in any test case, and start Test Steps at 1.

After the user agrees, never change the Story Setup or any setup step without asking first.

## Step 1C — Test Suite Sizing (sized units only)

This step decides how many test cases to write and which test types share a test case. It runs
only for a sized unit (Step 1A). It never removes coverage: every requirement still gets all 8
test types. Only the grouping changes.

1. Read `Raised Class:` in `analysis.md`. If it is None, read `Complexity Class:`. This is the
   story class.
2. Count these in `analysis.md`:
   - W: the `WF-###` rows in Workflow Analysis.
   - I: the `INT-###` rows in Feature Interaction Analysis.
   - L: the workflows that have a stated numeric limit (a min, max or length).
3. Work out the target from the formula for the class. Look up the min and max.
4. Plan the test cases with the combining table. List each planned test case with its anchor type
   and its Test Types.
5. Check the plan before you write anything:
   - Every requirement still gets all 8 types.
   - In a SIMPLE or MEDIUM story, every test case has an anchor type in `Test Types:`: Functional,
     Negative, Performance or Interaction.
   - No test case lists more types than the cap for the class.
6. Write the `## Test Suite Sizing` section: the class, the min, the target and the max. After you
   write the test cases, fill `Generated:` with the number of test cases. Do not count deprecated
   test cases.

**Combining rule per class.** "Own case" means the type gets its own test case. "In Functional"
means the type is listed in the Functional test case and tested by a step there.

| Type | SIMPLE | MEDIUM | HIGH | COMPLEX |
|---|---|---|---|---|
| Functional | 1 per story | 1 per workflow | 1 per workflow | 1 per workflow |
| Negative | 1 per story | 1 per workflow | 1 per workflow | 1 per workflow |
| Performance | 1, with Stress | 1, with Stress | 1 | 1 |
| Stress | in Performance | in Performance | own case | own case |
| Boundary (valid side) | in Functional | in Functional | own case per limited workflow | own case per limited workflow |
| Boundary (invalid side) | in Negative | in Negative | own case per limited workflow | own case per limited workflow |
| Edge | in Functional | in Functional | in Functional | own case per workflow |
| Exploratory | in Functional | in Functional | in Functional | own case |
| Interaction | 1 per INT | 1 per INT | 1 per INT | 1 per INT |

**Example: a SIMPLE story** with 1 INT row gets these 4 test cases:

| Test case | Test Types | Primary Type |
|---|---|---|
| Functional | Functional, Edge, Boundary, Exploratory | none |
| Negative | Negative, Boundary | Negative |
| Performance | Performance, Stress | Performance |
| Interaction | Interaction | Interaction |

**Bands and caps.** The target is what you aim for. Gate 2 checks the min and max.

| Class | Target formula | Min | Max | Test Types cap | Anchor rule |
|---|---|---|---|---|---|
| SIMPLE | 3 + I | 4 | 6 | 4 | Yes |
| MEDIUM | 2W + 1 + I | 6 | 10 | 4 | Yes |
| HIGH | 2W + L + 2 + I | 12 | 20 | 3 | No |
| COMPLEX | 3W + L + 3 + I | 24 | 40 | 3 | No |

In HIGH and COMPLEX stories, Edge, Boundary, Stress and Exploratory may stand alone in a test case.

**Above the max.** When the plan has more test cases than the max, merge test cases:

1. Merge only test cases that have the same anchor type, the same workflow preconditions and the
   same `Primary Type:`.
2. Merge in this order: Boundary test cases first, then Negative, then Functional.
3. Never merge Interaction test cases. They still count toward the max.
4. The merged test case keeps every requirement in `Requirement(s):` and every type in
   `Test Types:` from the test cases it merged.
5. The merged test case must have 6 to 18 test steps and stay within the Test Types cap. If it
   would not, do not make that merge.
6. Stop when the count is at or below the max, or when no merge is left.

**Below the min.** Walk this expansion checklist. Add a test case only for a real gap. Never add a
test case just to reach the min.

| Item | Look for |
|---|---|
| Negative | A business rule or validation rule with no check of a wrong value or a broken rule. |
| Edge | An Edge Case Decisions row with no test step. |
| Interaction | An `INT-###` row with no test case. |
| Recovery | A workflow that saves or changes data with no check of cancel, undo or reopen after a failure. |
| Platform | Behavior the analysis says is different on one platform, with no test step on that platform. |
| Inventory | A FULL Enumerated Value Inventory or Behavioral Coverage Inventory item with no test step. |

**Record.** List every merge and every expansion in Generation Warnings. For a merge, name the test
cases you merged and the test case they became. For an expansion, name the gap. These notes do not
count toward `openGenerationWarnings`.

If the count is still outside the band, go on. Gate 2 gives CONDITIONAL_PASS, and a person approves
it with a reason.

## Step 2 — Design and write test-cases.md

For a format 1 unit, build `test-cases.md` from this template:

(Use the template in `test-cases-template.md`, included alongside this skill.)

For a format 2 unit, build `test-cases.md` from this template instead. Read the format 2 rules
below first.

(Use the template in `test-cases-template-v2.md`, included alongside this skill.)

### Format 2 rules

These rules replace the format 1 setup rules (Common Setup Library, `Setup Details` in each test
case, `### Pre-requisite` and `### Steps`).

- Write one `## Story Setup` section with `Setup Details:` and `Pre-requisite:`. Either may be
  "None". Don't write a Common Setup Library.
- Don't write `Setup Details:` or `### Pre-requisite` in a test case.
- **Setup Steps**: merge the Story Setup into 1 or 2 steps. Each merged step has one combined
  expected result. Then add any extra setup that only this test needs. Put extra setup only in that
  test's Setup Steps. Never test anything in a setup step. Number setup steps from 1.
- **Test Steps**: 6 to 18 rows. Setup steps don't count. Go on numbering from the last setup step.
  With no setup steps, start at 1. Every test case must have a Test Steps table.
- **Purpose**: one "Verify …" sentence, plus optional scope bullets. Each Purpose must be unique in
  the story.
- Leave `Feature Area:` blank. Keep `Requirement(s):` so Gate 2 can check coverage.

**Setup self-check (format 2)**: before you save, check every test case against these five rules.
Count every break as one setup violation:

1. A merged setup step has more than one expected result.
2. A setup step tests something.
3. The setup steps don't match the Story Setup.
4. A Purpose repeats another test case's Purpose.
5. The setup changes something under test, or leaves another item incomplete or in conflict.

Fix what you can. Name each rule you could not fix in Generation Warnings.

### Design discipline (both formats)

- Prefer **workflow-oriented** test cases that combine related REQ/BR/VR. For a sized unit,
  Step 1C sets the number of test cases. Do not generate one test case per requirement.
- Every test case traces to at least one REQ/BR/VR/approved source; every referenced `REQ-xxx`
  exists in `analysis.md`. Requirements are never invented.
- Carry forward the explicit analysis decisions from `analysis.md`: edge-case decisions, state
  dependencies, comparison methods, performance targets, and data-preservation rows. Do not generate
  tests against unresolved or vague analysis records.
- Cover Coverage Intent per the analysis matrix; satisfy Enumerated Value Inventory (FULL) and
  Behavioral Coverage Inventory; generate boundary coverage for validation limits.
- **All eight test types are mandatory for every requirement — no skipping**: Functional,
  Negative, Edge, Boundary, Performance, Stress, Exploratory, Interaction. Generate at least one
  test case of each type per requirement (workflow-combined test cases may satisfy several
  requirements and several types at once, but every type must appear somewhere in the traced
  coverage for every requirement). In a sized unit, a folded type still counts. List it in
  `Test Types:` of the anchor test case, and make sure a step really tests it.
- **Record each test case's types in the multi-value `Test Types:` field** (comma-separated) and its
  covered requirements in the `Requirement(s):` field. Gate 2 cross-references the `analysis.md`
  Coverage Intent Matrix against these fields: any requirement marked `Y` for a test type with no
  test case declaring that type and referencing that requirement is a **blocking** Gate 2 failure.
  A single workflow-combined case may declare several of the eight types to cover multiple matrix
  cells at once.
- **Set `Primary Type:` on the line after `Test Types:`.** The export puts it in front of the
  Name (for example `Negative - Verify ...`). Choose it this way:
  - Look only at Negative, Interaction, Performance, and Stress in `Test Types`. Stress counts as
    Performance.
  - None of them present: leave out the `Primary Type:` line.
  - One present: use it.
  - More than one present: count the steps that mainly check each type. Pick the type with the
    most steps. On a tie, pick Negative, then Interaction, then Performance.
  - The value must be Negative, Interaction, or Performance, and must be one of the test case's
    `Test Types` (Stress counts as Performance).
  - Start the line at column 0 with no bold marks. Gate 2 blocks an indented or bold line. It does
    not check the value, so the human review at Gate 2 does.
- If `analysis.md` does not make clear how a mandatory test type applies to a requirement (e.g. no
  performance target, no stress volume, no defined interaction partner), do not guess or invent
  test data — use `vscode_askQuestions` to ask the user for the missing detail before writing that
  test case, then persist the clarified answer into the relevant field.
  Follow the Agent Question Rules in the writing standard: one short, plain question at a time.
- **Question scope — tester-observable only**: these are manual test cases, so only ask about what a
  tester can set up, do, or observe through the product (UI, inputs, outputs, messages, saved or
  exported data, states, limits, timings, permissions). Never ask about internal code design:
  class/interface/method names (e.g. `IHelper`), design patterns, dependency injection, module or
  layer structure, internal data structures, threading/async models, or refactoring choices. If the
  answer would not change a test step, test data, or an expected result, do not ask. If an internal
  detail does have an observable effect, ask about the effect, not the code. If the user volunteers
  internal design details in an answer, record them verbatim as given but ask no follow-up about them.
  When it is unclear whether a detail is internal or observable, ask — phrased only as the visible
  effect (e.g. *"What does the user see when …?"*). At the end of each interview round, show a short
  **Not asked (implementation detail)** list of the items you did not ask about for this reason (omit
  it when empty); if the user names an item, ask it as a normal question in the next round.
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
  Interaction Steps / Expected Result (expanded to the 6–18 step discipline below). Write these three
  fields at line start with no bold markers (parser-critical, like `Name:`). This carry-through is
  **advisory**: a missing expansion never FAILs Gate 2 (see Step 3).
- Test data must be concrete; expected results observable; steps reproducible.
- **Steps count rule (format 1)**: every test case's `### Steps` table must have **6 to 18 data rows**
  (aim for 6-7; the header and separator rows are not counted).
  Gate 2 enforces these bounds and fails below 6 or above 18. Each row is one `Action` paired with a
  non-empty, observable `Expected Result` — never leave `Expected Result` blank (a blank cell is a
  blocking Gate 2 failure). Each `Expected Result` has at least 5 words; a shorter one gives a Gate 2
  warning. If a scenario is too small to reach 6 steps on its own, combine it with
  related steps/assertions from the same workflow rather than padding with trivial actions; if it
  would exceed 18, split into multiple test cases instead of cramming more rows in.
  Preserve the authored fields needed by the approved xlsx export and Gate 2 coverage check:
  `User Story`, `Repository Path`, `Test Types`, `Feature Area`, `Requirement(s)`, `Setup Details`,
  `Pre-requisite`, `Purpose`, and a `Steps` table that keeps `Action` and `Expected Result` as
  distinct columns.

**Setup Details rule (format 1)**: the `Setup Details` field in every test case must contain a human-readable
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

**Title rule:** the `Name:` value must start with Verify, Validate, Check, or Confirm, for example
`Name: Verify user login with valid credentials`. A title like `Name: Login` fails Gate 2 and export.
Purpose, Setup Details, and Pre-requisite must not be empty; an empty one gives a Gate 2 warning.

## Final self-review (required)

Before you save, check your output against the Final Quality Check in the writing standard.
Fix what you can. Do not change text copied from Jira, Confluence, or user answers.
Count the rule breaks you could not fix.

Write it with `persist_artifact` — `stageId: "generate"`, `content: <the filled markdown>`. On
regeneration/interview rewrites, pass **`force: true`**.

## Logic self-review (required)

After the writing self-review, walk every generated test case against the Logic standard. Review each
step in order and fix safe issues first. If a problem remains, record a Generation Warning that names
the test case, the step, and the LOGIC rule ID.

- Count unresolved logic problems as `logicViolations`.
- If a problem can be fixed without changing the scenario intent, fix it before saving.
- If it cannot be fixed safely, list the issue in Generation Warnings and keep the count in the gate
  metric.
- Check every adopted lesson too. In your report, list each one by its `LSN` ID with its rule and
  result, for example `LSN-001 (LOGIC-03): checked, no problem`. A problem against a lesson counts
  as a logic violation.
- If a lesson should become an engine check, suggest that the tester opens a spec for it. Do not
  open one yourself.

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
- `writingViolations` = the count of rule breaks from the Final self-review.
  Send the count as "writingViolations": { "value": <N> }. If it is not 0, or you leave it out,
  the result is CONDITIONAL_PASS.
- `logicViolations` = the count of unresolved logic problems from the Logic self-review. Send it as
  "logicViolations": { "value": <N> }. If it is not 0, or you leave it out, Gate 2 fails.
- `setupViolations` (format 2 only) = the count from the setup self-check. Send it as
  "setupViolations": { "value": <N> }. If it is not 0, or you leave it out, Gate 2 fails.

Call `check_gate` — `stageId: "generate"`, `metrics: { "untracedTestCases": {"value": N}, ... }`.
Interpret the status:

- **PASS** — full coverage, full traceability, no warnings, formatting valid. Hand off to
  `/s2t-quality`.
- **CONDITIONAL_PASS** — coverage complete but generation warnings remain. Present them; advance only
  after `approve_gate` (`stageId: "generate"`, `approved: true`), or resolve and re-run. In a sized
  unit, this also happens when the number of test cases is outside the band for the class. Explain
  to the user why the count is outside the band. Then ask them to approve with a reason, or fix the
  count and run the gate again.
- **FAIL** — missing mandatory coverage, an untraced test case, a dangling requirement reference, or
  a contract/`Name:`-formatting violation. In a sized unit, also: a test case with no anchor type in
  a SIMPLE or MEDIUM story, or a test case whose Test Types list is over the cap for the class. Fix
  and re-run; approval cannot lift a FAIL.

**Feature-interaction carry-through (advisory)**: Gate 2 also runs an advisory `reference_chain` that
checks every `INT-###` row in `analysis.md` is referenced by an `Interaction Ref` in `test-cases.md`
(and, optionally, that no `Interaction Ref` is dangling). A missing or dangling interaction reference
is a **non-blocking** issue that yields CONDITIONAL_PASS (requiring recorded approval to advance) — it
NEVER causes a FAIL. It coexists with the blocking `REQ-###` `reference_chain` and the `matrix_coverage`
check for the Interaction test-type column, which are unchanged.

## Rules

- Operate only on the active unit; use `force: true` to update `test-cases.md`.
- Format 2: never change the Story Setup or a setup step without asking the user first.
- Never invent requirements; every test case (including Exploratory and Interaction) must trace to
  a REQ/BR/VR/approved source — no untraceable coverage.
- All eight test types (Functional, Negative, Edge, Boundary, Performance, Stress, Exploratory,
  Interaction) are mandatory coverage — none may be skipped or omitted.
- Do not advance to `/s2t-quality` until Gate 2 is PASS, or CONDITIONAL_PASS with a recorded approval.
- No network access.

