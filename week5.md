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
