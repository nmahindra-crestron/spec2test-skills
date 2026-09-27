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

Evidence-based interactions between this change and existing features.

| Interaction ID | Existing Feature | New Feature | Interaction Type | Impact |
|----------------|------------------|-------------|------------------|--------|
| INT-001 | | | Direct | |

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
Total Feature Interactions:

Assumptions — CONFIDENT / CERTAIN / UNCONFIDENT:
Requirement Gaps — HIGH / MEDIUM / LOW / SKIP:
Conflicts — OPEN / RESOLVED / SKIP:
Ambiguities — RESOLVED / UNRESOLVED / SKIP:

Overall Readiness: READY FOR SCENARIO GENERATION | READY WITH CLARIFICATIONS | NOT READY
