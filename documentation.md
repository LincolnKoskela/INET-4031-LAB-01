# Documentation

The purpose of this sheet is to truly understand what we did and why we are doing it. 
This doc will also highlight key terms and tools. 

## Lab 1 — Environment & Team Setup / Ansible

### Highlights

**Check the operating system**
```
cat /etc/os-release
```
**Check System Storage**
```
df -h
```

- Created Ansible directory
- Configured Ansible directory to target localhost instead of ssh'g to another machine in `ansible/inventory`. 
Inventory file tells ansible where the automation runs. The playbook tells ansible what the automation should do.
```
[local]
localhost ansible_connection=local
```

- Created `ansible/site.yml` and pasted the playbook that automates installing linux packages, 
ansible, and docker. It starts docker, configs user permissions etc. It preps our envirnmt for future labs.

**Verify** anisble has the correct `yml` syntax. From root directory run:

```
ansible-playbook -i ansible/inventory ansible/site.yml --syntax-check
```
Expected output: **playbook: ansible/site.yml**


- Ansible playbook: Basically a set of instructions that automatically installs the tools we need and sets up Docker, so we don't have to do everything by hand.
- Why it's useful: If we ever had to start over with a fresh server, we'd already have the setup instructions saved in GitHub instead of figuring everything out again.


Notes from playbook code highlighting idempotency.
```
- name: Ensure Docker is installed
  dnf:
    name:
      - docker-ce
      - docker-ce-cli
      - containerd.io
    state: present

```
- `state: present` tells the playbook if it exist, leave alone. If it doesn't exist, install. 
That's idempotency: Running the same instructions multiple times should produce the same end result without making unnecessary changes.

### Commits
```
git add ansible/
git commit -m "feat: initialize site.yml with baseline environment play"
git push origin main
```


### Validation checks

**Connected to a docker container**
```
whoami
hostname
```

**Repository Structure Is Correct**
```
ls -l
```

**Pass all checks**
```
chmod +x ./scripts/check-week1.sh
./scripts/check-week1.sh
```


### Key words

- **Ansible** is an open source automation tool used to manage IT projects. 
- **SSH** network protocol that establishes encrypted connections between computers for secure remote access. 
- **Idempotency** The ability to run automation repeatedly without making unnecessary changes when the desired configuration is already in place.
- **Docker daemon** is the core, background service running on a host operating system that acts as the brain of the entire docker engine.


### Summary

- In this lab, we set up the team environment, schedule role rotation for each sprint, and config'd ansible inventory file to run task on the 
same machine where it's invoked. We also created ansible playbook that automates installing linux packages, ansible, and docker.


### Lab 2 

