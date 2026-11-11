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
| v1.0 | 2026-11-11 | Complete Milestone 1 SRS: requirements, NFR metrics, use case model and RTM | Team Beta | Dr. Bestoon Hussien (pending) |

## 1. Introduction & System Vision

### 1.1 Purpose of the System

This document defines the functional, non-functional and detailed system requirements for the Secure Web-Based Banking System. The system gives authenticated bank customers secure online access to manage their accounts, check balances, and make deposits and withdrawals that are processed atomically. It is the foundation for the SCMP 401 studio project and the reference for all later design, implementation and testing work.

### 1.2 Product Vision

A web-based bank that a customer can trust: sign-in is protected by multi-factor authentication, every movement of money is atomic and recorded in an audit trail, and the two account types behave like real banking products.

| Account type | Purpose | Distinguishing rule |
|---|---|---|
| Current Account | Everyday transactions | May go negative up to an overdraft limit of USD 500.00 |
| Savings Account | Holding money and earning interest | Earns 2.5% interest per year, calculated monthly; balance may not fall below USD 100.00 |

### 1.3 Scope & System Boundary

**In scope**

- Customer identity verification: sign-in with user ID and password plus a second factor (UR-1).
- Account creation for Current Accounts and Savings Accounts (UR-2).
- Balance inquiry (UR-3).
- Deposits (UR-4) and withdrawals (UR-5), processed as atomic transactions and recorded in an immutable ledger.

**Out of scope**

- Physical ATM hardware and its maintenance.
- Manual vault and cash management.
- Credit underwriting, loans and credit cards.
- Transfers to other banks and connections to real payment networks.
- Foreign currency accounts.

**External systems (outside the boundary)**

- Auth Server: verifies credentials and second-factor codes.
- Core Banking Database: stores customers, accounts, the transaction ledger and audit logs.

### 1.4 Definitions, Acronyms and Abbreviations

| Term | Meaning |
|---|---|
| SRS | Software Requirements Specification: formal document specifying software capabilities |
| UR / SR / UC | User Requirement / System Requirement / Use Case identifiers |
| NFR | Non-Functional Requirement: a whole-system quality attribute or operational constraint |
| MFA | Multi-Factor Authentication: verification that needs two or more authentication factors |
| Current Account | A transactional account that permits frequent withdrawals and an overdraft |
| Savings Account | An interest-bearing account with a minimum balance |
| L_max | Overdraft limit of a Current Account (USD 500.00) |
| B_min | Minimum balance of a Savings Account (USD 100.00) |
| Ledger | Append-only record of monetary transactions; entries are never edited or deleted |
| Atomic transaction | An operation that either completes fully or leaves the data unchanged |
| RTM | Requirements Traceability Matrix (Section 7) |
| TDD | Test-Driven Development: unit tests are written before the code |

### 1.5 References

- IEEE Std 830-1998, *IEEE Recommended Practice for Software Requirements Specifications*.
- I. Sommerville, *Software Engineering*, Chapters 4, 22 and 25.
- OMG Unified Modeling Language (UML), use case notation.
- SCMP 401 Milestone 1 Assignment Brief.

## 2. Stakeholders & Reader Perspectives

Different stakeholders read this SRS for different purposes across the software development life cycle.

| Reader category | Primary stakeholders | Purpose and focus |
|---|---|---|
| Client managers | Course instructor (Dr. Bestoon Hussien), evaluators | Verify that the system meets the educational and business objectives |
| System end-users | Bank customers | Read the user requirements (Section 3) to confirm the services the system provides |
| Software developers | Team Beta | Use the system requirements (Section 4) as the blueprint for implementation |
| Test engineers | Team Beta, automated JUnit suite | Design unit, integration and system tests from Sections 4, 5 and 7 |

## 3. User Requirements Definition


User requirements describe, in plain language, the services the system provides to its users and the constraints it works under. Mandatory services use **shall**; optional enhancements use **should**.

**UR-1 [Security & Authentication]:** The Banking Application shall authenticate customers using multi-factor credentials before granting access to any account service.

- *Rationale:* Protects customers' financial assets and supports compliance with data privacy legislation.

**UR-2 [Account Creation]:** The system shall enable customers to open both Current Accounts and Savings Accounts.

- *Rationale:* Offers products suited to different financial needs: everyday spending with an overdraft, or saving with interest.

**UR-3 [Balance Inquiry]:** Authenticated customers shall be able to view the current balance, account number and account type of their own accounts at any time.

- *Rationale:* Lets customers plan their spending and avoid unintended overdrafts.

**UR-4 [Deposit]:** Customers shall be able to deposit funds into their own account and see the updated balance immediately.

- *Rationale:* Depositing is a core banking service and the basis of every later transaction.

