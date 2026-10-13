# Comment Capture

How `/s2t-rework` reads PR comments, stores them in `review-comments.md`, and posts queued
replies. The engine is not involved. You run `gh` in the terminal.

## Read the PR

Run one query. Fill in the owner, the repo and the PR number:

```
gh api graphql -F owner=<owner> -F repo=<repo> -F number=<number> -f query='
query($owner: String!, $repo: String!, $number: Int!) {
  repository(owner: $owner, name: $repo) {
    pullRequest(number: $number) {
      author { login }
      reviewThreads(first: 100) {
        nodes {
          id
          path
          line
          comments(first: 100) {
            nodes { id body createdAt author { login __typename ... on User { name } } }
          }
        }
      }
      reviews(first: 100) {
        nodes { id state body submittedAt author { login __typename ... on User { name } } }
      }
      comments(first: 100) {
        nodes { id body createdAt author { login __typename ... on User { name } } }
      }
    }
  }
}'
```

- `pullRequest.author.login` is the submitter.
- `reviewThreads` holds the comments on file lines.
- `reviews` holds the summary a reviewer writes when they approve or request changes.
- `comments` holds the general comments in the PR conversation.

Only the first 100 threads, reviews and comments are read. Reading more comes in a later change (E).

## Skip rules

Skip a comment when any of these is true:

- it is from a bot: `author.__typename` is `Bot`, or the login ends in `[bot]`;
- it is from the submitter;
- its comment ID is already stored: some `Text` in Review Comments or Reviewer Clarifications
  ends with `<!-- c:<comment id> -->`;
- it is a review with an empty body.

An edited comment keeps the text first stored. A deleted comment keeps its row.

## Answers to our questions

Do this first, for each triage row with Status `AWAITING_REVIEWER`:

1. Find our question. It is the latest submitter comment whose body starts with `[RC-xxx]`
   (the row's RC ID). Look in the comment's thread (its `Thread Ref`). When `File/Line` is `PR`,
   look in the PR conversation `comments` instead.
2. If there is no question, store nothing for this row.
3. Every later comment (by `createdAt`) that is not skipped is an answer. When `File/Line` is
   `PR`, a comment is an answer only if its body contains the RC ID.
4. Add each answer as a row in `## Reviewer Clarifications`, with the next free `RCC-xxx` ID and
   the RC ID it answers. Fill the other cells with the mapping below.
5. If at least one answer was added, set the triage row's Status to `CLARIFIED`.

Our questions on `PR` comments end with "Please start your reply with RC-xxx." A reply without
the ID is not an answer. It becomes a new review comment, and the tester sees it in triage.

## New comments

Every other comment that is not skipped is a new review comment. This includes:

- each comment in a review thread, including a new comment in a thread that is already stored
  (it gets the same `Thread Ref`);
- each review with a body;
- each PR conversation comment.

Add each one as a row in `## Review Comments` with the next free `RC-xxx` ID. Add a matching
row in `## Triage & Resolution`:

| RC ID | Target Stages | Category | Decision | Status | Question Asked | Reply | Reply Posted |
|-------|---------------|----------|----------|--------|----------------|-------|--------------|
| RC-xxx | — | — | PENDING_TRIAGE | OPEN | — | — | — |

## Mapping

| Column | Value |
|--------|-------|
| Round | the `Review Round:` value in `pr-review.md` |
| Reviewer Name | `author.name`; the handle when the name is empty or missing |
| Reviewer Handle | `author.login` |
| Date | the comment's `createdAt`; for a review, its `submittedAt` |
| Thread Ref | the thread `id`; for a review, the review `id`; for a conversation comment, the comment `id` |
| File/Line | `<path>:L<line>` for a thread (just `<path>` when `line` is empty); `PR` for a review or a conversation comment |
| Text | the body exactly as written, then a space and `<!-- c:<comment id> -->` |

In `Text`, write a pipe as `\|` and a line break as `<br>`. Change nothing else.

## Order

Add answers first, then new comments. In each group, add rows in `createdAt` order (then by
comment ID). The same PR data always gives the same file, so a second run changes nothing.

## Post queued replies

For each triage row with `Reply Posted: No`:

1. Pick the text: `Reply` when it is not `—`, otherwise `Question Asked`.
2. Start it with `[RC-xxx] ` (the row's RC ID). The next sync finds our question by this prefix.
3. Post it:
   - to a thread (`File/Line` is not `PR`):

     ```
     gh api graphql -f query='mutation($thread: ID!, $body: String!) {
       addPullRequestReviewThreadReply(input: {pullRequestReviewThreadId: $thread, body: $body}) {
         comment { id }
       }
     }' -f thread=<Thread Ref> -f body=<text>
     ```

   - to the PR conversation (`File/Line` is `PR`):
     `gh pr comment <number> --body-file -` with the text on standard input.
4. On success, set `Reply Posted` to `Yes`. On failure, leave `No` and show the error.
