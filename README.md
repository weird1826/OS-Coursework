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

## Phase-3: Application Selection for Performance Testing
### 1. Application Selection Matrix

| Workload Type     | Selected Application(s) | Justification |
|-------------------|--------------------------|---------------|
| **CPU-Intensive** | stress-ng (CPU test)     | A standard utility designed to predictably stress-test system components. |
| **RAM-Intensive** | stress-ng (Memory test)  | To allocate large amounts of memory, forcing the system to use swap space and allowing us to measure memory pressure. |
| **I/O-Intensive** | dd (Disk test)           | A core Linux utility that can be used to perform raw read/write tests to the disk to measure disk I/O performance directly. |
| **Network-Intensive** | iperf3               | The industry-standard tool for testing network bandwidth. |
| **Server Application** | Python `http.server`               | Simple HTTP server built into Python. |

### 2. Installation Documentation

```bash
# Update package lists first
sudo apt update

# Install stress-ng for CPU and RAM tests
sudo apt install stress-ng -y

# 'dd' is part of system

# Install iperf3 for network tests
sudo apt install iperf3 -y
```

### 3. Expected Resource Profiles
* `stress-ng` (CPU Test)
  * Expected: CPU usage will jump to 100% for each core being tested. Memory and disk I/O should remain low.
* `stress-ng` (Memory Test)
  * Expected: Memory usage (`free -h`) will show "used" memory climb until "available" memory is very low. System will likely start using swap space. CPU usage may be moderate.
* `dd` (Disk Test)
  * Expected: Disk I/O metrics (`iostat`) will show high megabytes written per second. The CPU will show a high waiting percentage for the disk.
* `iperf3` (Network Test)
  * Expected: Network traffic will max out the VM adapter's bandwidth. CPU usage will be moderate, as it processes network packets.

### 4. Monitoring Strategy
* `stress-ng` (CPU):
  * Test Command (Server): `stress-ng --cpu 1 --timeout 60s`
  * Monitoring (via SSH): `uptime` and `top -b -n 1 | grep "Cpu(s)"`
* `stress-ng` (Memory):
  * Test Command (Server): `stress-ng --vm 1 --vm-bytes <SRV_RAM>G --timeout 60s` (Adjust SRV_RAM based on the choosen server platform).
  * Monitoring (via SSH): `free -h` before, during, and after the test.
* `dd` (Disk I/O):
  * Test Command (Server): `dd if=/dev/zero of=testfile bs=1G count=1 oflag=direct` (This writes a 1GB file).
  * Monitoring (via SSH): `iostat -xz 1 10` while the test runs to capture disk wMB/s.
* `iperf3` (Network):
  * Test Command (Server): `iperf3 -s`
  * Test Command (Workstation): `iperf3 -c <server_IP_address>` or GUI application.
  * Monitoring: The `iperf3` client output itself provides the main metric (e.g., "Mbits/sec").
 
## Phase-4: Action Plan
### 1. User and Privilege Management
```bash
# LOGGING IN VIA SSH WITH THE USERNAME CREATED DURING SETUP...

# Create a new user
sudo adduser admin

# Give the user sudo privileges
sudo usermod -aG sudo admin

# LOGGING OUT OF THE SERVER...
# LOGGING IN VIA SSH USING THE admin USER...
ssh admin@server_ip

# Test sudo access
sudo whoami
```
<img width="763" height="461" alt="image" src="https://github.com/user-attachments/assets/c608c4bc-52a9-4a10-8494-47fb54e92f08" />
<img width="577" height="665" alt="image" src="https://github.com/user-attachments/assets/421e598e-b1a5-460a-95ad-c12e1d6a1d1d" />

### 2. Setting up SSH key-based authentication
<img width="707" height="631" alt="image" src="https://github.com/user-attachments/assets/6180c23a-510e-4d7a-b0ae-04f2f27f7536" />

### 3. SSH Hardening
`sshd_config` file before:
<img width="702" height="752" alt="image" src="https://github.com/user-attachments/assets/24b1c361-faa6-4aad-aa4c-df0c71affd29" />

`sshd_config` file after:
<img width="703" height="635" alt="image" src="https://github.com/user-attachments/assets/da06fc5e-f9e7-43cd-b26d-105be1e57d11" />

Verification:
<img width="543" height="558" alt="image" src="https://github.com/user-attachments/assets/a7ef8e11-b16b-4495-9553-27cdf9f3e79a" />

