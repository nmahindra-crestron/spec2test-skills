---
name: s2t-new
description: "Spec2Test pipeline stage 1 (intake). Invoke to start a new change: interview the user, produce a structured intake.md, and run Gate 0."
allowed-tools:
  - spec2test_info
  - create_work_unit
  - persist_artifact
  - check_gate
  - approve_gate
  - jira_issues
  - jira_links
  - jira_attachments
  - confluence_get_page
  - confluence_get_page_images
  - vscode_askQuestions
---

You are executing the **s2t-new** skill. Perform these steps yourself using the Spec2Test
tools — do not print or summarize this document.

## Step 0 — Engine compatibility (required)

Call the `spec2test_info` tool. If it is unavailable, tell the user the Spec2Test engine (MCP
server) is not installed — point them at the repository README — and stop. If its reported
`version` is below `0.3.0`, tell the user to update the engine to `0.3.0` or newer and stop.
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

### Step 2D — Dereference Confluence design links (best-effort)

After classifying Design Document references, dereference each **Confluence** link and capture its
content into the `### Design Extracts` table under `## Design Document`. This is best-effort: it must
never block intake.

**The `### Design Extracts` and `### Source Coverage` tables are always present**, even when there
are no Confluence links or the Confluence tool is unavailable — keep each header + separator with
zero data rows. An absent Design Extracts table fails `intake@1` validation at Gate 0, and Design
Extracts rows without a Source Coverage table fail Gate 0.

- **Fetch scope (exact page only)**: for each Confluence link, resolve the page id from supported URL
  forms (standard `/wiki/spaces/.../pages/<id>/...`, `/pages/viewpage.action?pageId=<id>`, and
  shortened `/x/<key>` tiny links) and call the configured Confluence MCP tool
  (e.g. `confluence_get_page`). Fetch **only** the exact linked page — do **NOT** recurse into child
  pages or Confluence links found inside the page body. Retain the original URL in `Confluence Links`.
- **Read the page body only** — do NOT read or extract Confluence page **comments** (out of scope).
- **Extract granularly**: read **all** sections of the page body. Write one `DEX-###` row per
  rule, limit, flow segment, or error/edge behavior — not one row per area. Keep source wording
  verbatim; **never summarize or condense**. Choose the best-fitting `Area` for each row:
  - **Universal areas**: `Scope & Boundaries`, `Architecture & Ownership`, `Data Models & Contracts`,
    `Edge Cases & Failure Modes`, `Lifecycle & Resource Cleanup`, `Performance & Constraints`,
    `Testability & Verification`.
  - **Feature-domain sub-areas** (whichever apply): `Editor / UI State`,
    `Import / Export / Migration`, `APIs / Backend Services`, `Device / Driver Systems`.
  - **Behavior areas**: `Workflow / Sequence` for flow and step-order content (what happens, in
    what order, per item type); `Business Rules` for rules, limits, and conditions that decide an
    outcome. Prefer these over a universal area when the content is a flow or a rule.
  - **Core review questions**: `Core: State Location`, `Core: Boundaries & Limits`,
    `Core: Teardown / Exit`, `Core: Batch / Multi-select`, `Core: Failure & Recovery`. For a `Core:`
    question the page does not answer, you MAY write a row with `Detail` = `unknown`; for all other
    areas, **omit** empty areas (never fabricate content or record an authoritative "none").
- **Row format** (columns in this exact order): `ID` = increasing `DEX-001`, `DEX-002`, … (unique);
  `Source` = `[SOURCE: Confluence <page-id-or-title> §<section heading>]`; `Type` = `SUPPLEMENT`;
  `Area` = one allowed value above; `Detail` = the content, encoding newlines as `<br>` and escaping
  pipes as `\|`.
- **Source Coverage** (columns in this exact order: `Source`, `Item`, `Status`, `DEX IDs`,
  `Reason`): write one row per examined page section. A page section is a heading, at any level,
  that has its own body content; a container heading with no body of its own gets no row.
  - `EXTRACTED` — `DEX IDs` lists the comma-separated `DEX-###` ids written from that section.
  - `SKIPPED` — boilerplate (table of contents, change log, related-page list) or no requirement
    content; `Reason` required. Write no DEX rows for boilerplate.
  - `NOT EXTRACTED` — the section could not be read; `Reason` required.
  - Every `DEX-###` row must be cited by at least one `EXTRACTED` row (Gate 0 checks this).
