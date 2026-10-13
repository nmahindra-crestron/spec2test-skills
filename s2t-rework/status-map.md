# Status Map

How `/s2t-rework` turns `gh pr view` data into `pr-review.md` values.

## PR Status

The first matching row wins. `any` matches every value. `empty` means `reviewDecision` is an
empty string.

| GitHub state | isDraft | reviewDecision | Status |
|--------------|---------|----------------|--------|
| MERGED | any | any | MERGED |
| CLOSED | any | any | CLOSED |
| OPEN | true | any | DRAFT |
| OPEN | false | APPROVED | APPROVED |
| OPEN | false | CHANGES_REQUESTED | CHANGES_REQUESTED |
| OPEN | false | REVIEW_REQUIRED | REVIEW_REQUIRED |
| OPEN | false | empty | OPEN |

`REJECTED` has no GitHub source. It stays in the allowed list so the format does not change later.

## Reviewer Decision

Use each reviewer's latest review (`latestReviews`, matched by `author.login` = Handle).

| GitHub review state | Decision |
|---------------------|----------|
| APPROVED | APPROVED |
| CHANGES_REQUESTED | CHANGES_REQUESTED |
| COMMENTED | COMMENTED |
| DISMISSED | DISMISSED |
| PENDING | PENDING |
| (no review yet) | PENDING |
