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

> _Owner: UI/UX Lead — branch `feature/srs-user-reqs`. Replace this line with the section content._

## 4. Detailed System Requirements Specification

System requirements expand the user requirements into explicit technical statements that give exact inputs, outputs, preconditions, postconditions and exception handling.

### 4.1 Authentication & Access Control

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
