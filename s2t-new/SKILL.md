---
name: s2t-new
description: "Spec2Test pipeline stage 1 (intake). Invoke to start a new change: interview the user, produce a structured intake.md, and run Gate 0."
allowed-tools:
  - spec2test_info
  - create_work_unit
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-new** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

---
name: s2t-new
description: "Spec2Test pipeline stage 1 (intake). Invoke to start a new change: interview the user, produce a structured intake.md, and run Gate 0."
allowed-tools:
  - spec2test_info
  - create_work_unit
  - persist_artifact
  - check_gate
  - approve_gate
---

You are executing the **s2t-new** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.2.0`, tell the user to update the engine to `0.2.0` or newer and stop.
Otherwise, continue with the steps below.

---

# s2t-new — Collect (Stage 1 of the Spec2Test pipeline)

Use this skill to start the Spec2Test pipeline for a new user story or feature. It creates a
**work unit** and produces a structured `intake.md`, then runs **Gate 0**.

You drive this through the Spec2Test engine tools — do not hand-create folders or slugs.

## What this skill does

1. Create a work unit (the engine mints the slug and folder, and makes it active).
2. Detect input mode (Jira key vs manual details), gather available context, and produce `intake.md`
  from the template in one pass, marking every missing field `[GAP]`.
3. Interview the user to resolve `[GAP]`s (critical ones first).
4. Run **Gate 0** and interpret its tri-state result.

## Input detection (required)

Detect mode from the user's first message before drafting intake:

- **Jira-key mode**: input includes a Jira key matching `[A-Z][A-Z0-9_]+-\d+`.
- **Manual mode**: no Jira key is present.

If both a Jira key and manual story text are present, ask the user which source should be primary
for this intake session before proceeding.

After initial prefill, both modes MUST follow the exact same lifecycle: persist intake, resolve
`[GAP]`s via interview, re-persist with `force: true`, and run Gate 0.

## Memory context (recommended)

Before drafting intake details, check whether `docs/s2t-memory/index.md` exists in the workspace.
If present, use it to understand existing behavior domains and avoid asking redundant questions
that are already covered by durable project memory.

## Step 1 — Create the work unit

Summarize the user's request into a short **change name** (3–6 words, lowercase-kebab-case).

Call `create_work_unit` with that name as the `title`. The engine allocates a unique slug of the
form `YYYY-MM-DD-<6×[A-Z0-9]>`, creates `.spec2test/changes/<slug>/`, and sets it active. All
subsequent tools act on this active unit. **Never invent a slug or path yourself.**

### Step 1A — Rename the work unit folder (Jira-key mode only)

After `create_work_unit` returns in Jira-key mode, rename the folder to append the Jira issue key
so it is human-identifiable (e.g. `2026-08-14-LD0ULK-CONSIM-2617`). Do this immediately, before
writing any artifacts, using these exact terminal commands (substitute actual values):

```
$oldSlug = "<slug returned by create_work_unit>"          # e.g. 2026-08-14-LD0ULK
$jiraKey = "<Jira issue key>"                              # e.g. CONSIM-2617
$base    = ".spec2test\changes"
$newSlug = "$oldSlug-$jiraKey"

Rename-Item "$base\$oldSlug" "$base\$newSlug"
Set-Content ".spec2test\active" $newSlug -NoNewline
$state = Get-Content "$base\$newSlug\state.json" | ConvertFrom-Json
$state.slug = $newSlug
$state | ConvertTo-Json -Depth 20 | Set-Content "$base\$newSlug\state.json"
```

Use `$newSlug` as the canonical slug for all subsequent references (intake `Slug:` field, artifact
paths, etc.). In manual mode, skip this step — the engine slug is used as-is.

## Step 2 — Draft intake.md

Fill this template from whatever the user provided, then write it:

(Use the template in `intake-template.md`, included alongside this skill.)

The template includes headings such as `## User Story`, `## Description`, and
`## Acceptance Criteria`.

Rules:

- Populate every section from the user's text. Set `Date` to today and `Slug` to the unit's slug.
- Any field or section with no information → write `[GAP]`. **Never leave a section blank.**

### Step 2A — Jira-key mode prefill

When in Jira-key mode, call the configured Jira MCP tool with the issue key and extract available
story data before filling intake.

Mapping requirements:

- **User Story**
  - `Name` ← Jira summary (or `[GAP]`)
  - `Number` ← Jira issue key
- **Jira Metadata**
  - `Issue Key`, `Status`, `Priority`, `Sprint`, `Labels`, `Components`, `Linked Issues` from Jira.
  - **Do not extract personal identity fields** from Jira (`Assignee`, `Reporter`): leave those rows
    as `[GAP]`.
- **Description**
  - Map Jira description body (or `[GAP]` if unavailable).
