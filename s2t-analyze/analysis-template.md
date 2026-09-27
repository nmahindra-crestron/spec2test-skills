# Requirement Analysis

Date: YYYY-MM-DD

## Evidence Register

Every REQ, BR, and VR must trace to documented evidence. Unsupported statements go to the
Assumptions Register — never to REQ/BR/VR.

### Requirement Evidence

| ID | Source Type | Source Location |
|----|-------------|-----------------|
| REQ-001 | Story | Acceptance Criteria 1 |

### Business Rule Evidence

| ID | Source Type | Source Location |
|----|-------------|-----------------|
| BR-001 | Design Document | Section 4.2 |

### Validation Rule Evidence

| ID | Source Type | Source Location |
|----|-------------|-----------------|
| VR-001 | Design Document | Section 5.3 |

## Assumptions Register

Assumptions are recorded separately and NEVER promoted to REQ/BR/VR.

| ID | Description | Source | Status |
|----|-------------|--------|--------|
| ASM-001 | Example assumption | None | UNCONFIDENT |

## Source

Story:
Change:
Slug:

## Intake Completeness

Source sections found (✅ / [GAP]); total GAP references carried from intake.

## Atomic Requirements

| ID | Workflow | Category | Priority | Source | Test Type Hint | Requirement |
|----|----------|----------|----------|--------|----------------|-------------|
| REQ-001 | | Functional | High | AC-1 | Functional | |

## Workflow Analysis

| Workflow ID | Workflow Name | Feature Area | Requirements |
|-------------|---------------|--------------|--------------|
| WF-001 | | | REQ-001 |

## Enumerated Value Inventory

Finite, named value sets that must not be partially covered. Coverage Requirement ∈ FULL /
REPRESENTATIVE / NEGATIVE. Every inventory must be evidence-backed or marked [GAP].

| Inventory ID | Category | Semantic Family | Values | Coverage Requirement |
|--------------|----------|-----------------|--------|----------------------|
| INV-001 | | | | FULL |

## Behavioral Coverage Inventory

Finite behavioral patterns requiring explicit validation. Coverage Requirement ∈ FULL /
REPRESENTATIVE / NEGATIVE.

| Behavior ID | Category | Behavior | Coverage Requirement |
|-------------|----------|----------|----------------------|
| BHV-001 | | | FULL |

## Coverage Hardening Signals

Platform parity, scope limits, lifecycle coupling, prefix/suffix ambiguity, etc. Missing evidence
creates a Requirement Gap + Clarification Question, never a Requirement.

- Signal — evidence / [GAP]

## Business Rules

| ID | Rule | Source |
|----|------|--------|
| BR-001 | | AC-2 |

## Validation Rules

| ID | Rule | Suggested Coverage |
|----|------|--------------------|
| VR-001 | | |

## Requirement Gaps

Severity HIGH / MEDIUM / LOW; Status OPEN / RESOLVED / SKIP.

| ID | Severity | Status | Gap | Impact |
|----|----------|--------|-----|--------|
| GAP-001 | HIGH | OPEN | | |

## Conflict Analysis

Status OPEN / SKIP.

| ID | Status | Conflict | Sources |
|----|--------|----------|---------|
| CONFLICT-001 | OPEN | | |

## Ambiguities

State UNRESOLVED / RESOLVED / SKIP.

| ID | State | Statement | Concern |
|----|-------|-----------|---------|
| AMB-001 | UNRESOLVED | | |

## Clarification Questions

| ID | Related Item | Status | Question | Response |
|----|--------------|--------|----------|----------|
| CQ-001 | GAP-001 | OPEN | | |

## Feature Interaction Analysis

Evidence-based interactions between this change's functional behavior and existing project
functionality (derived from the `docs/s2t-memory/` project memory tree). Record each interaction as
**one row** in the single combined table below — carrying both its classification/traceability
columns and its scenario-outline columns. Do NOT split into separate register/outline tables, and do
NOT author full test cases here (the generate stage expands each `INT-###` row into exactly one
interaction test case).

Interaction coverage is **advisory**: a zero-row table is valid (e.g. no project memory, or no
interactions found). Missing/unbacked interactions never FAIL the gate — at most CONDITIONAL_PASS.

**Interaction Type** MUST be exactly one of these twelve values (fixed taxonomy):

