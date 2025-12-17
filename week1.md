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