- **De-duplicate**: extract each page once, even when it is reachable by more than one route (story
  link, linked story, repeated link). One Source Coverage row per (source, item).
- **Large pages**: extract every section anyway; there is no size cap and no `[CONDENSED]` row.
- **Supplement, not authoritative**: extracted content is `SUPPLEMENT` evidence — it is distinct from
  human-authored intake and MUST NOT be promoted into Acceptance Criteria without explicit human
  confirmation.

**Fallback (never blocks intake):**

- **Confluence tool not configured/available**: record the bare URL in `Confluence Links`, keep the
  zero-row Design Extracts skeleton, add one Source Coverage row per link (`Item` = `page: <title or id>`,
  `NOT EXTRACTED`, `Reason` = `Confluence tool unavailable`), and continue.
- **Per-link failure** (inaccessible, unauthorized, 404, timeout, tool error, or an unresolvable
  URL): keep the bare URL, write no `DEX-###` row for that link, add a Source Coverage row
  (`Item` = `page: <title or id>`, `NOT EXTRACTED`, `Reason` = the failure), and continue with the remaining links
  and the rest of intake. Treat an HTML sign-in/login page response as a failure, not as page content.
- **Successful fetch but empty page** (only a title, or only images/attachments with no text): write
  zero DEX rows from its text and a Source Coverage row (`Item` = `page: <title or id>`, `SKIPPED`,
  `Reason` = `no extractable text`); its images are still handled by Step 2E.

**Determinism**: capture extracts **once** at intake time. Do NOT re-fetch on an unchanged
re-persist; only refresh extracted content when you deliberately re-run extraction and re-persist
with `force: true`.

### Step 2E — Read design images (best-effort)

Read the images on every page handled in Step 2D and on the Jira story (and on each linked story
confirmed in Step 2F).

- **Retrieval**: Confluence — `confluence_get_page_images` for each page. Jira — `jira_attachments`
  (`list`, then `get_content` for each image attachment). Images arrive inline in the tool result.
  **Never write image bytes to disk** — no downloads, no temporary files.
- **What to read**: if the story is about icons, logos, or other visual elements, read every image.
  Otherwise read every image except clearly decorative ones (spacers, avatars, generic banners),
  which get a `SKIPPED` Source Coverage row with a reason. There is no cap on the number of images.
- **Recording**: write the requirement content an image shows (flows, states, labels, limits, layout
  rules) as `DEX-###` rows with `Type` = `SUPPLEMENT` and
  `Source` = `[SOURCE: Image <file> @ Confluence <page>]` or `[SOURCE: Image <file> @ Jira <key>]`.
  Do not repeat content already extracted from the page text. Write one Source Coverage row per
  image (`Item` = `image: <file>`).
- **Failures** (retrieval error, unreadable image, or an external image the tools cannot fetch):
  Source Coverage row `NOT EXTRACTED` with the reason, and a Step 3 question asking the user to
  paste or describe the image. Intake continues.

### Step 2F — Linked stories (Jira-key mode)

Screen the story's linked issues for requirement-relevant content.

- **Scope**: issue links of any type, plus the parent (epic or parent issue) and sub-tasks (read
  with `jira_issues` / `jira_links`). Remote/web links are excluded.
- **Screen**: judge each one's relevance from its summary, type, and link type against this
  story's requirements.
- **Confirm**: ask one multi-select question (`vscode_askQuestions`) listing the linked issues, with
  the relevant ones pre-selected and free text to add other keys. Ask even when no candidate was
  detected or the story has no links.
- **Extract each confirmed story** (one level only — do not follow that story's own links to other
  stories; do not read comments):
  - its description and acceptance criteria → `DEX-###` rows with `Source` = `[SOURCE: Jira <key>]`
    and Source Coverage rows (`Item` = `description: <key>` / `acceptance criteria: <key>`);
  - its images → Step 2E;
  - its Confluence links → Step 2D.
