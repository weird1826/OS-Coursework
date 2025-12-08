# OS-Coursework
## Phase-1: System Planning and Distribution Selection
### 1. System Architecture Diagram
**TODO**
### 2. Distribution Selection Justification
I have selected Ubuntu Server 22.04 LTS for this project. It is due to its widespread use in industry, extensive documentation, and strong community support. It has AppArmor included in it, which fulfills several security requirements for Mandatory Access Control. This will allow me to focus on configuration and analysis rather than complex initial setup of a security module.
### 3. Workstation Configuration Decision
For my workstation, I will use my host machine - Windows 11. This approach is efficient in terms of resource as it only requires running on VM. For SSH operations to perform remote administration tasks, I will use the built-in Windows Terminal.
### 4. Network Configuration
The Ubuntu VM's network configuration is set to Bridged-Adapter Mode, allowing the VM to independently access the internet with its own unique IP address. This allows less reliance on the host machine. This does not restrict the host to SSH into the VM.
**P1S4I1**
**P1S4I2**
**P1S4I3**
### 5. Initial System Specification
The following commands were ran via SSH:
* `uname -a`
* `free -h`
* `df -h`
* `ip addr`
* `lsb_release -a`

**Outputs:**
**P1S5I1**
