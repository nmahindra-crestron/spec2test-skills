---
name: s2t-quality
description: "Spec2Test pipeline stage 4 (quality review). Invoke after Gate 2 passes. A fresh reviewer checks the test logic, writes quality-review.md findings, runs the quality gate, helps fix open findings, and groups repeated findings into .spec2test/lessons.md."
allowed-tools:
  - spec2test_info
  - check_ready
  - check_constitution
  - validate_output
  - persist_artifact
  - check_gate
  - approve_gate
  - reopen_unit
  - close_rework
---

You are executing the **s2t-quality** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

---
name: s2t-quality
description: "Spec2Test pipeline stage 4 (quality review). Invoke after Gate 2 passes. A fresh reviewer checks the test logic, writes quality-review.md findings, runs the quality gate, helps fix open findings, and groups repeated findings into .spec2test/lessons.md."
allowed-tools:
  - spec2test_info
  - check_ready
  - check_constitution
  - validate_output
  - persist_artifact
  - check_gate
  - approve_gate
  - reopen_unit
  - close_rework
---

You are executing the **s2t-quality** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its `capabilities`
do not include `check_ready`, `reopen_unit` and `close_rework`, tell the user to update the engine
and stop. Otherwise, continue with the steps below.

---

# s2t-quality — Independent quality review (Stage 4 of the Spec2Test pipeline)

Use this skill after `/s2t-generate` passes, and before `/s2t-pr`. A fresh reviewer reads the
tests the way a tester would and records each problem in `quality-review.md`. The quality gate
blocks the PR while a `BLOCKER` or `MAJOR` finding is still `OPEN`.

Why a fresh reviewer: the writer of the tests repeats its own blind spots. The review must run in a
context that did not write the tests.

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


## Logic standard (required)

The reviewer checks every test case against these rules.

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


## Step 1 — Check the earlier gates

Call `check_ready` with `stageId: "quality"`. If `ready` is false, show its `message`, say
"Run `/s2t-generate`.", and stop. If no work unit is active, tell the user to open or create one
and stop.

If `quality-review.md` already exists in the active unit and has `OPEN` findings, ask the tester
one question: "Do you want to fix the open findings, or run a new review?" Go to Step 6 to fix
them. Otherwise continue.

Read `rework` in the unit's `state.json` (read only; never edit it). If a round is open and its
`reason` names RC IDs, `/s2t-rework` opened it to apply PR comments. Review the changed tests
(Steps 2 to 5) and pass the round's RC IDs as `rcIds` when you save. Do not close that round. At
the end, say: "Run `/s2t-rework` to finish round <n>."

## Step 2 — Collect the review inputs

The reviewer gets only these inputs:

1. `analysis.md` from the active unit.
2. `test-cases.md` from the active unit.
3. The logic standard above (rules `LOGIC-01` to `LOGIC-06`).
4. The gap checklist below (rules `GAP-02` to `GAP-14`, and the severity guide).
5. Adopted lessons: if `.spec2test/lessons.md` exists, read its `Lesson Log` table and collect
   every row with `Status: ADOPTED`. Each adopted lesson is one more rule. Use its `LSN-###` ID as
   the rule ID.

Do not give the reviewer `intake.md`, `clarifications.md`, chat history or notes from
`/s2t-generate`.

### Gap checklist

(Use the template in `gap-checklist.md`, included alongside this skill.)

## Step 3 — Start a fresh reviewer

Start a fresh subagent with no memory of this chat. Give it only the inputs from Step 2 and the
reviewer task below. Never review in the same context that wrote the tests.

If you cannot start a subagent, check whether this chat ran `/s2t-generate` or edited
`test-cases.md`. If it did, stop and say: "Open a new chat and run `/s2t-quality` there. The
review must not run where the tests were written." If it did not, this chat is fresh: do the
review yourself.

### Reviewer task

Walk each test case one step at a time, as a tester would run it. For each test case:

