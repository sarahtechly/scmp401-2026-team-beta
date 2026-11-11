<!-- SYSTEM ARCHITECT · branch feature/uml-usecase
     1. Upload usecase-diagram.svg into the docs/ folder on this branch.
     2. Paste everything below under "## 6. Graphical Models & Tabular Use Case Specification", replacing the placeholder line. -->

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
