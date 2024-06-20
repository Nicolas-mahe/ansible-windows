---
marp: true
title: Ansible for Windows
theme: uncover
_class: lead
paginate: true
_paginate: false
backgroundImage: url('https://marp.app/assets/hero-background.svg')
---

# <!--fit--> Ansible for Windows

By Nicolas Mahé([@nicolas-mahe](https://github.com/nicolas-mahe))

---

### Ansible Communication Protocoles

- Ansible + Windows = WinRM on 5985 and 5986
- Ansible required powershell 3.0+ and .NET 4.0+ on target hosts
- Basic protocol for Simulation or Development
- Kerberos for production

---

### Ansible Configuration

- Ansible comes with a default configuration `/etc/ansible/ansible.cfg`.
- This configuration can be overriden with the following precedence:
  - The env variable: `ANSIBLE_CONFIG`
  - The file `ansible.cfg` on your current directory where ansible is executed
  - User home directory: `~/.ansible/ansible.cfg`
  - Default `/etc/ansible/ansible.cfg` file.

---

### Sample Configuration

```ini
[defaults]
host_key_checking = false
roles_path = ./roles
inventory = ./inventories
retry_files_enabled = false
remote_user = ansible
ask_vault_pass = false
deprecation_warnings = false
library = ./library/modules
vault_password_file = ./.vault_pass

[inventory]
enable_plugins = host_list, ini, script, yaml, auto

[privilege_escalation]
become_user = Administrator
become_method = runas

[ssh_connection]
pipelining = true
ssh_args = -o ControlMaster=no -o TCPKeepAlive=yes
```

---

### Inventory Sample structure

```plain
├── inventories
│   ├── dev
│   │   ├── group_vars
│   │   │   └── all.yml
│   │   │   └── backend.yml
│   │   ├── host_vars
│   │   │   └── db01.yml
│   │   │   └── backend01.yml
│   │   │   └── backend02.yml
│   │   │   └── frontend01.yml
│   │   └── hosts
```

---

### Inventory Sample hosts file

```ini
[db]           # group name
db01           # machine name

[backend]
backend0[1:2]  # use range to simplify backend01 and backend02

[frontend]
frontend01
```

---

### Required basic inventory Variables

```yml
ansible_connection: winrm
ansible_user: user
ansible_password: password
ansible_host: "10.10.10.10"
ansible_port : 5985
ansible_winrm_transport : basic
ansible_winrm_server_cert_validation: ignore
```

---

### Secret Management with Ansible Vault

- Use ansible-vault command to create vaulted passwords
```bash
ansible-vault encrypt_string 'stringToEncrypt'
```
- Recover the !vault content and paste it to your variable
```bash
ansible_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          62383936306662613131363633386437
```

---
### Playbook

```yml
- name: My awesome playbook
  hosts: all
  gather_facts: yes
  roles:
    - create-vm
    - install-database
```
```yml
- name: My awesome playbook
  hosts: all
  gather_facts: yes
  tasks:
    - name: Task
      win_ping:
```
---

- To Run a playbook on a specified inventory:
```bash
ansible-playbook deploy.yml -i inventories/dev
```

- If your playbook is in the playbook directory, specify the path (from your current directory)
```bash
ansible-playbook playbooks/deploy.yml -i inventories/dev
```

---

#### Role

Create a Role with command line : 
```bash
ansible-galaxy init roles/create-vm
```

- **meta**: Metadata of the role
- **defaults**: Default variables of the role
- **vars**: Other variables used by the roles and can be overridden by a user
- **tasks**: Set of tasks used by the role
- **handlers**: Handlers triggered used by the tasks of the role
- **files**: Static files used by the role
- **templates**: Templates based on jinja and used by the role

---

### Ansible variable precedence

See 👉 [Understanding variable precedence](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)

---

### Ansible collection

Install a role
```shell
ansible-galaxy install geerlingguy.java
```

Install a collection
```shell
ansible-galaxy collection install azure.azcollection
```

Or install multiple collections/roles with a requirements file
```shell
ansible-galaxy install -r requirements.yml
```

---

### Requirements.yml sample

```yml
roles:
  # Install a role from Ansible Galaxy.
  - name: geerlingguy.java
    version: "1.9.6" # note that ranges are not supported for roles

collections:
  # Install a role from Ansible Galaxy.
  - name: install azure.azcollection
    version: ">=1.14.0" # usage of ranges
  # Install a role from Ansible Galaxy.
  - name: awx.awx
    version: 21.11.0
    source: https://galaxy.ansible.com
  # Install a role from a git branch/tag/commit
  - name: https://github.com/organization/repo_name.git
    type: git
    version: develop
```

---

usefull links : 
- https://github.com/ansible-collections

---

### Usage of tags

```bash
Ansible-playbook deploy.yml --tags "patching"
Ansible-playbook deploy.yml --skip-tags "patching"
```

add tags to your task or plays
```yml
  tags:
    - patching
```

---

### Jinja Filters

all parameters inside [] are optionnal
operators can be (not limited to) : contains, ==, !=

- **sort** : sort([reverse=true], attribute='attribute_name')
- **select** : select('operator','value')
- **selectattr** :  selectattr('attribute_name','operator','value')
- **map** : map(attribute='attribute_name')

---

### Loop

```yml
files:
  - 'C:\File01.txt'
  - 'C:\File02.txt'
  - 'C:\File03.txt'
```

```yml
- name: Display Name of the file
  win_shell: "{{ item }}"
  loop: "{{ files }}"
```

---

### Loop with overriden iteration name

```yml
files:
  - 'C:\File01.txt'
  - 'C:\File02.txt'
  - 'C:\File03.txt'
```

```yml
- name: Display Name of the file
  win_shell: "{{ file_name }}"
  loop: "{{ files }}"
  loop_control:
    loop_var: file_name
```

---

### Handlers

[Teams connector](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook)

---

## 👌 The end