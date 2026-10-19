---
name: s2t-init
description: "Spec2Test project setup. Run first, once per project: create or update .spec2test/constitution.md with the project details and the usual test setup. Run it again to change one part."
allowed-tools:
  - spec2test_info
  - check_constitution
  - save_constitution
---

You are executing the **s2t-init** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. Otherwise, continue
with the steps below.

---

---
name: s2t-init
description: "Spec2Test project setup. Run first, once per project: create or update .spec2test/constitution.md with the project details and the usual test setup. Run it again to change one part."
allowed-tools:
  - spec2test_info
  - check_constitution
  - save_constitution
---

You are executing the **s2t-init** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool before you ask the user anything. If it is unavailable, tell the
user the Spec2Test engine (MCP server) is not installed — point them at the repository README — and
stop. If its `capabilities` do not include `check_constitution` and `save_constitution`, tell the
user to update the engine and stop. Otherwise, continue with the steps below.

---

# s2t-init — Project setup (run first)

Use this skill once per project, before `/s2t-new`. It writes `.spec2test/constitution.md`: the
project name, the application under test, the platforms, and the setup a tester does before every
test. Later skills read this file. Run this skill again to change one part of it.

This is the only skill that saves the constitution. Always save it with `save_constitution`. Never
write `.spec2test/constitution.md` yourself.

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


## Step 1 — Make the folders

Create `.spec2test/` and `.spec2test/changes/` in the workspace if they are missing. Keep every
file and folder that is already there.

Never create or change `.spec2test/activation.json`. The installer owns that file.

## Step 2 — Pick the run type

Call `check_constitution`.

- `configured` is `false`: tell the user this engine has no constitution setting, and stop.
- `exists` is `false`: this is a **first run**. Go to Step 3.
- `exists` is `true`: this is a **re-run**. Go to Step 4. If `valid` is `false`, list the problems
  from `violations` and say that this run will fix them.

## Step 3 — First run

### 3A — Ask once

Show this prompt exactly, and wait for one reply:

> **Tell me about your project in one reply:**
> the project name, the application under test, and the platforms it runs on (Windows, macOS, Web,
> Mobile…). Also tell me what a tester normally creates or opens before testing (a project,
> solution, workspace, file…), and what they usually add or configure before the real test begins
> (devices, components, users, settings…). Last, give the baseline pre-requisite, for example
> "the application is installed and opens without errors."
> If you have sample test cases as CSV files, give me the folder path and I'll suggest values from
> them. Write **None** for anything that doesn't apply.

### 3B — Read CSV samples (optional)

If the user gave a folder path:

- Read only the `.csv` files in it, once. Use them only to suggest values, for example the
  usual setup steps and pre-requisite.
- If the folder holds Excel files (`.xlsx` or `.xls`), ask the user to save them as CSV first.
  Don't read the Excel files.
- If the folder is missing or has no CSV files, say so and go on without samples.
- Never store the samples or copy them into the constitution. The user's own words always win
  over a value from a sample.

### 3C — Check the reply

The project name, the application and the platforms are required. "None" is not allowed for these
three. The other values may be "None".

If a required value is missing or is "None", ask one follow-up question for the missing values.
Ask only once. If a required value is still missing after the follow-up, save nothing. Tell the
user which value is missing, and tell them to run `/s2t-init` again when they have it.

### 3D — Build the draft

Start from `(Use the template in `constitution-template.md`, included alongside this skill.)` and fill it in:

- **Project**: `Name:`, `Application:` and `Platforms:` from the reply. Leave `Version:` and
  `Updated:` as they are. The engine sets them when it saves.
- **Placeholder Defaults**: fill `application`, `workspace-type`, `items` and `settings` from the
  reply. Drop a row whose answer is "None".
- **Setup Actions**: keep all 7 generic actions. Don't put product words in the `Action` or
  `Expected Result` cells. Product words go in Placeholder Defaults.
- **Default Actions**: list the Setup Action IDs a tester does before every test, in order. Use
  `LAUNCH-APP` always. Add `CREATE-WORKSPACE` (or `OPEN-WORKSPACE`) unless the answer for what a
  tester creates or opens is "None". Add `ADD-ITEMS` or `CONFIGURE` unless the answer for what a
  tester adds or configures is "None". Leave out every action whose answer is "None".
- **Setup Details**: write the default actions as one numbered line in plain words, for example
  `1. Launch Acme Studio. 2. Create a new project. 3. Add the required components.` Write "None"
  when there are no default actions.
- **Pre-requisite**: the user's baseline pre-requisite, or "None".
- **Change Log**: leave the template row. The engine replaces it.

Then go to Step 5.

## Step 4 — Re-run

1. Show the current `.spec2test/constitution.md` in full.
2. Ask what to change. Ask one short, plain question.
3. Change only that part. Keep every other line the same.

Then go to Step 5.

## Step 5 — Confirm or Edit

Show the full draft. Ask: **Confirm or Edit?**

- **Edit**: ask what to change, change only that part, and show the full draft again. Repeat until
  the user picks Confirm.
- **Confirm**: go to Step 6.

## Final self-review (required)

Before you save, check your output against the Final Quality Check in the writing standard.
Fix what you can. Do not change text copied from user answers.

## Step 6 — Save

Call `save_constitution` with:

- `content`: the full confirmed draft.
- `change`: one short line, for example `Created by s2t-init` on a first run, or what changed on a
  re-run, such as `Added macOS to Platforms`.
- `source`: where the change came from, for example `User reply`.

If the result is `saved`, tell the user the `version` and the `path`. On a first run, tell them to
run `/s2t-new` next.

If the result is `blocked`, list each problem from `violations` in plain words, fix the draft with
the user, and go back to Step 5. The engine saved nothing.

## Rules

- Only this skill saves the constitution, and only through `save_constitution`.
- Never ask for the version or the date. The engine sets them.
- Never write `.spec2test/activation.json`.
- Never store CSV samples.

