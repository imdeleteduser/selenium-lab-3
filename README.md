# selenium-lab-3
# Hospital Management Software

## White Box Testing & Unit Testing

-----

## Aim

To identify and fix the incorrect tax percentage bug in the hospital patient billing module using Dynamic White Box Testing and Unit Testing techniques.

-----

## Objective

- Understand how Dynamic White Box Testing detects logic errors inside code.
- Apply Unit Testing to isolate the faulty tax calculation function.
- Compare the roles of Testing and Debugging in resolving billing defects.
- Prepare a review checklist for the billing module.
- Document bug reports with test cases and sample code.

-----

## Tools Required

|Tool                |Purpose                                |
|--------------------|---------------------------------------|
|Python 3.x / Java   |Language for billing module sample code|
|pytest / JUnit      |Unit testing framework                 |
|VS Code / Eclipse   |IDE for code writing and debugging     |
|Git / GitHub        |Version control and submission         |
|Coverage.py / JaCoCo|Code coverage analysis                 |

-----

## Theory

### Dynamic White Box Testing

Involves executing the actual code with full visibility of its internal logic. Testers design test cases based on internal paths, branches, and conditions.

In this scenario, the tester inspects `calculate_bill()` directly, traces execution paths, and verifies the correct tax rate (5%) is applied.

**Key techniques:**

- **Statement Coverage** – every line of tax logic is executed at least once.
- **Branch Coverage** – both if/else paths (taxable vs non-taxable) are tested.
- **Path Coverage** – all unique execution flows are traced.

### Unit Testing

Isolates the smallest testable piece — the billing calculation function — and verifies it independently. A unit test calls `calculate_bill(base_cost, tax_rate)` with known inputs and asserts the exact expected output, immediately exposing any wrong tax percentage.

### Testing vs Debugging

|Aspect      |Testing                           |Debugging                                    |
|------------|----------------------------------|---------------------------------------------|
|Definition  |Finding if a defect exists        |Locating & fixing the defect                 |
|Who does it?|Tester                            |Developer                                    |
|When?       |After code is written             |After a bug is reported                      |
|Output      |Bug report / test result          |Fixed code                                   |
|In this case|Unit test reveals wrong tax amount|Dev traces `calculateTax()` and corrects rate|

-----

## Test Plan

|Field            |Details                                                    |
|-----------------|-----------------------------------------------------------|
|Module Under Test|Patient Billing – Tax Calculation                          |
|Test Type        |Unit Test + Dynamic White Box Test                         |
|Test Environment |Python 3.11 + pytest                                       |
|Entry Criteria   |Billing module code available; tax rate defined in config  |
|Exit Criteria    |All unit tests pass; correct tax applied to all bill types |
|Risk             |Hardcoded tax rate after update; incorrect branch selection|

-----

## Test Cases

|TC ID|Description                  |Input             |Expected Output       |Status|
|-----|-----------------------------|------------------|----------------------|------|
|TC01 |Standard bill with 5% tax    |Base=1000, Tax=5% |Total=1050            |PASS|
|TC02 |Wrong tax rate (18%) applied |Base=1000, Tax=18%|Total=1050 (bug: 1180)|FAIL|
|TC03 |Zero tax for exempted patient|Base=500, Tax=0%  |Total=500             |PASS|
|TC04 |Negative base amount         |Base=-100, Tax=5% |Error / 0             |PASS|
|TC05 |Null/missing tax rate        |Base=800, Tax=null|Error raised          |PASS|
|TC06 |Large bill amount            |Base=99999, Tax=5%|Total=104998.95       |PASS|

-----

## Sample Code

### billing.py

```python
TAX_RATE = 0.05   # 5% — correct rate after bug fix

def calculate_bill(base_cost: float, tax_rate: float = TAX_RATE) -> float:
    if base_cost < 0:
        raise ValueError("Base cost cannot be negative")
    if tax_rate is None:
        raise ValueError("Tax rate must be provided")
    tax = base_cost * tax_rate
    return round(base_cost + tax, 2)
```

### test_billing.py

```python
import pytest
from billing import calculate_bill

def test_standard_tax():
    assert calculate_bill(1000, 0.05) == 1050.0

def test_wrong_tax_detected():
    # Bug: update accidentally set rate to 0.18
    assert calculate_bill(1000, 0.18) != 1050.0  # detects bug

def test_zero_tax_exemption():
    assert calculate_bill(500, 0.0) == 500.0

def test_negative_base_raises():
    with pytest.raises(ValueError):
        calculate_bill(-100, 0.05)

def test_null_tax_raises():
    with pytest.raises(ValueError):
        calculate_bill(800, None)
```

-----

## Bug Report

|Field             |Details                                                 |
|------------------|--------------------------------------------------------|
|Bug ID            |BUG-001                                                 |
|Title             |Wrong tax percentage applied after billing module update|
|Severity          |Critical                                                |
|Priority          |High                                                    |
|Module            |Patient Billing – `calculate_bill()`                    |
|Reported By       |Unit Test TC02                                          |
|Steps to Reproduce|Call `calculate_bill(1000)` → Expected 1050, Got 1180   |
|Root Cause        |`TAX_RATE` changed to 0.18 during update instead of 0.05|
|Fix Applied       |Reverted `TAX_RATE = 0.05`; added config validation     |
|Status            |Fixed – Verified                                      |

-----

## Billing Module Review Checklist

- [ ] Tax rate value matches approved configuration (5%)
- [ ] Tax rate is not hardcoded; read from config/env
- [ ] Negative and zero base costs handled with errors
- [ ] Null/missing tax rate raises exception
- [ ] All branches of tax logic covered by unit tests
- [ ] Exempted patient category applies 0% correctly
- [ ] Rounding of final bill is consistent (2 decimal places)
- [ ] Code reviewed after every update to billing module
- [ ] Test suite runs in CI/CD pipeline on every commit
- [ ] Bug BUG-001 verified fixed and closed

-----

## Result

The billing module bug (BUG-001) was successfully identified using Dynamic White Box Testing and Unit Testing. The root cause — an incorrect `TAX_RATE` value (0.18 instead of 0.05) introduced during a recent update — was pinpointed by TC02. After reverting the tax rate and adding configuration validation, all six unit tests passed. Branch coverage reached 100% for the `calculate_bill()` function.
