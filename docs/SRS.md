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



> _Owner: Back-End & Security Lead — branch `feature/srs-authentication`. Replace this line with the section content._

### 4.2 Account Management Logic

> _Owner: Back-End Developer — branch `feature/srs-accounts`. Replace this line with the section content._

### 4.3 Transaction Processing Logic

> _Owner: Database Lead — branch `feature/srs-transactions`. Replace this line with the section content._

## 5. Non-Functional Requirements & Metrics

> _Owner: ScrumMaster — branch `feature/srs-nfr`. Replace this line with the section content._

## 6. Graphical Models & Tabular Use Case Specification

> _Owner: System Architect — branch `feature/uml-usecase`. Replace this line with the section content._

## 7. Requirements Traceability & Change Control

> _Owner: Product Owner — branch `feature/srs-rtm`. Replace this line with the section content._
