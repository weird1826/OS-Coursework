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
 