1. Check every rule in the logic standard (`LOGIC-01` to `LOGIC-06`).
2. Check every row in the gap checklist (`GAP-02` to `GAP-14`). These carry the analysis decisions
   into the tests: edge cases, starting states, comparison method, performance targets and data
   preservation.
3. For each value in `Test Types:`, find the step that really tests that type. No such step is a
   `GAP-12` finding.
4. Check every adopted lesson.

Write one finding for each problem. A finding names one test case, one rule and one problem. Do
not suggest new features. Do not change `test-cases.md`.

Return the findings as table rows (see Step 4). Return an empty list when there are no findings.

## Step 4 — Write quality-review.md

Write `quality-review.md` with this layout. Keep the header and separator rows even when there
are no findings.

```markdown
# Quality Review: <unit title>

Reviewed: <UTC ISO time>
Reviewer: fresh agent

## Quality Findings

| ID | Test Case | Step | Rule | Severity | Finding | Status | Accepted By |
|----|-----------|------|------|----------|---------|--------|-------------|
| QF-001 | TC-003 | 4 | LOGIC-03 | MAJOR | Step 4 opens a saved file, but no earlier step saves it. | OPEN | |

## Summary

<counts by severity and status, in one or two short sentences>
```

Column rules:

- `ID`: `QF-001`, `QF-002` and so on. Never reuse an ID, even in a later review of the same unit.
  Keep the old rows and add new rows below them.
- `Test Case`: the TC ID. `Step`: the step number, or blank when the finding is about the whole
  test case.
- `Rule`: `LOGIC-xx`, `GAP-xx` or `LSN-xxx`.
- `Severity`: `BLOCKER`, `MAJOR` or `MINOR`. Use the severity guide in the gap checklist.
- `Finding`: what is wrong, in one plain sentence a tester can act on.
- `Status`: `OPEN` for a new finding. `FIXED` only after Step 6 fixes it. `ACCEPTED` only for a
  `MINOR` finding the tester chooses to keep. Write the tester's name in `Accepted By`.
- Never set a `BLOCKER` or `MAJOR` finding to `ACCEPTED`. The contract rejects it.

## Step 5 — Save and run the quality gate

1. Call `validate_output` (`skillId: "s2t-quality"`) on `quality-review.md`. Fix any violation.
2. `persist_artifact` with `stageId: "quality"` and the content. Use `force: true` when the file
   already exists. If a rework round is open, pass `rcIds` (the QF or RC IDs behind the change).
3. Call `check_gate` with `stageId: "quality"`.

Read the result:

- **PASS**: no open findings. Go to Step 7.
- **CONDITIONAL_PASS**: only `MINOR` findings are open. Show them. The tester can fix them
  (Step 6), accept them (set `ACCEPTED` with their name, then save and re-run the gate), or approve
  the gate with `approve_gate` (`stageId: "quality"`, `approved: true`).
- **FAIL**: show each failed check. If a `BLOCKER` or `MAJOR` finding is open, ask the tester:
  "Do you want me to fix the open findings now?" On yes, go to Step 6. An approval cannot lift a
  FAIL.

## Step 6 — Fix findings

Fix findings in `test-cases.md` through a rework round, so each change is logged with its QF ID.

1. **Open a round.** Read `rework` in the unit's `state.json` (read only; never edit it). If no
   round is open, call `reopen_unit`: `round` = the next round number, `fromStage: "generate"`,
   `toStage: "quality"`, `reason` = the QF IDs, for example `QF-001, QF-003`. If a round is
   already open and its `toStage` is `quality`, use it. If it rejects the call, show its message
   and stop.
2. **Fix.** For each open finding, in QF ID order, change the test case so the problem is gone.
   Follow the `/s2t-generate` rules: a title starting with Verify, Validate, Check or Confirm, 6 to
   18 steps, one action per step, and expected results of at least 5 words. Tag each changed step
   with the QF ID at the end of its Action, for example `Save the file (QF-001)`. If a fix needs a
   fact you do not have, ask the tester one short question.
