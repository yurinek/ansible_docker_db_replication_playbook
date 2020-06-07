# ansible_docker_db_replication_playbook

## Goals of the project

This project remotely deploys Postgres database replication over multiple docker containers with the automation help of Ansible. <br>
The containers can run eather on one or multiple docker hosts. <br> <br>

Some of the Ansible modules which are used: <br> <br>



ansible module   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; equivalent docker command   &nbsp;&nbsp;&nbsp; action performed <br>
----------------------------------------------------------------------------------------------------------------------------------------------------- <br>
docker_container  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;                      docker run    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pulls images from yurinek/postgres_master, yurinek/postgres_slave, runs containers <br>
----------------------------------------------------------------------------------------------------------------------------------------------------- <br>
docker_compose  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  docker-compose    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; builds the same images from dockerfiles, runs containers with compose <br>
----------------------------------------------------------------------------------------------------------------------------------------------------- <br>
docker_stack  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; docker stack deploy  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; pulls images from yurinek/postgres_master, yurinek/postgres_slave, runs swarm services <br> <br>


Following file structure of group vars illustrates how multiple stacks can be deployed across the same bunch of docker hosts.  <br>
Just one variable for docker_compose_project_name should be changed inside group_vars/*/vars/main.yml <br> <br>

group_vars/ <br>
| <br>
|--myapp_production <br>
| <br>
|--otherapp_production <br>


## How to install

To use the playbook, create the file ~/vault_pw.txt and place there your free to choose ansible-vault password (main password for encryption of sensible data). <br>

Place your target docker host names in the inventory files under inventory_production or inventory_staging directories. <br>

To encrypt the database password as string, follow the instructions in group_vars/*/vault/main.yml and place the encrypted value inside this file.  <br>
Alternatively the whole file can be encrypted. <br>
For testing purposes a plain text password can be placed instead. <br>

To install docker and related packages on target host
```hcl
ansible-playbook -i inventory_staging/myapp_staging docker_install.yml
```

## How to run

### Run in staging

For test, QA, staging or development following tasks can be used which deploy containers on single docker host
```hcl
ansible-playbook -i inventory_staging/myapp_staging docker_container.yml --vault-password-file=~/vault_pw.txt
```

For image builds, docker compose can be used
```hcl
ansible-playbook -i inventory_staging/myapp_staging docker_compose.yml --vault-password-file=~/vault_pw.txt
```

### Run in production

For production environment the next task can be used to spred container replication over multiple docker hosts using docker swarm stack
```hcl
ansible-playbook -i inventory_production/myapp_production docker_swarm_stack.yml --vault-password-file=~/vault_pw.txt
```

### Destroy

To destroy remote containers
```hcl
ansible-playbook -i inventory_staging/myapp_staging docker_container_destroy.yml --vault-password-file=~/vault_pw.txt
```

To destroy remote containers in compose 
```hcl
ansible-playbook -i inventory_staging/myapp_staging docker_compose_destroy.yml --vault-password-file=~/vault_pw.txt
```

To destroy remote containers in swarm stack
```hcl
ansible-playbook -i inventory_production/myapp_production docker_swarm_stack_destroy.yml --vault-password-file=~/vault_pw.txt
```

Destroy swarm completely
```hcl
ansible-playbook -i inventory_production/myapp_production docker_swarm_destroy.yml --vault-password-file=~/vault_pw.txt
```