- `Direct` — change directly invokes/modifies an existing feature
- `Workflow` — change participates in a multi-step flow spanning existing features
- `Lifecycle` — change affects create/update/delete/state transitions of existing entities
- `Data` — change reads/writes data shared with existing features
- `Dependency` — change requires an existing feature as a prerequisite (or vice-versa)
- `Configuration/Feature-Flag` — behavior varies by existing settings/flags/entitlements
- `Concurrency/Contention` — simultaneous operations on shared resources (locks, races)
- `Permission/Authorization` — change intersects existing access-control/role rules
- `Integration/External` — change touches existing external API/system contracts
- `Event/Notification` — change emits/consumes events existing features subscribe to
- `UI/Navigation` — shared screens/navigation/component state
- `Regression/Backward-Compatibility` — change risks altering established existing behavior

**Memory Source** cites the specific project-memory fact backing the row (e.g.
`auth-tokens (source: PROJ-123)`). When project memory is absent and the user names the existing
feature during the interview, cite the backing clarification instead (e.g. `clarification CLR-003`).

<!-- No-memory fallback: if docs/s2t-memory/ is absent/empty, add an advisory note here (e.g.
"No project memory found — run s2t-hydrate to enable memory-backed feature-interaction analysis;
interactions below are user-sourced only."), raise ONE mandatory clarification (run s2t-hydrate vs.
manually name existing features), never fabricate features, and cite `clarification CLR-###` in
Memory Source for any user-named interaction. A zero-row table is valid — coverage is advisory. -->

| Interaction ID | Existing Feature | Memory Source | New Feature / REQ | Interaction Type | Risk | Impact | Objective | Pre-conditions | Interaction Steps | Expected Result |
|----------------|------------------|---------------|-------------------|------------------|------|--------|-----------|----------------|-------------------|-----------------|
| INT-001 | | | | Direct | Medium | | | | | |

## Risk Assessment

### High Risk

### Medium Risk

### Low Risk

### Risk Rationale

## Testability Assessment

Score:

### Testability Concerns

### Recommendation

## Feature Area Classification

| Requirement | Feature Area |
|-------------|--------------|
| REQ-001 | |

## Coverage Intent Matrix

All eight test-type categories below are **mandatory for every requirement — always `Y`, never
`N`, no skipping**. Every requirement must receive coverage for Functional, Negative, Edge,
Boundary, Performance, Stress, Exploratory, and Interaction. If it is unclear how a category
applies to a requirement (e.g. no documented performance target, no defined stress volume), do not
guess or silently mark it thin — **ask the user** (see Step 3 of the skill) until the coverage
approach is clear, then record it in the corresponding Non Functional Consideration or Coverage
Hardening Signal. Never mark a column `N` to skip generating that test type.

| Requirement | Functional | Negative | Edge | Boundary | Performance | Stress | Exploratory | Interaction |
|-------------|------------|----------|------|----------|-------------|--------|-------------|-------------|
| REQ-001 | Y | Y | Y | Y | Y | Y | Y | Y |

## Requirement Traceability Matrix

| Requirement | Business Rule | Validation Rule | Coverage Type | Priority |
|-------------|---------------|-----------------|---------------|----------|
| REQ-001 | BR-001 | VR-001 | Functional | High |

## Hidden Test Opportunities

Advisory only. MANDATORY / OPTIONAL / EXPLORATORY. Never promoted to test cases, never in the
Traceability Matrix, and never contribute to Gate scoring unless traceable to REQ/BR/VR/approved
source.

## Non Functional Considerations

### Security

### Performance

### Accessibility

### Reliability

### Auditability

## Out Of Scope

- Item

## Existing Test Coverage Assessment

| Requirement | Existing Test Case | Status | Coverage Gap | Recommendation |
|-------------|--------------------|--------|--------------|----------------|
| REQ-001 | | UNCOVERED | | Generate New Test |

## Gate Result

Status: PASS | CONDITIONAL_PASS | FAIL

Quality Score: 0-100 (90-100 excellent / 75-89 good / 50-74 moderate / 0-49 poor)

### Blocking Issues

- None

### Non Blocking Issues

- None

### Recommendation

- Proceed with testcase generation | Request clarification | Do not proceed

## Analysis Summary

Total Requirements:
Total Business Rules:
Total Validation Rules:
Total Workflows:
Total Feature Interactions:  <!-- count of INT-### rows in the Feature Interaction Analysis table -->

Assumptions — CONFIDENT / CERTAIN / UNCONFIDENT:
Requirement Gaps — HIGH / MEDIUM / LOW / SKIP:
Conflicts — OPEN / RESOLVED / SKIP:
Ambiguities — RESOLVED / UNRESOLVED / SKIP:

Overall Readiness: READY FOR SCENARIO GENERATION | READY WITH CLARIFICATIONS | NOT READY
