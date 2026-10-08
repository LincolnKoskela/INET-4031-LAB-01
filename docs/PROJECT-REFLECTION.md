# System Admin Infrastructure Project Reflection

## Week 1 

- Assigned team roles and schedule rotation on the roles
- Set up our version control environment by creating a team repository, pulling in the starter files, and pushing the changes to the team repo. Once pushed, team members pulled the changes to their local environments.
- Created Team Charter markdown
- Verified baseline state of the container
- Created ansible/site.yml ansible/inventory and configured Ansible inventory file to target localhost, telling ansible to run task on the same machine where it is invoked.
- Created initial Ansible playbook (will grow throughout project).
- Conducted storage checks using `df -h`

## Week 2 
**Building the Three-Tier Stack Application**

### Define the Services
- Created `docker-compose.yml` 
- Added database service to `docker-compose.yml` | Stores the data
- Added Flask service to `docker-compose.yml` below database | The python application
- Created `nginx.conf` to let nginx know where to send traffic
- Added nginx service to `docker-compose.yml`

### Network and Persistence
- Added `docker-compose.yml` network service to allow containers to talk to each other
- Added volumes service to `docker-compose.yml` to provide persistent storage for the containers | this creates storage outside the containers so when the containers are disposed, data remains
- Created `.env` file which is a settings file
