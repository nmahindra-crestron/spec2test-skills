# Gap checklist for the quality review

Use this list with the logic standard. Check each test case against every row. When a row fails,
write one finding and use the Rule ID shown here.

| Rule | Check | Look in | Default severity |
|---|---|---|---|
| GAP-02 | Each edge case in the Edge Case Decisions table has a test step that tries it and checks the decided result. No decision is `UNKNOWN`. | `analysis.md` Edge Case Decisions, the test steps | MAJOR |
| GAP-03 | Each behavior in State Dependencies is tested from every starting state listed for it, not only one. | `analysis.md` State Dependencies, the test steps | MAJOR |
| GAP-04 | Each "same as the old product" requirement has a test that follows the Comparison Method: the same start state, the same event order, and a check after each event. | `analysis.md` Comparison Method, the test steps | MAJOR |
| GAP-05 | Each performance claim uses the number, unit, data size and machine from Performance Targets. Words like "fast" or "quickly" are not a target. | `analysis.md` Performance Targets, the Expected Result column | MAJOR |
| GAP-07 | Each expected result allows only one outcome. Words like "or", "may", "should", "either" or "if possible" that allow two outcomes are a finding. | the Expected Result column | MAJOR |
| GAP-09 | The setup does not change the thing under test, and does not leave another item half done. | Setup Details, Setup Steps, Pre-requisite | MAJOR |
| GAP-10 | Each Data Preservation row has a test that compares the data item by item, and checks it again after reopening when the row says so. | `analysis.md` Data Preservation, the test steps | MAJOR |
| GAP-11 | Where an edge case and a starting state meet (for example, a limit reached while a setting is on), at least one test covers that mix. | Edge Case Decisions, State Dependencies, Feature Interaction Analysis | MINOR |
| GAP-12 | Each value in `Test Types:` has at least one step that really tests that type. For example, `Performance` needs a timed step, and `Boundary` needs a step at the limit. | `Test Types:` and the test steps | MAJOR |
| GAP-13 | A test case claims at most 3 requirements and at most 3 test types. Exploratory-only tests have no cap. | `Requirement(s):` and `Test Types:` | MINOR |
| GAP-14 | Every requirement the Traceability Matrix lists for a test case is really checked by a step in that test case. | Traceability Matrix, the test steps | MAJOR |

## Severity guide

- **BLOCKER**: the test would pass when the product is wrong, or it cannot be run at all.
- **MAJOR**: the test misses a decided behavior, or its result can be read two ways.
- **MINOR**: the test works, but it is weaker or harder to follow than it should be.

Raise the severity by one level when the same problem affects more than half of the test cases.
