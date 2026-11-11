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

> _Owner: Back-End Developer — branch `feature/srs-accounts`. Replace this line with the section content._

### 4.3 Transaction Processing Logic

These requirements refine **UR-3**, **UR-4** and **UR-5**. All monetary operations are atomic: they either complete fully or leave the database unchanged.

**SR-3.1 [Atomic Withdrawal Processing Specification]:**

| Item | Specification |
|---|---|
| Preconditions | Customer authenticated; Account status = `ACTIVE`; session token valid |
| Input | Account ID, Withdrawal Amount `A` (`A > USD 0.00`) |
| Processing action | The system checks that `(Balance + OverdraftLimit) >= A`, where OverdraftLimit = `L_max` for a Current Account (SR-2.1) and `-B_min` for a Savings Account (SR-2.3). If true, it deducts `A` from Balance and appends a transaction record to the immutable ledger, both inside one database transaction. |
| Output | Updated balance and an acknowledgment to the customer |
| Postconditions | `Balance_new = Balance_old - A`; one ledger record exists for the withdrawal |
| Exception handling | If the balance is insufficient, the transaction is aborted, the database state rolls back, and the system returns `ERR_INSUFFICIENT_FUNDS`. If the ledger record cannot be written, the deduction is rolled back as well. |

**SR-3.2 [Atomic Deposit Processing Specification]:**

| Item | Specification |
|---|---|
| Preconditions | Customer authenticated; Account status = `ACTIVE`; session token valid |
| Input | Account ID, Deposit Amount `A` (`A > USD 0.00`) |
| Processing action | The system adds `A` to Balance and appends a transaction record to the ledger, both inside one database transaction. |
| Output | Updated balance and an acknowledgment to the customer |
| Postconditions | `Balance_new = Balance_old + A`; one ledger record exists for the deposit |
| Exception handling | Invalid amount: rejected with `ERR_INVALID_AMOUNT`, nothing changes. If the ledger record cannot be written, the credit is rolled back. |

**SR-3.3 [Balance Inquiry]:** The system shall return the current balance, account number and account type of an account only to the authenticated customer who owns it. A balance inquiry shall not change any data.

**SR-3.4 [Immutable Ledger and Audit Trail]:** Every deposit and withdrawal shall append one ledger record containing the transaction ID, Account ID, User ID, type, amount, resulting balance and timestamp. Ledger records shall never be updated or deleted.

#### Data integrity rules

| Rule | Enforcement |
|---|---|
| A balance never goes below its permitted floor | Checked inside the same transaction that changes the balance |
| No balance change without a ledger record | Balance update and ledger insert commit together or not at all |
| Concurrent withdrawals cannot overdraw an account | The account row is locked for the duration of the transaction |
| Ledger records are permanent | No update or delete permission on the ledger table |

#### Error codes

| Code | Raised when |
|---|---|
| `ERR_INSUFFICIENT_FUNDS` | A withdrawal would take the balance below its permitted floor |
| `ERR_INVALID_AMOUNT` | The amount is zero, negative or not a valid number |
| `ERR_ACCOUNT_INACTIVE` | The account status is not `ACTIVE` |

## 5. Non-Functional Requirements & Metrics

> _Owner: ScrumMaster — branch `feature/srs-nfr`. Replace this line with the section content._

## 6. Graphical Models & Tabular Use Case Specification

> _Owner: System Architect — branch `feature/uml-usecase`. Replace this line with the section content._

## 7. Requirements Traceability & Change Control

> _Owner: Product Owner — branch `feature/srs-rtm`. Replace this line with the section content._
