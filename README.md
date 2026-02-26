# Task 14: Configuration Management Using Ansible

## Overview
This task demonstrates configuration management using Ansible on WSL (Windows Subsystem for Linux) with Ubuntu 22.04.

---

## Tools Used
- **Primary:** Ansible
- **Alternatives:** Chef, Puppet
- **OS:** Ubuntu 22.04 (WSL on Windows)

---

## Project Structure
```
ansible-task14/
├── inventory.ini        # Target hosts configuration
├── site.yml             # Main Ansible playbook
├── execution_output.log # Playbook execution log
├── screenshots/         # Step-by-step screenshots
└── README.md            # Project documentation
```

---

## Prerequisites
- Windows 10/11 with WSL2 enabled
- Ubuntu 22.04 installed via WSL
- Internet connection

---

## Commands Used

### 1. Install WSL (PowerShell - Admin)
```powershell
wsl --install
wsl -d Ubuntu-22.04
```

### 2. Install Ansible (Inside WSL)
```bash
sudo apt update
sudo apt install -y ansible
ansible --version
```

### 3. Create Project Directory
```bash
mkdir ~/ansible-task14
cd ~/ansible-task14
```

### 4. Create Inventory File
```bash
nano inventory.ini
```
**inventory.ini:**
```ini
[webservers]
localhost ansible_connection=local
```

### 5. Test Connectivity
```bash
ansible -i inventory.ini all -m ping
```

### 6. Create Playbook
```bash
nano site.yml
```

**site.yml:**
```yaml
---
- name: Task 14 - Configuration Management
  hosts: webservers
  become: yes

  vars:
    packages:
      - curl
      - git
      - nginx

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install packages
      apt:
        name: "{{ packages }}"
        state: present

    - name: Ensure nginx is started
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Create a config file
      copy:
        content: |
          server {
              listen 8080;
              server_name localhost;
          }
        dest: /etc/nginx/conf.d/custom.conf
        mode: '0644'
      notify: Reload Nginx

  handlers:
    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded
```

### 7. Run Playbook and Save Log
```bash
ansible-playbook -i inventory.ini site.yml | tee execution_output.log
```

### 8. Verify nginx is Running
```bash
systemctl status nginx
```

---

## Execution Output
```
PLAY [Task 14 - Configuration Management] **************************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Update apt cache] ********************************************************
ok: [localhost]
TASK [Install packages] ********************************************************
ok: [localhost]
TASK [Ensure nginx is started] *************************************************
ok: [localhost]
TASK [Create a config file] ****************************************************
changed: [localhost]
RUNNING HANDLER [Reload Nginx] *************************************************
changed: [localhost]
PLAY RECAP *********************************************************************
localhost : ok=6  changed=2  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

## Key Concepts Learned
- **Inventory** - Defines target hosts
- **Playbook** - YAML automation script
- **Modules** - apt, service, copy, template
- **Variables** - Dynamic values using {{ var_name }}
- **Idempotency** - Safe to run multiple times
- **Handlers** - Tasks triggered only on change
- **Roles** - Reusable playbook structure

---

## Deliverables
- ✅ Ansible Playbook (site.yml)
- ✅ Execution Output Log (execution_output.log)

---

## Final Outcome
Intern understands configuration automation using Ansible.