**UR-5 [Withdrawal]:** Customers shall be able to withdraw funds from their own account, subject to the overdraft limit of a Current Account and the minimum balance of a Savings Account.

- *Rationale:* Gives customers access to their money while protecting the bank from over-withdrawal.

### 3.1 Summary

| ID | Service | Who uses it | Detailed in |
|---|---|---|---|
| UR-1 | Secure sign-in with MFA | Customer | Section 4.1 |
| UR-2 | Open a Current or Savings Account | Customer | Section 4.2 |
| UR-3 | View balance | Authenticated customer | Section 4.3 |
| UR-4 | Deposit funds | Authenticated customer | Section 4.3 |
| UR-5 | Withdraw funds | Authenticated customer | Sections 4.2 and 4.3 |


## 4. Detailed System Requirements Specification

System requirements expand the user requirements into explicit technical statements that give exact inputs, outputs, preconditions, postconditions and exception handling.

### 4.1 Authentication & Access Control
These requirements refine **UR-1**.

**SR-1.1 [Login and Password Hashing]:** Upon user login, the system shall prompt for User ID and Password. Passwords shall be hashed using BCrypt (cost factor 12) before storage and before verification against the database. Plaintext passwords shall never be stored or written to logs.

**SR-1.2 [Account Lockout]:** If three (3) consecutive incorrect password attempts are detected within 5 minutes, the system shall lock the account for 15 minutes and write a security audit log entry.

**SR-1.3 [Multi-Factor Authentication]:** After a correct password, the system shall require a second factor: a 6-digit time-based one-time code that is valid for 30 seconds. Access shall be granted only when both factors are valid.

**SR-1.4 [Session Management]:** After successful authentication the system shall issue a session token that expires after 30 minutes. Every request to an account service shall be rejected with an authentication error if the token is missing, invalid or expired.

#### Login processing specification

| Item | Specification |
|---|---|
| Preconditions | Customer has a registered account; account is not locked |
| Input | User ID, Password, 6-digit MFA code |
| Processing | 1. Look up the User ID. 2. Compare the BCrypt hash of the password with the stored hash. 3. Verify the MFA code. 4. Issue a session token. |
| Output | Session token; redirect to the account dashboard |
| Postconditions | A valid 30-minute session exists; a "signed in" audit entry is recorded |
| Exception handling | Wrong password: failure counter increases; on the third failure within 5 minutes the account is locked for 15 minutes (`ERR_ACCOUNT_LOCKED`). Wrong or expired MFA code: no session is issued (`ERR_INVALID_MFA`). Unknown User ID: the same generic error as a wrong password, so that valid IDs are not revealed. |

#### Lockout timing

| Quantity | Value |
|---|---|
| Failed attempts that trigger a lock | 3 consecutive |
| Window in which they are counted | 5 minutes |
| Lock duration | 15 minutes |
| Session lifetime | 30 minutes |

### 4.2 Account Management Logic


These requirements refine **UR-2** and supply the account rules used by **UR-5**.

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

### 5.1 Security Requirements
* All user password inputs must be hashed using bcrypt before storage.
* All external communications must use TLS 1.3 encryption.
* Multi-Factor Authentication (MFA) is mandatory for all user login attempts.

### 5.2 Performance & Reliability
* System API response time must be under 200ms for 95% of standard user queries.
* System availability objective is 99.9% uptime during operational hours.


### 6.1 Actor Identification

| Actor | Type | Role and goals |
|---|---|---|
| Customer | Primary (left of the boundary) | Starts every service: logs in, creates an account, checks the balance, deposits and withdraws funds |
| Current Account Holder | Primary, specialises Customer | Inherits every Customer interaction; withdrawals may use the overdraft |
| Savings Account Holder | Primary, specialises Customer | Inherits every Customer interaction; earns monthly interest and must keep the minimum balance |
| Auth Server | Secondary «System» (right of the boundary) | Verifies passwords and MFA codes |
| Core Banking Database | Secondary «System» (right of the boundary) | Stores customers, accounts, the transaction ledger and audit logs |

### 6.2 UML Use Case Diagram

![UML use case diagram of the Secure Banking Application](usecase-diagram.svg)

*Figure 1 – Use case diagram of the Secure Banking Application. Solid lines are associations. Dashed arrows labelled «include» point from a base use case to a step it always performs. Dashed arrows labelled «extend» point from an optional use case to the base use case it extends, with the condition in brackets. Hollow triangles show generalization and point to the parent.*

### 6.3 Use Case Summary & Relationship Rationale

