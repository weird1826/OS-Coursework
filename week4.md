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
