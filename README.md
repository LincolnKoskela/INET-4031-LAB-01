# INET-4031 TEAM 4 (The Technologist Masters)

## Lab 1 — Environment Setup & Ansible

**Objective:** Establish our development environment and create an Ansible playbook to automate the installation of essential tools and Docker.

### Environment

- OS: RHEL 9.8
- Tools: Docker 29.9.0, Ansible Core 2.14.18, Git
- Host: Shared university VM

### Ansible Configuration

- `ansible/inventory` — Targets the local VM without SSH.
- `ansible/site.yml` — Defines tasks for installing Linux packages, configuring Docker, and managing user permissions.
- `ansible/roles/` — Directory for reusable automation components.

### Validation

Check the playbook's syntax:

```bash
ansible-playbook -i ansible/inventory ansible/site.yml --syntax-check
```

Expected output:

```text
playbook: ansible/site.yml
```

### 1 minute guide

**Clone:**

```
git clone https://github.com/INET4031-Labs/inet4031-week01.git temp-week01
rm -rf temp-week01/.git
cp -r temp-week01/. .
rm -rf temp-week01
git add .
git commit -m "chore: pull in week-1 starter files"
git push
```

Everyone pull
```
git pull
```

Create ansible directory
```
mkdir -p ansible/roles
touch ansible/site.yml ansible/inventory
```

Inside ansible inventory
```
[local]
localhost ansible_connection=local
```

Inside `ansible/site.yml` paste playbook:
```
---
- name: Baseline environment setup
  hosts: localhost
  connection: local
  become: yes
  tasks:
    - name: Update dnf cache
      dnf:
        update_cache: yes

    - name: Ensure baseline packages are installed
      dnf:
        name:
          - curl
          - git
          - vim
          - python3
          - python3-pip
        state: present

    - name: Ensure EPEL repository is enabled
      dnf:
        name: https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
        state: present
        disable_gpg_check: yes

    - name: Ensure Ansible is installed
      dnf:
        name: ansible-core
        state: present

    - name: Import Docker CE GPG key
      ansible.builtin.rpm_key:
        key: https://download.docker.com/linux/rhel/gpg
        state: present

    - name: Add Docker CE repository
      ansible.builtin.get_url:
        url: https://download.docker.com/linux/rhel/docker-ce.repo
        dest: /etc/yum.repos.d/docker-ce.repo
        mode: '0644'

    - name: Remove podman-docker (conflicts with docker-ce's docker binary)
      dnf:
        name:
          - podman-docker
          - podman
        state: absent

    - name: Ensure Docker is installed
      dnf:
        name:
          - docker-ce
          - docker-ce-cli
          - containerd.io
          - docker-compose-plugin
        state: present
        update_cache: yes

    - name: Ensure Docker service is enabled and running
      ansible.builtin.service:
        name: docker
        state: started
        enabled: yes

    - name: Add invoking user to the docker group
      ansible.builtin.user:
        name: "{{ lookup('env', 'SUDO_USER') or lookup('env', 'USER') }}"
        groups: docker
        append: yes

    - name: Check current Docker CLI context
      ansible.builtin.command: docker context show
      become: yes
      become_user: "{{ lookup('env', 'SUDO_USER') or lookup('env', 'USER') }}"
      register: docker_current_context
      changed_when: false
      failed_when: false

    - name: Reset Docker CLI context to default (in case a stale podman context was left behind)
      ansible.builtin.command: docker context use default
      become: yes
      become_user: "{{ lookup('env', 'SUDO_USER') or lookup('env', 'USER') }}"
      changed_when: docker_current_context.stdout | default('') != 'default'
```

Validate paste from root directory:
```
ansible-playbook -i ansible/inventory ansible/site.yml --syntax-check
```
**Expected Output:** **playbook: ansible/site.yml**

Validate your work 
```
chmod +x ./scripts/check-week1.sh
./scripts/check-week1.sh
```




## Team Members

| Name | Email | GitHub Username | Current Role |
| ---- | ----- | --------------- | ------------ | 
| **Abdikarim** | Abdibmoha32@gmail.com | Abdibmoha32-star | Developer |
| **Mohamed** | mahad083@umn.edu | mahad083 | Sys Admin | 
| **Franklin** | franklin1jbradley@gmail.com | FranklinBrad | QA | 
| **Lincoln** | koske113@umn.edu | lincolnkoskela | Scrum Master | 
| **Kahiye** | kahin012@umn.edu | kahin012-cyber | Developer |
| **Mahir** | ismai164@umn.edu | -- | Developer |

## Links
- [TEAM Charter](TEAM_CHARTER.md)

## Documentation files
- README.md: This will be our main demo guide, architecture diagrams, set up requirements, exact commands, explanation verifications, and the 10 minute sequence.
- runbook.md: This will be our troubleshooting log going throughout the lab. This will be a resource to draw upon during the 10 minute demo.
- documentation.md: This will be our team notes, personel understanding file to study and learn together. 
- adr.md: this file will be our design decision record

## Google Docs

| Sprint # | Week | Doc | Sections | 
| -------- | ---- | --- | ---------| 
| Sprint 1| Week 1 | [Sprint 1](https://docs.google.com/document/d/18M1M-BXXiRc-_oAEJzN-DMDf3LAYOGaXO1BtKQHledY/edit?tab=t.0) | Lab 1 part 1 & 2 |
| Sprint 1| Week 2 | [Sprint 1](https://docs.google.com/document/d/15OqQ9wg6eAtSuQJsSK4ycqgn7YZe2qml4hF9kY_ykks/edit?tab=t.0) | Lab 1 part 3 && Lab 2 part 1 & 2 | 
| Sprint 1 | Week 3 | [Sprint 1](https://docs.google.com/document/d/1zbn7Vs36repbvgo8TzE_0WQ4cSWPTJu2vE7PpyH3DAo/edit?tab=t.0) | Lab 2 part 3 |
| Sprint 2 | Week 4 | [Sprint 2](https://docs.google.com/document/d/1CM4l-zpCr22cF59WCulq8-Z2I5JXKqWv3ZUrqZKwRf4/edit?tab=t.0) | Lab 3 | 
| Sprint 2 | Week 4 | [Sprint 2](https://docs.google.com/document/d/1X3Cch7b5BpypOXtlK-KE6l3pMkvsO5NedIFNNhzOf1s/edit?tab=t.0) | Lab 4 | 
| Sprint 3 | Week 5 | [Sprint 3](https://docs.google.com/document/d/1T7WB-vNLd0U6nvW2LPKlDWJchTMlT8eMmWoQWH7TV2E/edit?tab=t.0#heading=h.os0x1xy1p7cg)| Lab 5 | 