| ID | Use case | Relationship | Why this relationship |
|---|---|---|---|
| UC-01 | Log In | Core | Entry point to all account services (UR-1) |
| UC-02 | Create Account | Core, generalized | Parent of UC-11 and UC-12 (UR-2) |
| UC-03 | Withdraw Funds | Core | Includes UC-07 and UC-08; extended by UC-10 (UR-5) |
| UC-04 | Check Balance | Core | Reads the balance from the Core Banking Database (UR-3) |
| UC-05 | Deposit Funds | Core | Includes UC-08 (UR-4) |
| UC-06 | Verify MFA Code | «include» from UC-01 | Mandatory: UR-1 requires a second factor at every login |
| UC-07 | Verify Balance | «include» from UC-03 | Mandatory: every withdrawal must be checked against the account's floor |
| UC-08 | Record Transaction | «include» from UC-03 and UC-05 | Mandatory and shared: every monetary operation writes a ledger record |
| UC-09 | Lock Account | «extend» to UC-01 | Conditional: happens only after 3 failed attempts within 5 minutes |
| UC-10 | Display Overdraft Warning | «extend» to UC-03 | Conditional: happens only when a withdrawal takes the balance below zero |
| UC-11 | Open Current Account | Generalization to UC-02 | A kind of Create Account with the overdraft rule |
| UC-12 | Open Savings Account | Generalization to UC-02 | A kind of Create Account with the interest and minimum-balance rules |

Authentication is modelled as a **precondition** of UC-03, UC-04 and UC-05 rather than as an «include», because the customer logs in once per session and not again inside each operation.

### 6.4 Tabular Use Case Specifications

#### UC-01: Log In

| Field | Specification |
|---|---|
| Use Case ID & Name | UC-01: Log In |
| Primary Actor | Customer |
| Secondary Actor | Auth Server |
| Description | The customer proves their identity with a user ID, a password and an MFA code to start a session. |
| Preconditions | The customer has a registered account; the account is not locked. |
| Main Success Scenario | 1. Customer enters User ID and password.<br>2. System sends the credentials to the Auth Server, which checks the BCrypt hash.<br>3. System runs **Verify MFA Code** («include»).<br>4. System issues a 30-minute session token and records a sign-in audit entry.<br>5. System shows the account dashboard. |
| Extensions / Exceptions | 2a. Wrong password: system shows a generic error and counts the failure.<br>2b. Third failure within 5 minutes: system runs **Lock Account** («extend») and locks the account for 15 minutes.<br>3a. Wrong or expired MFA code: no session is issued. |
| Postconditions | A valid session exists; failed attempts are recorded in the audit log. |
| Related Requirements | UR-1; SR-1.1, SR-1.2, SR-1.3, SR-1.4 |

#### UC-02: Create Account

| Field | Specification |
|---|---|
| Use Case ID & Name | UC-02: Create Account |
| Primary Actor | Customer |
| Secondary Actor | Core Banking Database |
| Description | The customer opens a Current Account (UC-11) or a Savings Account (UC-12). |
| Preconditions | The customer has provided all required details. |
| Main Success Scenario | 1. Customer chooses the account type and enters the required details.<br>2. System validates the details.<br>3. System creates the account with a unique account number, the chosen type, status ACTIVE and a balance of USD 0.00.<br>4. System stores the account in the Core Banking Database and confirms to the customer. |
| Extensions / Exceptions | 2a. A required field is missing or invalid: system shows which field to correct and creates nothing.<br>2b. Account type is not Current or Savings: request rejected. |
| Postconditions | One new ACTIVE account exists; an audit entry is recorded. |
| Related Requirements | UR-2; SR-2.1, SR-2.2, SR-2.3, SR-2.4 |

#### UC-03: Withdraw Funds

| Field | Specification |
|---|---|
| Use Case ID & Name | UC-03: Withdraw Funds |
| Primary Actor | Authenticated Customer |
| Secondary Actor | Core Banking Database |
| Description | The customer withdraws an amount from their Current or Savings Account. |
| Preconditions | Customer is authenticated; session token is active; Account status = ACTIVE. |
| Main Success Scenario | 1. Customer selects the account and enters the withdrawal amount A.<br>2. System runs **Verify Balance** («include») against the account's floor.<br>3. System deducts A from the balance.<br>4. System runs **Record Transaction** («include») to append the ledger record.<br>5. System shows the new balance as acknowledgment. |
| Extensions / Exceptions | 2a. Current Account and the new balance is below zero but within the overdraft limit: system runs **Display Overdraft Warning** («extend») and continues at step 3.<br>2b. The new balance would be below the permitted floor: transaction is aborted, the database rolls back, and the system returns `ERR_INSUFFICIENT_FUNDS`.<br>1a. Session expired: system redirects the customer to Log In. |
| Postconditions | Balance_new = Balance_old − A and one ledger record exists; on any failure nothing has changed. |
| Related Requirements | UR-5; SR-2.1, SR-2.3, SR-3.1, SR-3.4 |

