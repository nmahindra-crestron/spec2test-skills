# Fix test-cases.md

How `/s2t-rework` applies review comments to `test-cases.md` in an open rework round.

Save every file in this asset with `stageId: "generate"`. Saves under `stageId: "review"` are
blocked while the round is open.

## Which comments to handle

Read `review-comments.md`. Handle a comment when all of these are true:

- its Target Stages include `generate`;
- its Status is `OPEN` or `CLARIFIED`;
- its Decision changes a stage file (not `NO_CHANGE` or `REJECTED_WITH_REASON`).

Skip a comment when Review Comment Details in `test-cases.md` already has a row with its RC ID and
the open round in the `Round` column. That comment is already done in this round.

Handle the comments in RC ID order. Read the saved `analysis.md` first: a new or changed `REQ`,
`BR` or `INT` there needs test cases here.

## Find the affected test cases

Use the first that works:

1. the `REQ`, `BR` or `VR` IDs the comment or the analysis change names (the `Requirement(s)`
   field and the Traceability Matrix);
2. the comment's `File/Line`, when it points into `test-cases.md`;
3. the scenario the comment describes.

## For each comment

1. **Covered?** If `test-cases.md` already does what the comment asks, change nothing. Note the
   evidence: the TC IDs or a short quote.
2. **Unclear?** First look in `clarifications.md` → Review Comment Clarifications for a row with
   this `RC ID`. If there is one, use its answer and do not ask again. If there is none, ask the
   tester one short, plain question. Add the answer as a new row in that section:
   - `ID`: the next `CLR-n`; `Stage`: `generate`; `Recorded At`: now (UTC ISO);
   - `Recorded By`: the tester; `Target`: the affected ID, or the RC ID; `Resolution`: `ANSWERED`;
   - `Question` and `Answer`: what you asked and what the tester said;
   - `Source: user interview`, `RC ID`: the comment, `Asked Of: USER`.

   Save it with `persist_artifact`: `stageId: "generate"`, `artifact: "clarifications.md"` and
   `rcIds` = this RC ID.
3. **Ask the reviewer instead?** Use the rules in "Ask the reviewer instead" in the skill (Step 10).
   If one applies, change nothing for this comment. Set its Status to `AWAITING_REVIEWER` and its
   Decision to `NEEDS_REVIEWER_CLARIFICATION`. Write the question in `Question Asked` and set
   `Reply Posted: No`. Save `review-comments.md` with `stageId: "generate"`,
   `artifact: "review-comments.md"` and `rcIds` = this RC ID.
4. **Apply.** Pick one action below and edit `test-cases.md` in place.

Save every `clarifications.md` change before `check_gate` runs for this stage.

## Pick the action

### Update (`UPDATED_STEPS`)

Use it when the comment refines the scenario of an existing test case, and the result has 6 to 18
steps.

- Change or insert steps in the Steps table.
- Renumber the steps 1, 2, 3 and so on, with no gaps.
- Tag each changed or new step: add the RC ID at the end of its Action, for example
  `Enter a locked user name (RC-001)`.
- Never update a deprecated test case. Add a new one instead.

For a format 2 unit, the Steps table is the Test Steps table. It must have 6 to 18 steps. Number
the Setup Steps from 1, and continue the Test Steps from the next number. Never change the Story
Setup or a Setup Steps row without asking the tester first.

### New (`NEW_TC`)

Use it when the comment adds a new scenario, a new data variant, a new `REQ` or `BR`, or a new
test type. Also use it when an update would go over 18 steps.

- Use the next free TC ID. Never reuse an ID, even one that is deprecated.
- Fill every field the way `/s2t-generate` does: `Name: Verify …`, `Requirement(s)`, Steps,
  Pre-requisite.
- Add its row to the Traceability Matrix.

### Deprecate (`DEPRECATED_TC`)

Use it when the comment makes a test case obsolete.

- Add the field `Status: DEPRECATED (RC-xxx)` under the test case's `Name:` line, with the RC ID.
- Never delete a test case. Export leaves out deprecated test cases.
- Keep its Traceability Matrix row.

## Review Comment Details

Add one row for each comment you applied. Do not add a row for a covered comment.

```markdown
## Review Comment Details

| RC ID | Round | Reviewer | Date Applied | Action | TC IDs | Steps Changed | Clarification Ref |
|-------|-------|----------|--------------|--------|--------|---------------|-------------------|
| RC-001 | 1 | Jane Doe | 2026-10-14T12:00:00Z | UPDATED_STEPS | TC-001 | 3 | — |
```

- `Round`: the open round number. `Reviewer`: the comment's Reviewer.
- `Date Applied`: now (UTC ISO). `Action`: `UPDATED_STEPS`, `NEW_TC` or `DEPRECATED_TC`.
- `TC IDs`: the test cases you changed. `Steps Changed`: the step numbers, or `—`.
- `Clarification Ref`: the `CLR-n` you used, or `—`.

Keep Review Comment Details as the last section. Add the section if it is missing. Keep older rows
as they are; add new rows below them.

## Save and run the gate

Do this when `test-cases.md` changed, or when an earlier stage's gate ran again in this round. A
new earlier gate makes the generate gate out of date, even if `test-cases.md` did not change.

1. Run the Final self-review in the skill.
2. If `test-cases.md` changed: `persist_artifact` with `stageId: "generate"`, the `test-cases.md`
   content and `rcIds` = the RC IDs you applied.
3. Count the gate metrics in the saved `test-cases.md`. Deprecated test cases do not count.
   - `untracedTestCases`: test cases that trace to no `REQ`, `BR`, `VR` or approved source.
   - `requirementCoverage`: the percent of `REQ` items covered (`100` when there are none).
   - `businessRuleCoverage`: the percent of `BR` items covered (`100` when there are none).
   - `validationRuleCoverage`: the percent of `VR` items covered (`100` when there are none).
   - `evidenceTraceability`: the percent of covered items backed by analysis evidence.
   - `inventoryCoverage`: the percent of FULL inventory items covered.
   - `openGenerationWarnings`: open gaps, conflicts, ambiguities and assumptions carried over as
     Generation Warnings.
   - `writingViolations`: the rule breaks from the Final self-review that you could not fix.
   - `setupViolations` (format 2 units only): the setup problems you could not fix. A setup
     problem is a merged setup step, a setup step that tests something, setup steps that don't
     match the Story Setup, or a Purpose that repeats another test case's Purpose.
4. `check_gate` with `stageId: "generate"` and the eight metrics (nine for a format 2 unit), for
   example `metrics: { "untracedTestCases": { "value": 0 }, ... }`.
   - PASS: go on to Step 14.
   - CONDITIONAL_PASS: show the issues and ask the tester one short, plain question: approve or
     fix? Call `approve_gate` only on a clear yes.
   - FAIL: stop. Show the failed checks. The round stays open.

If `test-cases.md` did not change and no earlier gate ran again, do not save it and do not run its
gate.
