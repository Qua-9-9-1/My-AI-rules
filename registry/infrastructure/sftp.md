# SFTP Infrastructure Guidelines

These guidelines define standard security and performance practices for integrating secure file transfers over SSH (SFTP) within the application.

---

## 1. Authentication & Security Controls

* **Key-Based Authentication**: Strongly prefer SSH private keys (e.g., PEM, OpenSSH formats) over password-based authentication. Passwords and private keys must be stored in secure configuration settings or environment variables, never hardcoded.
* **Host Key Verification**: Every SFTP client connection must validate the target server's host key against a trusted list of known host fingerprints (e.g., MD5, SHA-256) to verify server identity and prevent man-in-the-middle (MITM) attacks. Do not implement a policy that accepts all host keys blindly.

---

## 2. Session Management & Transfer Integrity

* **Connection Disposal**: Always wrap SFTP client sessions inside a `using` declaration or ensure explicit closure of connection channels and sockets on completion of files transfer to prevent SSH daemon thread exhaustion.
* **Timeout & Retries**: Implement connection timeouts (e.g., 15-30 seconds) and retry mechanisms for network-related drops, distinguishing between transient connection failures and permanent authentication errors.
* **Transfer Integrity Verification**: Validate the integrity of uploaded or downloaded files by verifying file sizes on the destination, or comparing hash checksums if supported by the remote SFTP server environment.
