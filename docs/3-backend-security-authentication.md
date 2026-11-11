<!-- BACK-END & SECURITY LEAD · branch feature/srs-authentication
     Paste everything below under "### 4.1 Authentication & Access Control", replacing the placeholder line. -->

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
