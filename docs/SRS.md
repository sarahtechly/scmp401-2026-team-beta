# Software Requirements Specification (SRS)

**Project:** Secure Web-Based Banking System &nbsp;|&nbsp; **Course:** SCMP 401 Software Engineering Studio

| | |
|---|---|
| **Team Name** | Team Beta |
| **Date & Version** | 2026-11-11 · Version 1.0 (Milestone 1 Submission) |
| **Document Authors** | Sara (Product Owner), Larin (System Architect) |
| **Course & Instructor** | SCMP 401 — Software Engineering Studio · Dr. Bestoon Hussien |
| **Document Standard** | Adapted from IEEE 830-1998 & Ian Sommerville, *Software Engineering*, Chapter 4 |

## Document Preface & Revision History

This Software Requirements Specification (SRS) outlines the functional, non-functional and system requirements for the Secure Web-Based Banking System developed in SCMP 401. It is the primary technical agreement between project stakeholders, system architects, software developers and quality assurance engineers.

| Version | Date | Description of Changes | Author(s) | Approved By |
|---|---|---|---|---|
| v0.1 | 2026-11-11 | Document structure and section ownership | Sara (Product Owner) | — |

## 1. Introduction & System Vision

> _Owner: Product Owner — branch `feature/srs-introduction`. Replace this line with the section content._

## 2. Stakeholders & Reader Perspectives

> _Owner: Product Owner — branch `feature/srs-introduction`. Replace this line with the section content._

## 3. User Requirements Definition

> _Owner: UI/UX Lead — branch `feature/srs-user-reqs`. Replace this line with the section content._

## 4. Detailed System Requirements Specification

System requirements expand the user requirements into explicit technical statements that give exact inputs, outputs, preconditions, postconditions and exception handling.

### 4.1 Authentication & Access Control

> _Owner: Back-End & Security Lead — branch `feature/srs-authentication`. Replace this line with the section content._

### 4.2 Account Management Logic

> _Owner: Back-End Developer — These requirements refine **UR-2** and supply the account rules used by **UR-5**.

**SR-2.1 [Current Account Overdraft]:** The system shall permit negative balances on Current Accounts up to a pre-configured overdraft limit `L_max = USD 500.00`. A withdrawal of amount `A` is permitted only if:

```
Balance - A >= -L_max
```

**SR-2.2 [Savings Account Interest]:** The system shall calculate the monthly interest on each Savings Account on the last calendar day of each month using:

```
I = B * (r / 12)
```

where `B` is the minimum daily balance during that month and `r = 2.5%` (0.025) is the annual interest rate. The result shall be rounded to the nearest cent, credited to the account once per month and recorded in the ledger.

**SR-2.3 [Savings Balance Floor]:** The system shall reject a withdrawal from a Savings Account if the resulting balance would fall below the minimum required balance `B_min = USD 100.00`. A withdrawal of amount `A` is permitted only if:

```
Balance - A >= B_min
```

**SR-2.4 [Account Creation]:** When a customer opens an account, the system shall create it with a unique account number, the selected type (Current or Savings), status `ACTIVE` and an opening balance of USD 0.00. The system shall reject the request if any required field is missing or the account type is not one of the two supported types.

#### Account type rules

| Rule | Current Account | Savings Account |
|---|---|---|
| Lowest permitted balance (floor) | `-L_max` = USD -500.00 | `B_min` = USD 100.00 |
| Interest | None | `I = B * (r / 12)`, `r = 2.5%` per year |
| Interest posting date | — | Last calendar day of each month |

#### Worked examples

| Case | Calculation | Result |
|---|---|---|
| Current Account, balance USD 200.00, withdraw USD 600.00 | 200 − 600 = −400 ≥ −500 | Permitted; new balance USD −400.00 |
| Current Account, balance USD 200.00, withdraw USD 750.00 | 200 − 750 = −550 < −500 | Rejected: `ERR_INSUFFICIENT_FUNDS` |
| Savings Account, balance USD 350.00, withdraw USD 300.00 | 350 − 300 = 50 < 100 | Rejected: below minimum balance |
| Savings Account, minimum daily balance USD 1,200.00 | I = 1200 × (0.025 / 12) | Interest USD 2.50 for the month |


### 4.3 Transaction Processing Logic

> _Owner: Database Lead — branch `feature/srs-transactions`. Replace this line with the section content._

## 5. Non-Functional Requirements & Metrics

> _Owner: ScrumMaster — branch `feature/srs-nfr`. Replace this line with the section content._

## 6. Graphical Models & Tabular Use Case Specification

> _Owner: System Architect — branch `feature/uml-usecase`. Replace this line with the section content._

## 7. Requirements Traceability & Change Control

> _Owner: Product Owner — branch `feature/srs-rtm`. Replace this line with the section content._
