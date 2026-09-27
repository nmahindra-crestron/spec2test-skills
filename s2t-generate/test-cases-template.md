# Test Cases

Date: YYYY-MM-DD

## Source

Story:
Change:
Slug:
Analysis: .spec2test/changes/YYYY-MM-DD-<slug>/analysis.md

## Generation Summary

Evidence Validation: PASS | FAIL
Evidence Traceability %: 100%
Mode: FULL | DELTA | UPDATE

Total Requirements Covered:
Total Business Rules Covered:
Total Validation Rules Covered:
Total Test Cases Generated:

| Type | Count |
|------|-------|
| Functional | |
| Negative | |
| Edge | |
| Boundary | |
| Performance | |
| Stress | |
| Exploratory | |
| Interaction | |

## Generation Warnings

None

## Caveats

None

## Common Setup Library

### SETUP-001

Launch Application
Create Project
Configure Environment
Verify environment is ready

## Scenarios

### SCN-001

Workflow:
Feature Area:
Priority: High | Medium | Low
Purpose:
Covered Requirements:
Covered Business Rules:
Covered Validation Rules:

## Test Cases

### TC-001

Name: <plain title — do NOT use bold; the export parser needs `Name:` at line start>

User Story:
Feature Area:
Repository Path:
Scenario:
Requirement(s):
Business Rule(s):
Validation Rule(s):
Coverage Intent: REQ | BR | VR | REGRESSION | RISK | EXPLORATORY | INTERACTION
Priority:
Risk:
Test Types: <comma-separated list of one or more of: Functional, Negative, Edge, Boundary, Performance, Stress, Exploratory, Interaction>
Interaction Type: <required ONLY when Test Types includes Interaction — exactly one of the twelve values from analysis.md; omit otherwise>
Interaction Ref: <required ONLY when Test Types includes Interaction — the single INT-### row this expands (strict 1:1); omit otherwise>
Existing Feature(s): <required ONLY when Test Types includes Interaction — the existing feature(s) targeted, carried from analysis.md; omit otherwise>
Tags:
Setup Details: SETUP-001
Purpose:

### Pre-requisite

- Item

### Test Data

| Parameter | Value |
|-----------|-------|

### Steps

Minimum 6 data rows (aim for 6-7), maximum 15 data rows — the header and separator rows do not
count. Every `Expected Result` cell must be non-empty and observable. Gate 2 enforces these bounds.

| # | Action | Expected Result |
|---|--------|-----------------|

### Post Conditions

- Item

### Notes

- [ASSUMPTION: ASS-xxx] (only if traceable per analysis rules)

## Existing Test Case Recommendations

| Existing Test Case | Action | Reason |
|--------------------|--------|--------|

## Traceability Matrix

| Test Case | Coverage Intent | Requirement | Business Rule | Validation Rule |
|-----------|-----------------|-------------|---------------|-----------------|
| TC-001 | REQ | REQ-001 | BR-001 | VR-001 |

## Coverage Validation

Evidence Traceability %:
Requirement Coverage %:
Business Rule Coverage %:
Validation Rule Coverage %:
Workflow Coverage %:
Scenario Coverage %:
Feature Area Coverage %:
Coverage Intent Compliance %:
Risk Coverage %:

Uncovered Requirements: None
Uncovered Business Rules: None
Uncovered Validation Rules: None

## Gate 2 Result

Status: PASS | CONDITIONAL_PASS | FAIL

### Missing Coverage

None

### Coverage Gaps

None

### Recommendation

Proceed To Export | Review Coverage Gaps