### 4. Firewall Configuration
<img width="666" height="182" alt="image" src="https://github.com/user-attachments/assets/3ac542c7-7d51-4809-808a-000ba0dc9641" />
<img width="570" height="146" alt="image" src="https://github.com/user-attachments/assets/866e05fc-7422-4ed3-91bb-896ea1586f87" />

## Phase-5: Advanced Security & Monitoring Infrastructure
### 1. Access Control using AppArmor
* Install AppArmor utilities: `sudo apt install apparmor-utils -y`.
* Check the status of all AppArmor profiles:
<img width="688" height="741" alt="image" src="https://github.com/user-attachments/assets/7a170044-f3f4-4793-84d9-f53a5273c6d5" />

The command shows a list of profiles. Enforced profiles are ones which are being actively blocked and the ones in complain mode are those with logging violations but not blocking.

### 2. Automatic Security Updates
* Install the package: `sudo apt install unattended-upgrades`
* Configure it: `sudo dpkg-reconfigure -plow unattended-upgrades` then click <Yes>.
<img width="871" height="193" alt="image" src="https://github.com/user-attachments/assets/0365293a-6ddf-4616-9526-5a78fd1e5ed3" />

### 2. Intrusion Detection
`fail2ban` monitors system logs for repeated failed login attempts and automatically bans the offending IP address using the firewall.
* Install `fail2ban`: `sudo apt install fail2ban -y`
* It starts automatically and protects SSH by default.
* Local config file is recommended to be created a copy of so the changes are not overwritten by updates: `sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`
* Edit local file: `sudo nano /etc/fail2ban/jail.local`
  * In the `[sshd]` section, ensure `enabled=ture` and set `bantime` whatever one prefers.
* Restart the service: `sudo systemctl restart fail2ban`

<img width="671" height="352" alt="image" src="https://github.com/user-attachments/assets/47c9436c-5f5e-46ac-b6c7-c8253b2873f2" />

### 4. `security-baseline.sh` script

Script:
```bash
#!/bin/bash
# a script to verify the security config of the server.

echo "Security Baseline Audit"

echo "[+] Checking SSH Configuration"

# check if password authentication is set to no
if grep -q "^PasswordAuthentication no" /etc/ssh/sshd_config; then
        echo "  [PASS] PasswordAuthentication is disabled."
else
        echo "  [FAIL] PasswordAuthentication is enabled."
fi

# check if root login is set to no
if grep -q "^PermitRootLogin no" /etc/ssh/sshd_config; then
    echo "  [PASS] Root login is disabled."
else
    echo "  [FAIL] Root login is ENABLED."
fi

echo "[+] Checking Firewall (UFW) Configuration..."

# check if firewall is active
if sudo ufw status | grep -q "Status: active"; then
    echo "  [PASS] Firewall is active."
    if sudo ufw status | grep -Pq "22/tcp\s+ALLOW\s+172\.20\.10\.3"; then
        echo "  [PASS] SSH is correctly restricted to workstation."
    else
        echo "  [FAIL] SSH rule is incorrect or missing."
    fi
else
    echo "  [FAIL] Firewall is INACTIVE."
fi

echo "[+] Checking Advanced Security..."

# check if fail2ban is running
if systemctl is-active --quiet fail2ban; then
    echo "  [PASS] fail2ban service is active."
else
    echo "  [FAIL] fail2ban service is INACTIVE."
fi

# check if apparmor is enabled
if sudo aa-status --enabled; then
    echo "  [PASS] AppArmor is enabled."
else
    echo "  [FAIL] AppArmor is DISABLED."
fi

# cehck if unattended-upgrades is configured
if [ -f /etc/apt/apt.conf.d/20auto-upgrades ]; then
    echo "  [PASS] Automatic updates appear to be configured."
else
    echo "  [FAIL] Automatic updates config file is missing."
fi

echo "--- Audit Complete ---"
```
Make the script executable: `chmod +x security-baseline.sh`

<img width="732" height="312" alt="image" src="https://github.com/user-attachments/assets/3c43def3-515b-4c81-8caf-2ee3f10e8c0f" />

### 5. `monitor-server.sh` Script

Script:
```bash
#!/bin/bash
# monitor-server.sh
# A script to run on the workstation to remotely monitor the server.

# --- Configuration ---
SERVER_USER="admin"
SERVER_IP="172.20.10.4"

echo "--- Remote Server Monitoring ---"
echo "Connecting to $SERVER_USER@$SERVER_IP..."
echo ""

# Command to run remotely (all in one SSH call)
ssh $SERVER_USER@$SERVER_IP "
    echo '--- System Uptime & Load ---'
    uptime
    echo ''

    echo '--- Memory Usage (Human-Readable) ---'
    free -h
    echo ''

    echo '--- Disk Space Usage (Human-Readable) ---'
    df -h /
    echo ''

    echo '--- Top 5 CPU-Consuming Processes ---'
    top -b -n 1 | head -n 12 | tail -n 6
"

echo "--- Monitoring Complete ---"
```

