## Phase-7: Security Audit & System Evaluation
### 1. Security Scanning with Lynus
Run the command: `sudo lynis audit system`
**Lynis Scores before & After Remediation**
_Before:_
<img width="1185" height="721" alt="image" src="https://github.com/user-attachments/assets/a23f5f0a-f42d-4d0e-861e-40748bf3172d" />

I addressed several findings from the initial Lynis scan to improve the system's hardening index. Specifically, I hardened the SSH configuration by disabling unneeded forwarding options and increasing logging verbosity. I also installed debsums and apt-show-versions to improve package integrity verification and added legal banners to /etc/issue to warn unauthorized users.

_After:_
<img width="970" height="560" alt="image" src="https://github.com/user-attachments/assets/baabe02f-a53c-469c-b594-6fa8c7e15f93" />

### 2. Network Scanning with NMap
<img width="1028" height="108" alt="image" src="https://github.com/user-attachments/assets/30ab621f-0724-4804-8062-fdfe6e56b8d2" />


### 3. Service Audit
Run: `sudo ss -tulpn`
Output:

| Netid | State  | Recv-Q | Send-Q | Local Address:Port | Peer Address:Port | Process |
|------|--------|--------|--------|--------------------|-------------------|---------|
| udp | UNCONN | 0 | 0 | 127.0.0.54:53 | 0.0.0.0:* | systemd-resolve (pid 645, fd 16) |
| udp | UNCONN | 0 | 0 | 127.0.0.53%lo:53 | 0.0.0.0:* | systemd-resolve (pid 645, fd 14) |
| udp | UNCONN | 0 | 0 | 192.168.0.182%ens33:68 | 0.0.0.0:* | systemd-network (pid 804, fd 24) |
| udp | UNCONN | 0 | 0 | [fe80::20c:29ff:fe09:1215]%ens33:546 | [::]:* | systemd-network (pid 804, fd 22) |
| tcp | LISTEN | 0 | 511 | 0.0.0.0:80 | 0.0.0.0:* | nginx (pid 1365–1367, fd 5) |
| tcp | LISTEN | 0 | 4096 | 0.0.0.0:22 | 0.0.0.0:* | sshd (pid 41976), systemd (pid 1) |
| tcp | LISTEN | 0 | 4096 | 127.0.0.53%lo:53 | 0.0.0.0:* | systemd-resolve (pid 645, fd 15) |
| tcp | LISTEN | 0 | 4096 | 127.0.0.54:53 | 0.0.0.0:* | systemd-resolve (pid 645, fd 17) |
| tcp | LISTEN | 0 | 511 | [::]:80 | [::]:* | nginx (pid 1365–1367, fd 6) |
| tcp | LISTEN | 0 | 4096 | *:5201 | *:* | iperf3 (pid 963, fd 3) |
| tcp | LISTEN | 0 | 4096 | [::]:22 | [::]:* | sshd (pid 41976), systemd (pid 1) |

### 4. Access Control & SSH Verification
<img width="812" height="333" alt="image" src="https://github.com/user-attachments/assets/e0f26bab-dc99-4a10-853f-b77265db2ed5" />
<img width="892" height="656" alt="image" src="https://github.com/user-attachments/assets/09e96fc1-8d5e-447e-837c-717072889883" />

### 5. Infrastructure Assessment & Remaining Risks
**Infrastructure Assessment: ** The infrastructure security assessment confirms that the server is in a robust, hardened state, employing a defense-in-depth strategy to effectively mitigate identified threats. All mandatory controls have been validated, including a strict default-deny firewall configuration (UFW), SSH hardening with key-based authentication, and mandatory access control via AppArmor. System integrity is preserved through automated patch management and continuous monitoring, with the server's security posture validated by a final Lynis Hardening Index, demonstrating a significant improvement over the baseline configuration and successfully reducing the attack surface as required.

**Remaining Risks:** The main remaining risk is a zero-day exploit in the kernel or sshd, which is mitigated by automatic updates. A secondary risk is the theft of the private SSH key from the workstation, which would grant an attacker full access. This could be mitigated by adding a strong passphrase to the SSH key.
