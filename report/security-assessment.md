# SWYNEX Security Fundamentals Assessment

## Task 1 — Security Fundamentals Assessment

## 1. Executive Summary

A security assessment was performed against an intentionally vulnerable Metasploitable 2 virtual machine in an isolated VirtualBox laboratory environment.

The assessment identified multiple security weaknesses including anonymous FTP access, anonymous SMB access, a broad NFS export, and disabled SMB message signing. The target also exposed numerous legacy and network-facing services.

## 2. Scope and Authorization

### Target
- Metasploitable 2
- IP address: `192.168.56.101`

### Assessment Workstation
- Kali Linux
- IP address: `192.168.56.102`

Testing was limited to the intentionally vulnerable laboratory target.

## 3. Methodology

1. Host discovery
2. Full TCP port scanning
3. Service and version enumeration
4. Service-specific security checks
5. Evidence collection
6. Risk assessment
7. Mitigation planning

## 4. Service Enumeration

| Port | Service | Detected Software |
|---:|---|---|
| 21 | FTP | vsftpd 2.3.4 |
| 22 | SSH | OpenSSH 4.7p1 |
| 23 | Telnet | Linux telnetd |
| 25 | SMTP | Postfix |
| 53 | DNS | ISC BIND 9.4.2 |
| 80 | HTTP | Apache 2.2.8 |
| 111 | RPC | rpcbind |
| 139 | NetBIOS/SMB | Samba |
| 445 | SMB | Samba 3.0.20-Debian |
| 2049 | NFS | NFS v2–4 |
| 3306 | MySQL | MySQL 5.0.51a |
| 5432 | PostgreSQL | PostgreSQL 8.3 |
| 5900 | VNC | VNC 3.3 |
| 8180 | HTTP | Apache Tomcat 5.5 |

Complete scan output:

`evidence/reconnaissance/full-port-scan.txt`

# 5. Security Findings

## F-01 — Anonymous FTP Access

### Affected Component

TCP port 21 — vsftpd 2.3.4

### Evidence

The FTP enumeration reported:

`ftp-anon: Anonymous FTP login allowed (FTP code 230)`

The service information also indicated that FTP control and data connections were plain text.

Evidence:

`evidence/findings/ftp-anonymous.txt`

### Risk

Anonymous FTP permits users to authenticate without a normal account. Depending on permissions, this can expose resources that should not be publicly accessible.

Plain-text FTP also does not provide confidentiality for normal FTP communications.

### Recommended Mitigation

- Disable anonymous FTP unless explicitly required.
- Restrict FTP access to trusted hosts if required.
- Use SFTP or another encrypted file-transfer mechanism.
- Apply least-privilege filesystem permissions.
- Monitor authentication and file-transfer activity.

---

## F-02 — Anonymous SMB Access

### Affected Component

TCP ports 139 and 445 — Samba 3.0.20-Debian

### Evidence

Anonymous SMB enumeration reported:

`Anonymous login successful`

Exposed shares included:

- `print$`
- `tmp`
- `opt`
- `IPC$`
- `ADMIN$`

### Risk

Unauthenticated SMB access can allow users to enumerate shared resources and, depending on permissions, access files or other network resources without appropriate authentication.

### Recommended Mitigation

- Disable unnecessary guest/anonymous SMB access.
- Require authenticated access.
- Apply least-privilege permissions.
- Remove unnecessary shares.
- Restrict SMB through firewall rules.
- Monitor SMB access.

---

## F-03 — Broad NFS Export

### Affected Component

TCP port 2049 — NFS

### Evidence

NFS enumeration reported:

`Export list for 192.168.56.101: / *`

This indicates that the root filesystem export `/` is available to clients represented by `*`.

### Risk

A broad NFS export can expose filesystem resources to clients that should not have access. Actual impact depends on filesystem permissions and export configuration.

### Recommended Mitigation

- Do not export `/` unless absolutely necessary.
- Export only required directories.
- Restrict exports to trusted clients.
- Use read-only exports where possible.
- Enable root squashing.
- Review NFS firewall rules.

---

## F-04 — SMB Message Signing Disabled

### Affected Component

TCP port 445 — Samba

### Evidence

SMB security enumeration reported:

`message_signing: disabled`

The evidence also showed the account used was `guest`.

Evidence:

`evidence/findings/smb-signing.txt`

### Risk

Disabled SMB message signing reduces protection against certain network-level tampering scenarios. This configuration finding alone does not demonstrate that an attack has occurred.

### Recommended Mitigation

- Enable SMB message signing.
- Require signing where appropriate.
- Restrict SMB exposure to trusted networks.
- Review authentication and protocol configuration.
- Maintain supported and appropriately patched Samba software.

# 6. Additional Attack-Surface Observations

The scan identified additional legacy or network-facing services including:

- Telnet
- rsh/rlogin
- VNC
- FTP
- SMB
- NFS
- Legacy database services
- Apache/Tomcat
- IRC
- Java/Ruby remote services

These are documented as attack-surface observations rather than separate confirmed vulnerabilities unless supported by specific evidence.

# 7. Risk Summary

| ID | Finding | Risk |
|---|---|---|
| F-01 | Anonymous FTP | Unauthorized resource access / plaintext communication |
| F-02 | Anonymous SMB | Unauthorized share enumeration/access |
| F-03 | Broad NFS export | Excessive filesystem exposure |
| F-04 | SMB signing disabled | Reduced message integrity protection |

# 8. Overall Recommendations

1. Remove unnecessary legacy services.
2. Disable anonymous and guest access where unnecessary.
3. Restrict network-facing services using firewall rules.
4. Apply least-privilege permissions.
5. Replace insecure plaintext protocols with secure alternatives.
6. Restrict NFS exports to required directories and trusted clients.
7. Enable appropriate SMB security controls.
8. Keep exposed software supported and patched.
9. Monitor authentication and network-service activity.

# 9. Conclusion

The assessment demonstrated how reconnaissance, service enumeration, targeted configuration checks, and evidence collection can identify security weaknesses in an intentionally vulnerable environment.

The exercise provided practical experience with network discovery, service identification, evidence collection, risk documentation, and mitigation planning.

All testing described in this report was conducted against the authorized Metasploitable 2 laboratory target.