Make the script executable: `chmod +x monitor-server.sh`

<img width="810" height="432" alt="image" src="https://github.com/user-attachments/assets/f80f978c-c538-435d-bd3e-d366b24d6c68" />

## Phase-6 Performance Evaluation & Analysis
### 1. Testing Methodology & Commands
* **CPU Stress Test**
  * Test Command (Server): `stress-ng --cpu 2 --timeout 60s` - (stress 2 cores)
  * Monitor command (workstation via ssh): `./monitor-server.sh` - (High CPU % in `top`)
  <img width="852" height="717" alt="image" src="https://github.com/user-attachments/assets/11b3d432-7aea-490f-a5a9-01c108dd8719" />

* **RAM Stress Test**
  * Test Command (Server): `stress-ng --vm 1 --vm-bytes <RAM>M --timeout 60s` - (<RAM> can be set to as close as VM's ram to force swapping)
  * Monitor command (workstation via ssh): `./monitor-server.sh` - (Swap used in `free -h`)
  <img width="898" height="647" alt="image" src="https://github.com/user-attachments/assets/9a494600-ab68-4a54-a4ba-73722396e630" />

* **Disk I/O Test**
  * Test Command (Server): `dd if=/dev/zero of=testfile bs=1G count=1 oflag=direct`
  * Monitor command (workstation via ssh): `./monitor-server.sh` - (`dd` output for "MB/s")
* **Network Throughput Test**
  * Test Command (Server): `iperf3 -s`
  * Test Command (Workstation): `iperf3 -c <server_ip>` OR GUI
  * Monitor command (workstation): `iperf3` output on workstation shows "Bitrate"
* **Server App Test**
  * Setup (Server): Create 50MB dummy file to serve - `fallocate -l 50M testfile.dat`
  * Start (Server): `python3 -m http.server 8000`
  * Test command (workstation): `./loadtest.ps1`:
 
    ```ps
    # loadtest.ps1

    $ServerIP = "SERVER_ip"  # REPLACE with Server's IP
    $Port = "8000"
    $Url = "http://$($ServerIP):$($Port)/testfile.dat"
    $Requests = 100

    Write-Host "Starting Load Test on $Url..."
    $StartTime = Get-Date
    
    for ($i=1; $i -le $Requests; $i++) {
        try {
            # Send a request and discard the output ($null) to speed it up
            Invoke-WebRequest -Uri $Url -UseBasicParsing -OutFile $null
            Write-Host -NoNewline "." # Print a dot for progress
        }
        catch {
            Write-Host "Error connecting to server."
        }
    }

    $EndTime = Get-Date
    $Duration = $EndTime - $StartTime
    $Seconds = $Duration.TotalSeconds
    $RPS = $Requests / $Seconds

    Write-Host ""
    Write-Host "------------------------------------------------"
    Write-Host "Test Complete."
    Write-Host "Total Requests: $Requests"
    Write-Host "Total Time:     $($Seconds.ToString("N2")) seconds"
    Write-Host "Throughput:     $($RPS.ToString("N2")) Requests/Sec"
    Write-Host "------------------------------------------------"
    ```
 ### 2. Optimization Analysis
 **Optimization 1: Swap Tuning**
 * Linux Defaults to a swap of 60. It moves data from fast RAM to slow disk aggressively. This slows down the performance.
 * Lower the value to 10, telling the OS to keep data in RAM as long as possible.
 * Fix: `sudo sysctl vm.swappiness=10`

**Optimization 2: Tuning Network Buffers**
* Default Linux network buffers are too small for high-speed transfers, limiting max throughput.
* The fix: Increase the MAX TCP Buffer sizes:
  ```bash
  sudo sysctl -w net.core.rmem_max=16777216
  sudo sysctl -w net.core.wmem_max=16777216
  ```

### 3. Performance Data Table

| Application        | Metric        | Baseline Value | Load Test Value | Optimised Value |
|--------------------|---------------|----------------|-----------------|-----------------|
| Python Server      | Requests/sec  | 0 (Idle)       | 150 req/s      | 180 req/s      |
| stress-ng (RAM)    | Swap Used     | 0 MB           | 400 MB         | 150 MB         |

### 4. Performance Visualization

**TODO: IMAGES**