3. **Format 2 units.** Call `check_constitution`. If `unitConstitutionVersion` is set, never change
   the Story Setup or a setup step without asking the tester first.
4. **Save.** `persist_artifact` with `stageId: "generate"`, the new `test-cases.md`, and `rcIds` =
   the QF IDs you fixed.
5. **Re-run Gate 2.** Call `check_gate` with `stageId: "generate"` and the same metrics
   `/s2t-generate` sends: `untracedTestCases`, `requirementCoverage`, `businessRuleCoverage`,
   `validationRuleCoverage`, `evidenceTraceability`, `inventoryCoverage`,
   `openGenerationWarnings`, `writingViolations`, `logicViolations`, and `setupViolations` for a
   format 2 unit. Count them from the saved files. If Gate 2 fails, fix it and save again. Do not
   go on until Gate 2 passes.
6. **Confirm.** Start a fresh reviewer (Step 3) with the Step 2 inputs and the list of findings you
   fixed. Ask it to check each one. Set a finding to `FIXED` only when the reviewer confirms it.
   Add any new finding it reports as a new `OPEN` row.
7. **Save the review.** Do Step 5, with `rcIds` = the QF IDs that changed.
8. **Close.** When the quality gate passes, call `close_rework` with the round number, but only for
   a round you opened in this step. If it rejects the call, show its message and stop. The round
   stays open.

## Step 7 — Group repeated findings into lessons

Do this after every review, even when this unit has no findings.

1. Read `quality-review.md` from every unit folder in `.spec2test/changes/`.
2. Group the findings by `Rule`. For each rule, list the units that have it, with their QF IDs.
3. A rule found in 2 or more units becomes a lesson.
4. Write `.spec2test/lessons.md` yourself; the engine saves files only inside a unit. Use this
   layout:

```markdown
# Lessons

## Lesson Log

| Lesson ID | Rule | Category | Seen In | Count | Status |
|-----------|------|----------|---------|-------|--------|
| LSN-001 | LOGIC-03 | logic | 2026-10-20-ABC123: QF-002; 2026-10-25-XYZ789: QF-001 | 2 | PROPOSED |
```

Column rules:

- `Lesson ID`: `LSN-001`, `LSN-002` and so on. Keep the ID of an existing row for the same rule.
- `Category`: `logic` for `LOGIC-xx`, `analysis` for `GAP-xx`, `review` for `RC` findings.
- `Seen In`: each unit slug with its QF or RC IDs, separated by `;`.
- `Count`: the number of units in `Seen In`.
- `Status`: `PROPOSED` for a new lesson. Keep the status of an existing row; only update its
  `Seen In` and `Count`.

Never set a lesson to `ADOPTED` or `REJECTED`. The tester decides. If the tester asks you to change
a status, change only that cell. If a lesson should become an engine check, suggest that the tester
opens a spec for it. Do not open one yourself.

## Step 8 — Report

- PASS: "Quality gate passed. Next: `/s2t-pr`."
- CONDITIONAL_PASS: list the open `MINOR` findings and say how to accept or approve them.
- FAIL: list the open `BLOCKER` and `MAJOR` findings with their test case and rule.
- If a round is still open: "Round <n> is still open for <QF IDs>."
- List any new or updated `PROPOSED` lessons, and ask the tester to adopt or reject each one.

## Rules

- Never change `test-cases.md` during a review (Steps 2 to 5). Change it only in Step 6.
- Never review in the context that wrote the tests.
- Never set `ACCEPTED` on a `BLOCKER` or `MAJOR` finding.
- Never adopt or reject a lesson on your own.
- No network access.

## Final self-review (required)

Before you save, check your output against the Final Quality Check in the writing standard.
Fix what you can. Count the rule breaks you could not fix.