#### UC-04: Check Balance

| Field | Specification |
|---|---|
| Use Case ID & Name | UC-04: Check Balance |
| Primary Actor | Authenticated Customer |
| Secondary Actor | Core Banking Database |
| Description | The customer views the balance, account number and type of their own account. |
| Preconditions | Customer is authenticated; session token is active. |
| Main Success Scenario | 1. Customer opens the account view.<br>2. System reads the account owned by the customer from the Core Banking Database.<br>3. System displays the balance, account number and account type. |
| Extensions / Exceptions | 1a. Session expired: system redirects the customer to Log In.<br>2a. The account does not belong to the customer: access denied. |
| Postconditions | No data is changed; the response is given in under 1.5 seconds. |
| Related Requirements | UR-3; SR-3.3 |

#### UC-05: Deposit Funds

| Field | Specification |
|---|---|
| Use Case ID & Name | UC-05: Deposit Funds |
| Primary Actor | Authenticated Customer |
| Secondary Actor | Core Banking Database |
| Description | The customer adds funds to their own account. |
| Preconditions | Customer is authenticated; session token is active; Account status = ACTIVE. |
| Main Success Scenario | 1. Customer selects the account and enters the deposit amount A.<br>2. System validates that A is greater than USD 0.00.<br>3. System adds A to the balance.<br>4. System runs **Record Transaction** («include») to append the ledger record.<br>5. System shows the new balance. |
| Extensions / Exceptions | 2a. Invalid amount: system returns `ERR_INVALID_AMOUNT` and nothing changes.<br>4a. The ledger record cannot be written: the credit is rolled back.<br>1a. Session expired: system redirects the customer to Log In. |
| Postconditions | Balance_new = Balance_old + A and one ledger record exists. |
| Related Requirements | UR-4; SR-3.2, SR-3.4 |


## 7. Requirements Traceability & Change Control

Requirements traceability links each user need to its detailed system requirements, its use case and the automated test that will verify it. Change control governs how changes to requirements are evaluated, approved and tracked.

### 7.1 Requirements Traceability Matrix (UR → SR → UC → Test)

| User Requirement (UR) | System Requirement (SR) | UML Use Case | Automated JUnit Test |
|---|---|---|---|
| UR-1 [Security] | SR-1.1, SR-1.2, SR-1.3, SR-1.4 | UC-01: Log In (UC-06, UC-09) | `AuthenticationTest.java` |
| UR-2 [Account Creation] | SR-2.1, SR-2.2, SR-2.3, SR-2.4 | UC-02: Create Account (UC-11, UC-12) | `AccountCreationTest.java`, `AccountRulesTest.java` |
| UR-3 [Balance Inquiry] | SR-3.3 | UC-04: Check Balance | `BalanceInquiryTest.java` |
| UR-4 [Deposit] | SR-3.2, SR-3.4 | UC-05: Deposit Funds (UC-08) | `DepositFundsTest.java` |
| UR-5 [Withdrawal] | SR-3.1, SR-3.4, SR-2.1, SR-2.3 | UC-03: Withdraw Funds (UC-07, UC-08, UC-10) | `WithdrawalAtomicTest.java` |

The JUnit test classes are planned names. They will be implemented under `/tests` when coding starts in Milestone 2.

### 7.2 What each planned test class verifies

| Test class | Verifies |
|---|---|
| `AuthenticationTest.java` | BCrypt cost factor 12; lock after 3 failures within 5 minutes; MFA code required; session expiry |
| `AccountCreationTest.java` | Both account types can be created; invalid requests are rejected |
| `AccountRulesTest.java` | Overdraft limit of USD 500.00; minimum balance of USD 100.00; interest `I = B * (r / 12)` |
| `BalanceInquiryTest.java` | Only the owner can read a balance; no data changes; response under 1.5 seconds |
| `DepositFundsTest.java` | Balance increases by the amount; ledger record written; invalid amounts rejected |
| `WithdrawalAtomicTest.java` | Boundary values at the floor; `ERR_INSUFFICIENT_FUNDS`; rollback; concurrent withdrawals |

### 7.3 Change Control

1. **Request:** a team member opens a GitHub Issue labelled `change-request` naming the affected UR, SR or UC.
2. **Impact analysis:** the Product Owner and System Architect use the matrix above to list every affected requirement, use case and test.
3. **Approval:** the Product Owner approves scope and priority; the System Architect approves the technical impact.
4. **Implementation:** the change is made on a `feature/*` branch that updates this SRS, the UML diagram and the tests together, and is merged by pull request with at least one peer review.
5. **Baseline:** the revision history table is updated and the matrix is re-checked.