- **Order and dedup**: finish this confirmation before running Steps 2D and 2E on linked-story
  sources. A page or image already extracted for the main story (or another linked story) is not
  extracted again and gets no second Source Coverage row.
- **Failure**: a story that cannot be fetched gets a Source Coverage row (`Item` = `story: <key>`,
  `NOT EXTRACTED`, `Reason`); intake continues.

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

**Per-type workflows**: when `## Business Workflows` (or a design extract) states that one flow
applies to several listed item types (e.g. "same flow for X, Y, Z"), ask whether any of those types
behaves differently. For each type that differs, add a separate `[SUPPLEMENT]` bullet to Business
Workflows describing that type's flow, and record the answer as a `CLR-###` (Stage `intake`). If the
user says there is no difference, keep the single flow and ask no follow-up.

**Images not extracted**: for each Source Coverage image row that is `NOT EXTRACTED`, ask the user to
paste the image or describe it. If they supply content, write it as `DEX-###` rows (same `Source` as
Step 2E) and change the row to `EXTRACTED` with the new ids. If they `[SKIP]`, the row stays
`NOT EXTRACTED`.

## Step 3B — Record clarifications (required)

Human interview answers are recorded verbatim in a dedicated **input** artifact `clarifications.md`,
never embedded as raw prose in derived artifacts. Create it once, early, from the template
`clarifications-template.md` (included alongside this skill), then append a row per answer.

- **Create the log**: on the first turn, write the minimal `clarifications.md` (the three sections
  `Source`, `Clarification Log`, `Summary` with the empty log table) via
  `persist_artifact` — `stageId: "intake"`, `artifact: "clarifications.md"`, `content: <filled template>`.
  A zero-row log is valid; do NOT invent entries.
- **Resolve `Recorded By`**: run `git config user.name` (fall back to `git config user.email`, then
  the literal `user`). Use this value for every row you write this session.
- **Append a row per answer** in the `## Clarification Log` table with columns, in order:
  `ID` (`CLR-001`, `CLR-002`, … unique, increasing) · `Stage` = `intake` · `Recorded At` = current
  ISO-8601 UTC timestamp (e.g. `2026-09-27T14:32:10Z`) · `Recorded By` (resolved above) · `Target`
  (`REQ-xxx / <TestType>`, `REQ-xxx`, or `general`) · `Resolution` (`Y`/`N`/`SKIP`/`ANSWERED`) ·
  `Question` and `Answer` **verbatim** (encode newlines as `<br>` and any literal pipe as `\|`) ·
  `Source` (e.g. `user interview (vscode_askQuestions)`).
- Re-write `clarifications.md` with `persist_artifact` (`artifact: "clarifications.md"`, `force: true`)
  after each round. Keep the `## Summary` counts consistent.
- The engine validates `clarifications.md` against contract `clarifications@1` at Gate 1 and Gate 2;
  a malformed log fails those gates.

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

Source Coverage results:

- **FAIL `table_id_coverage`** — the named `DEX-###` rows are not cited by any `EXTRACTED` Source
  Coverage row (or the Source Coverage table is missing). Add the id to the row for the section or
  image it came from. `DEX IDs` must be comma-separated (`DEX-001, DEX-002`); ids separated by
  semicolons or spaces are not recognized and show up here as "not cited".
- **FAIL `table_refs_resolve`** — an `EXTRACTED` row cites a `DEX-###` id that is not in Design
  Extracts, or its `DEX IDs` cell holds text with no `DEX-###` id at all. Fix the id or add the
  missing extract row.
- **CONDITIONAL_PASS from `matrix_completeness`** — one or more Source Coverage rows are
  `NOT EXTRACTED`. Show the user the listed items. Either recover the content (Step 3) or record
  sign-off with `approve_gate`.

## Rules

- Never overwrite another unit's `intake.md`; always operate on the active unit and use `force: true`
  to update the intake you are building.
- Every section contains captured information or `[GAP]` — never blank.
- Do not advance to `s2t-analyze` until Gate 0 is PASS, or CONDITIONAL_PASS with a recorded approval.