- **Acceptance Criteria**
  - Parse from description/comments only, then normalize extracted criteria into checklist items.
  - Check all of the following sources in order — use the **first** that yields content (OR logic):
    1. **Known AC custom fields** — check these Jira custom fields first, as Jira instances often
       store the Acceptance Criteria section as a dedicated custom field rather than inline in the
       description: `customfield_10095`, `customfield_10031`, or any custom field whose key or
       rendered label contains `Acceptance Criteria`, `AC`, or `Definition of Done`.
    2. **Named section anywhere in the description** — any section titled `Acceptance Criteria`,
       `AC`, `Definition of Done`, or a close variant, regardless of where it appears in the
       description body (beginning, middle, or end, including after sections like "Behavioral
       Differences" or "Clarifications").
    3. **Given/When/Then blocks** anywhere in the description or comments.
    4. **Any numbered or bulleted statements** anywhere in the description body — including bullets
       describing behavior, clarifications, or requirements even without a named heading. Any
       substantive bulleted or numbered content qualifies; do not require "criteria" language.
    5. **Comment fallback** — bulleted or numbered lists in comments describing verified/expected
       behaviors (e.g. "covered and certified" lists, sign-off summaries), tagged `[SUPPLEMENT]`.
  - Scan the **full** description and all non-null custom fields before concluding no AC exists.
  - Normalize all extracted items into checklist items.
  - If no credible criteria can be extracted from any of the above, set section to `[GAP]`.
- **Design Document**
  - Classify extracted references:
    - attachment references → `Attachments`
    - Confluence links → `Confluence Links`
    - Figma links → `Figma Links`
    - remaining spec/architecture links → `Spec Links`
  - Missing subsections remain `[GAP]`.
- **Existing Test Coverage**
  - Populate `Existing Test Case References` from linked/mentioned test assets where available.
  - Populate `Existing Test Cases` from freeform test details in description/comments where present.
  - Ensure a `### Known Coverage Gaps` subsection exists in intake output.
  - Populate `Known Coverage Gaps` for extracted acceptance criteria without matched test evidence.
  - Use `[GAP]` when a subsection cannot be populated.

For each Jira-derived value written into intake, include a provenance marker such as
`[SOURCE: Jira ABC-123]`.

### Step 2B — Manual mode prefill

When in manual mode, populate intake from user-provided story details and mark unknowns as `[GAP]`.

Persist it with `persist_artifact` — `stageId: "intake"`, `content: <the filled markdown>`. When you
re-write `intake.md` on later turns (after interview answers), pass **`force: true`** so the engine
accepts the update instead of returning `reconcile-required`.

### Step 2C — Jira fallback behavior

If Jira retrieval fails (invalid key, inaccessible issue, unauthorized, timeout, or tool error),
present a short reason and continue in manual mode in the same session. Preserve already collected
context and proceed with normal intake drafting/interview behavior.

## Step 3 — Intake completeness interview

After the first write, inspect the sections still marked `[GAP]` and interview the user to resolve
them. Ask **critical** items first, then optional ones. Group related questions.

**Every question is mandatory — the user must provide an explicit answer.** Do not move on until
the user responds to each question. The user may answer `[SKIP]` for any question to indicate the
information is unavailable, but silently ignoring a question or receiving no answer is not
acceptable — re-ask unanswered questions before proceeding.

**Questions MUST be asked using the `vscode_askQuestions` tool** — never stated as plain text in
the chat. Each gap must become a distinct question entry in the tool call. Do not list gaps as
bullet points or prose and wait for a reply; always use the tool so the user receives a structured
prompt they must respond to explicitly.

**Image attachments are welcome**: for any question where a screenshot, diagram, or design mockup
would help (e.g. UI layout, AC from a screen, design document), tell the user in the question text
that they may attach an image directly in their chat reply in addition to typing an answer. After
the user responds, inspect their chat message for attached images and extract any relevant
information from them (text, UI elements, AC items, field names, etc.) exactly as you would from
a typed answer. Treat image-derived content as `[SUPPLEMENT]` and note the source as
"user-attached screenshot".

Update `intake.md` via `persist_artifact` (`force: true`) after each round of answers.

Critical sections (must be populated to pass Gate 0):

- **User Story**
- **Description**
- **Acceptance Criteria**

All other sections are non-critical: leaving them `[GAP]` does not fail the gate, but yields a
CONDITIONAL result that needs human sign-off.

Tag information the user supplies later with `[SUPPLEMENT]`.

## Step 4 — Run Gate 0

Count the remaining gaps in the current `intake.md`:

- `criticalGaps` = number of the three critical sections still unpopulated (`[GAP]`/empty).
- `nonCriticalGaps` = number of other sections still `[GAP]` (a section the user marked `[SKIP]`
  does not count).

Call `check_gate` — `stageId: "intake"`, `metrics: { "criticalGaps": { "value": <N> },
"nonCriticalGaps": { "value": <M> } }`. Interpret the returned status:

- **PASS** — critical sections populated and no non-critical gaps. The pipeline may advance; hand off
  to `s2t-analyze`.
- **CONDITIONAL_PASS** — non-critical gaps remain. Present them to the user. To advance, either
  resolve them (update intake, re-run Step 4) or record explicit sign-off with `approve_gate`
  (`stageId: "intake"`, `approved: true`). The pipeline does not advance until approval is recorded.
- **FAIL** — a critical section is still a gap. Resolve it, re-write `intake.md`, and re-run Step 4.
  Approval cannot lift a FAIL.

## Rules

- Never overwrite another unit's `intake.md`; always operate on the active unit and use `force: true`
  to update the intake you are building.
- Every section contains captured information or `[GAP]` — never blank.
- Do not advance to `s2t-analyze` until Gate 0 is PASS, or CONDITIONAL_PASS with a recorded approval.

