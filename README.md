# OS-Coursework
## Phase-1: System Planning and Distribution Selection
### 1. System Architecture Diagram
<img width="500" height="495" alt="image" src="https://github.com/user-attachments/assets/e821f57e-4a09-41aa-a652-490ea472dca5" />

### 2. Distribution Selection Justification
I have selected Ubuntu Server 22.04 LTS for this project. It is due to its widespread use in industry, extensive documentation, and strong community support. It has AppArmor included in it, which fulfills several security requirements for Mandatory Access Control. This will allow me to focus on configuration and analysis rather than complex initial setup of a security module.
### 3. Workstation Configuration Decision
For my workstation, I will use my host machine - Windows 11. This approach is efficient in terms of resource as it only requires running on VM. For SSH operations to perform remote administration tasks, I will use the built-in Windows Terminal.
### 4. Network Configuration
The Ubuntu VM's network configuration is set to Bridged-Adapter Mode, allowing the VM to independently access the internet with its own unique IP address. This allows less reliance on the host machine. This does not restrict the host to SSH into the VM.
<img width="877" height="890" alt="P1S4I1" src="https://github.com/user-attachments/assets/15d98f44-dbe9-49aa-ba4e-a9c4771e5386" />
<img width="813" height="302" alt="P1S4I2" src="https://github.com/user-attachments/assets/6728b2b1-352f-43a4-970e-67000108d5df" />
<img width="760" height="758" alt="P1S4I3" src="https://github.com/user-attachments/assets/40576658-4fba-4e99-b42b-41cb2e40153c" />
### 5. Initial System Specification
The following commands were ran via SSH:
* `uname -a`
* `free -h`
* `df -h`
* `ip addr`
* `lsb_release -a`

**Outputs:**
<img width="1245" height="800" alt="P1S5I1" src="https://github.com/user-attachments/assets/3c4bfd74-65a0-4cf5-bf0f-8e13c29e6fea" />

## Phase-2: Security Planning & Testing Methodology
### 1. Performance Testing Plan
* **Remote monitoring Methdology**: All the monitoring will be performed from the workstation by executing a remote script (`monitor-server.sh`) via SSH. This script will run commands on the server and print the output to the workstation's terminal.
* **Testing Approach**: Three types of testing will be undertaken - _Baseline_, _Load_, and _Optimisation_.
  * **Baseline Testing**: Measuring the server at idle with the applications running normally.
  * **Load Testing**: Measuring the server while actively stressing the application.
  * **Optimisation Testing**: After making a performance-tuning change, re-running the load test to measure the improvement.
* **Metrics & Tools**:

| Metric to Measure          | Planned Tool(s)              | Justification                                                 |
| -------------------------- | ---------------------------- | ------------------------------------------------------------- |
| **CPU Usage**              | `top` (batch mode), `uptime` | To see process-level CPU load and average system load.        |
| **Memory Usage**           | `free -h`                    | To check available RAM and swap usage.                        |
| **Disk I/O**               | `iostat`, `df -h`            | To measure read/write speeds and disk space usage.            |
| **Network Performance**    | `ping`, `iperf3`  | To measure latency and throughput between server/workstation. |
| **System Latency**         | `ping`                       | To measure basic network response time.                       |
| **Service Response Times** | `curl`           | To measure how long a web service takes to respond. |

### 2. Security Configuration Checklist
* [ ] **User Privilege Management**
  * [ ] Create a non-root administrative user with `sudo` access.
  * [ ] Disable the root account's login.
* [ ] **SSH Hardening**
  * [ ] Configure key-based authentication.
  * [ ] Disable password authentication.
  * [ ] Disable root login via SSH.
* [ ] **Network Security**
  * [ ] Configure `fail2ban` for SSH intrusion detection.
* [ ] **Firewall Configuration**
  * [ ] Enable UFW (Uncomplicated Firewall)
  * [ ] Create a rule to permit SSH only from the workstation's specific IP address.
  * [ ] Set the default policy to deny all incoming traffic.
* [ ] **Automatic Updates**
  * [ ] Configure `unattended-upgrades` to install security patches automatically.
* [ ] **Mandatory Access Control (MAC)**
  * [ ] Implement and configure AppArmor to confine key services.

### 3. Threat Model
**Threat-1: Brute Force Attack on SSH**
* An attacker can repeatedly tries to guess the server's password for the `root` or other user accounts, gaining full access if successful.
* **Mitigation**:
  * Disable password authentication entirely and enforce high-strenght SSH keys. This makes the password impossible to guess.
  * Implement `fail2ban` to automatically ban IP addresses that show malicious behavior.

**Threat-2: Unauthorized Network Access / Vulnerability Scanning**
* An attacker on the same network scans for the server for open ports, identifies running services, and attempts to exploit them.
* **Mitigation**:
  * Configure UFW firewall to "deny by default".
  * Only open SSH port and restrict access exclusively to the static IP of the trusted workstation. All other ports and IPs will be blocked.

**Threat-3: Privilege Escalation via Software Vulnerability**
* An attacker exploits a bug in a running service to gain initial access as a low-privilege user. They then exploit a kernel or OS vulnerability to gain full `root` access.
* **Mitigation**:
  * Configure automatic security updates to ensure all known vulnerabilities are patched quickly, reducing the windowm of opportunity.
  * Implement AppArmor to confine services. Even if a service is exploited, AppArmor's profile will prevent the attacker from accessing other parts of the system, this stopping the privilege escalation.
