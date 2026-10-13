# Fix analysis.md

How `/s2t-rework` applies review comments to `analysis.md` in an open rework round.

Save every file in this asset with `stageId: "analysis"`. Saves under `stageId: "review"` are
blocked while the round is open.

## Which comments to handle

Read `review-comments.md`. Handle a comment when all of these are true:

- its Target Stages include `analysis`;
- its Status is `OPEN` or `CLARIFIED`;
- its Decision changes a stage file (not `NO_CHANGE` or `REJECTED_WITH_REASON`).

Skip a comment when Review Comment Details in `analysis.md` already has a row with its RC ID and
the open round in the `Round` column. That comment is already done in this round.

Handle the comments in RC ID order. Read the saved `intake.md` first: a change there may need a
matching change here.

## For each comment

1. **Covered?** If `analysis.md` already does what the comment asks, change nothing. Note the
   evidence: the IDs or a short quote.
2. **Unclear?** First look in `clarifications.md` → Review Comment Clarifications for a row with
   this `RC ID`. If there is one, use its answer and do not ask again. If there is none, ask the
   tester one short, plain question. Add the answer as a new row in that section:
   - `ID`: the next `CLR-n`; `Stage`: `analysis`; `Recorded At`: now (UTC ISO);
   - `Recorded By`: the tester; `Target`: the affected ID, or the RC ID; `Resolution`: `ANSWERED`;
   - `Question` and `Answer`: what you asked and what the tester said;
   - `Source: user interview`, `RC ID`: the comment, `Asked Of: USER`.

   Save it with `persist_artifact`: `stageId: "analysis"`, `artifact: "clarifications.md"` and
   `rcIds` = this RC ID.
3. **Ask the reviewer instead?** Use the rules in "Ask the reviewer instead" in the skill (Step 10).
   If one applies, change nothing for this comment. Set its Status to `AWAITING_REVIEWER` and its
   Decision to `NEEDS_REVIEWER_CLARIFICATION`. Write the question in `Question Asked` and set
   `Reply Posted: No`. Save `review-comments.md` with `stageId: "analysis"`,
   `artifact: "review-comments.md"` and `rcIds` = this RC ID.
4. **Apply.** Edit `analysis.md` in place. Change only what the comment asks for.

Knock-on edits: a new or changed `REQ`, `BR` or `INT` also needs its row in the Coverage Intent
Matrix and the Requirement Traceability Matrix. Make those edits in the same save. A new `REQ`
needs its source in the Evidence Register.

Save every `clarifications.md` change before `check_gate` runs for this stage.

## Review Comment Details

Add one row for each comment you applied in step 4. Do not add a row for a covered comment.

```markdown
## Review Comment Details

| RC ID | Round | Reviewer | Date Applied | Change Summary | Affected IDs | Clarification Ref |
|-------|-------|----------|--------------|----------------|--------------|-------------------|
| RC-001 | 1 | Jane Doe | 2026-10-14T12:00:00Z | Added REQ-002 for the locked account. | REQ-002 | — |
```

- `Round`: the open round number. `Reviewer`: the comment's Reviewer.
- `Date Applied`: now (UTC ISO). `Change Summary`: one short sentence.
- `Affected IDs`: the IDs you changed, or `—`. `Clarification Ref`: the `CLR-n` you used, or `—`.

Keep Review Comment Details as the last section. Add the section if it is missing. Keep older rows
as they are; add new rows below them.

## Save and run the gate

Do this when `analysis.md` changed, or when the intake gate ran again in this round. A new intake
gate makes the analysis gate out of date, even if `analysis.md` did not change.

1. Run the Final self-review in the skill.
2. If `analysis.md` changed: `persist_artifact` with `stageId: "analysis"`, the `analysis.md`
   content and `rcIds` = the RC IDs you applied.
3. Count the gate metrics in the saved `analysis.md`:
   - `unsupportedItems`: `REQ`, `BR` and `VR` items with no Evidence Register row.
   - `openConflicts`: conflicts with Status `OPEN`.
   - `highGaps`: requirement gaps with severity `HIGH` and Status `OPEN`.
   - `qualityScore`: the 0 to 100 score in the Gate Result section.
   - `openAdvisoryItems`: `OPEN` gaps with severity `MEDIUM` or `LOW`, `UNRESOLVED` ambiguities,
     assumptions that are not `CONFIDENT`, and `[SKIP]` items.
   - `writingViolations`: the rule breaks from the Final self-review that you could not fix.
4. `check_gate` with `stageId: "analysis"` and the six metrics, for example
   `metrics: { "unsupportedItems": { "value": 0 }, ... }`.
   - PASS: go on to the next stage.
   - CONDITIONAL_PASS: show the issues and ask the tester one short, plain question: approve or
     fix? Call `approve_gate` only on a clear yes.
   - FAIL: stop. Show the failed checks. The round stays open.

If `analysis.md` did not change and the intake gate did not run again, do not save it and do not
run its gate.
