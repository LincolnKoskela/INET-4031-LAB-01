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

Created Ansible directory

Configured Ansible directory to target localhost instead of ssh'g to another machine in `ansible/inventory`. 
Inventory file tells ansible where the automation runs. The playbook tells ansible what the automation should do.
```
[local]
localhost ansible_connection=local
```

Created `ansible/site.yml` and pasted the playbook that automates installing linux packages, 
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


## Lab 2 - Building the 3 tier stack application

This lab we will be containerizing and operating the three tier stack application. 
We will be using the services of nginx, python flask API, and PostgreSQL database. 
We will be writing the Docker compose configurations that wire the three services together, 
handles start up ordering, data persistence, and manage credentials. We are also extending 
the ansible playbook to include the application stack, moving us closer to automation. 

### Walkthrough

**Part One: Defining the service**

`docker-compose.yml` is where the three services are being defines. The location is inside the week-2/ folder
right outside the app folder. This is because docker-comppose.yml is managing how the app runs and recieves traffic.

Created `docker-compose.yml`

Pasted postgreSQL into docker-comppose

Pasted Flask into docker-comppose

Created `week/2 nginx.conf`

Pasted code into nginx.conf so the reverse proxy knows where to send its traffic

Pasted nginx into docker-comppose

Organize the services in the order they depend on one another.
- Start-up: PostgreSQL → Flask → Nginx
Incoming HTTP traffic: User → Nginx → Flask → PostgreSQL (when database access is needed)

**Networking and Persistence**

Added network and volume to bottom of docker-compose to create persistence and networking.
- network creates private network so the services can communicate on their own private network
- volume creates place to store data so it doesn't disappear when containers are removed

cp .env file and inside change port # to 8084

Start the stack application from week-2
`docker compose up -d`

Check status of the containers
`docker compose ps`

Tested data persistence 
- Created test incident command response

Restart PostgreSQL Containers 
`docker compose restart db`

Start back up

Containers are on the VM. Check by running `docker compose ps`. The three containers on the VM are `nginx, flask, and PostgreSQL`.
Docker compose tells the Docker how to create and run those environments. The three containers talk through 
`app-network` (docker network).

**Environment configuration and Ansible**

Add `app-stack` to `ansible/site.yml`

Create directory
`ansible/roles/app-stack/tasks`
```
touch main.yml
```

We're creating a folder called app-stack that holds instructions for Ansible to automatically start our application.
Why? Instead of manually starting everything each time, we're adding these steps to Ansible so it can eventually rebuild our application setup for us.
Think of it this way: Docker Compose starts the containers. Ansible automates the process of starting Docker Compose.

Next we connect app-stack to the ansible playbook

**Basically, we're telling Ansible: After setting up the Linux environment and Docker, run our app-stack instructions to start PostgreSQL, Flask, and Nginx.**

We're combining our server setup and application startup into one automated process, making it easier to rebuild everything later.

Pasted inside ansible playbook
```
- name: Deploy application stack
  hosts: localhost
  connection: local
  become: yes

  roles:
    - app-stack
```


### Key words

- Reverse Proxy: an intermediary; routes all incoming traffic request to their correct destination. 
- API: Application Programming Interface: a set of rules and protocols that allows different software applications to 
communicate and exchange data with each other.
- .yml file: a human-readable data serialization format. Commonly used for software config files.

- PostgreSQL Database: the database. Stores incident records. Must finish initializing before Flask can connect.
- Nginx: the API. Reads and writes incidents. Must be healthy before Nginx routes traffic to it.
- Flask: the reverse proxy. Handles incoming requests and forwards them to Flask. Comes up last.


### Summary

In lab 1 we laid the foundation for the ansible playbook, told ansible where to run it's automations and
gave it the playbook to tell ansible what automations to do. It ensures the baseline environment is configured. 
In lab 2 we expanded on our project by first defining the three tier stack application services: nginx, flask, and postgreSQL. 
These definitions went inside the `docker-compose.yml` file. Then we added network and volume features to the `docker-compose.yml`. 
This enables the containers to talk to each other on their own private network while also provided volume is keeping the database records
safe. Lastly, we expanded ansible by making a folder called `app-stack` that will hold instructions to automatically start our application.
We're adding steps to ansible so eventually it can rebuild our application setup for us. Then we connect the `app-stack` to the playbook 
in `site.yml`.

### Validate

```
chmod +x scripts/check-week2.sh
./scripts/check-week2.sh
```


### Troubleshooting / Problems and Errors

