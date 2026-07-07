# LDAPs Infrastructure Guidelines

These guidelines define the security and implementation rules for integrating secure LDAP (LDAPs) authentication and directory services within the application.

---

## 1. Transport Security & Port Enforcement

* **Mandatory Port 636**: Only secure LDAPs via SSL/TLS on port 636 is permitted. Cleartext LDAP on port 389 must be blocked and rejected.
* **Certificate Validation**: Secure connection handshakes must validate the server's SSL/TLS certificates. Self-signed certificates must be added to the trusted root authority store rather than bypassing validation logic.
* **Strict Hostname Verification**: The connection settings must explicitly match the common name (CN) or subject alternative name (SAN) specified in the server's certificate to prevent man-in-the-middle (MITM) attacks.

---

## 2. Secure Binding & Connection Lifecycle

* **Service Account Credentials**: Credentials for binding to the LDAP server (Bind DN and Password) must never be hardcoded. Retrieve these values through secure environment variables or global settings.
* **Connection Disposal**: Directory connections must be closed immediately after completing searches or credentials checks to prevent resource leaks on both the client and server side.
* **Safe Input Sanitization**: User inputs evaluated inside LDAP filters (such as searching for a user by username) must be sanitized to prevent LDAP Injection attacks (e.g., escaping special characters like `*`, `(`, `)`, `\`, and `NUL`).
