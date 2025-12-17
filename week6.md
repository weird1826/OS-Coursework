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
<img width="632" height="468" alt="image" src="https://github.com/user-attachments/assets/8ba5e2d1-af59-4974-b43d-4f9a048c2fe6" />

