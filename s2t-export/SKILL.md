---
name: s2t-export
description: "Spec2Test pipeline stage 6 (export). Invoke on approved test-cases to validate export readiness and deliver the xlsx; records export-summary.md."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - run_export
---

You are executing the **s2t-export** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

---
name: s2t-export
description: "Spec2Test pipeline stage 6 (export). Invoke on approved test-cases to validate export readiness and deliver the xlsx; records export-summary.md."
allowed-tools:
  - spec2test_info
  - persist_artifact
  - check_gate
  - approve_gate
  - run_export
---

You are executing the **s2t-export** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

# s2t-export — Deliver (Stage 6 of the Spec2Test pipeline)

Use this skill after `/s2t-hydrate` passes. It validates export readiness and
delivers an **xlsx** export. It NEVER creates, modifies, or adds test cases or coverage — validation,
transformation, and export only.

Drive everything through the engine tools.

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


## Step 1 — Validate readiness

Confirm the active unit's `test-cases.md` is ready to export:

- Gate 2 is PASS or approved CONDITIONAL_PASS, and the review and hydrate gates permit advancement (the engine
  also blocks stage 6 and the export tool until this holds).
- Every test case has the required export-facing data for the approved xlsx layout (Test Case ID,
  Name, User Story, Test Repository Path, Test Type, Feature Area, Requirement ID, Setup Details,
  Pre-requisite, Purpose, Step Action, Expected Results). Export never creates or alters coverage —
  it only validates and transforms.
- Traceability is intact (each test case traces to REQ/BR/VR/approved source) and Coverage Intent is
  valid and matches its source.

If readiness fails, stop and report the specific failures — do not export.

## Step 2 — Export to xlsx

Call the `run_export` tool with the format spec `test-cases-xlsx@1`. The engine:

- blocks if `test-cases.md` violates its contract (Principle III),
- blocks unless every earlier gate, up to and including `hydrate`, permits advancement,
- otherwise parses `test-cases.md` and writes `export/test-cases-xlsx@1.xlsx` — a derived,
  **gitignored** deliverable (never committed).

The column set lives entirely in the format spec (content) — changing columns is a content edit.

The sheet has one row per step. Test case fields are on the first row of each test case only.
Test Case ID is `1, 2, 3 …` in `test-cases.md` order, and Test Type is always `Manual`.
Interaction Type, Interaction Ref, and Existing Feature(s) stay in `test-cases.md` and are not
exported.

### Step 2A — Rename the exported xlsx to include the story identifier

After the `run_export` tool succeeds, rename the output file so it is identifiable by story. Derive
`$storyId` from the intake's `Number` field (e.g. `CONSIM-2617` in Jira-key mode, or the change
title in manual mode). Run these terminal commands (substitute actual values):

```
$exportDir = ".spec2test\changes\<slug>\export"
$storyId   = "<Jira issue key or change title>"    # e.g. CONSIM-2617
$src  = "$exportDir\test-cases-xlsx@1.xlsx"
$dest = "$exportDir\test-cases-$storyId.xlsx"

Rename-Item $src $dest
```

Use `$dest` as the output path in the export-summary.md `Output File` field.

## Final self-review (required)

Before you save, check your output against the Final Quality Check in the writing standard.
Fix what you can. Do not change text copied from Jira, Confluence, or user answers.
Count the rule breaks you could not fix.

## Step 3 — Record the export summary

Build `export-summary.md` from this template and persist it as the stage-4 artifact (the committed
record of the run; the xlsx itself stays gitignored):

(Use the template in `export-summary-template.md`, included alongside this skill.)

Fill the `Test Case ID Mapping` table: one row per test case, in `test-cases.md` order. Sheet ID is
the test case's position (1, 2, 3 …); Test Case is its `TC-###`. This is the same order the export
uses.

Write it with `persist_artifact` — `stageId: "export"` — then `check_gate(stageId: "export")` to
record the export gate (artifact present + contract valid).

## Rules

- Never create new test cases, add coverage, or alter test intent or traceability.
- Never silently ignore validation failures.
- Require human approval when Gate 2 is CONDITIONAL_PASS or warnings exist.
- Exports are derived and gitignored; only the markdown artifacts (`test-cases.md`,
  `export-summary.md`) are the committed source of truth.
- No network access.

