# Setting up Docker Swarm

### 1. Initialize the swarm

```bash
docker swarm init --advertise-addr <ip_address>
```  
**Note1:** if using <ip_address>:<port>, the used port must be used by other nodes to join the swarm.  
**Note2:** the host initializing the swarm will automaticly become a manager.  

The output will contain the `docker swarm join` command that we need to run on  
other nodes in order to join the swarm:  

**Note:** an odd number of manager nodes is recommended by Docker to ensure stability and reliability


```
Swarm initialized: current node (mcxvetwlfc3enzmwebu613tab) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-11yrq8zwk5huvqsf6rhpux5sitrgiwd2oeq36szhyxsr7loka0-d0giftswzhz2fsb0e6gcnakrx 192.168.1.172:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```  
### 2. Join nodes  
Following with the example above, we would need to run:  
```bash
docker swarm join --token SWMTKN-1-11yrq8zwk5huvqsf6rhpux5sitrgiwd2oeq36szhyxsr7loka0-d0giftswzhz2fsb0e6gcnakrx 192.168.1.172:2377
```  
Within the host in order to join it to the swarm.  

⚠️ **Important:** The clocks on all hosts in a Docker swarm must be synchronized. A CA certificate's validity is based on its creation time, so if a worker's clock is ahead of the manager's, the certificate might appear invalid, causing the join command to fail.  

### 3. Add workflows  
Workflows that the swarm managers will manage are `services` and `stacks`:

**Service** - definition of a task you want to run in the swarm. 
```bash
docker service create --name my-web-app --replicas 3 -p 8080:80 nginx
``` 
**Stack** - group of multiple services, along with their associated networks and volumes, into a single application, they use .yaml files to describe the services that form the application
```bash
docker stack deploy -c my-compose-file.yml my-app-stack
```  
Basicly, a **Stack** is an application composed of one or more **Services**.  

Example of a .yaml file
```
version: '3.8'

services:
  webserver:
    image: httpd:latest
    ports:
      - "8080:80"
    networks:
      - custom_network
    deploy:
      replicas: 2
      restart_policy:
        condition: any

  db:
    image: mariadb
    networks:
      - custom_network
    environment:
      MARIADB_ROOT_PASSWORD_FILE: /run/secrets/mariadb_passwd
    secrets:
      - mariadb_passwd
    deploy:
      replicas: 2
      restart_policy:
        condition: any

networks:
  custom_network:

secrets:
  mariadb_passwd:
    external: true
```
#### Common Swarm commands and opts  
| Command | Subcommand | Description | Common Options |
| ----- | ----- | ----- | ----- |
| `docker swarm` | `init` | Initializes a new swarm, making the current machine the first manager node. | `--advertise-addr <IP>` (Specifies the IP address for the manager to advertise to other nodes.) |
| | `join` | Joins a machine to an existing swarm as a worker or manager. | `--token <TOKEN>` (Required token for joining), `--advertise-addr <IP>` (Required for manager nodes) |
| | `join-token` | Manages join tokens for worker and manager nodes. | `worker` (Displays the worker join token), `manager` (Displays the manager join token) |
| | `leave` | Leaves a swarm. | `--force` (Forces the node to leave even if it's a manager.) |
| `docker stack` | `deploy` | Deploys a new stack or updates an existing one from a Compose file. | `-c` (Specifies the Compose file path) |
| | `ls` | Lists all stacks currently deployed in the swarm. | |
| | `ps` | Lists the tasks (containers) for one or more stacks. | `--filter` (Filter by service, node, etc.), `--no-trunc` (Don't truncate output) |
| | `rm` | Removes a deployed stack and all of its services. | |
| `docker service` | `create` | Creates a new service. | `--name`, `--replicas`, `-p` (publish port) |
| | `ls` | Lists all services in the swarm. | |
| | `ps` | Lists the tasks (containers) for one or more services. | `--filter` (Filter by node, desired state, etc.), `--no-trunc` (Don't truncate output) |
| | `scale` | Scales the number of replica tasks for a service. | `--detach` (Exits immediately) |
| | `update` | Updates a service with new settings or image. | `--image`, `--replicas`, `--detach` |
| | `rm` | Removes one or more services. | |
| `docker node` | `ls` | Lists all nodes in the swarm. | |
| | `inspect` | Displays detailed information about a node. | `--pretty` (Formats output for readability) |
| | `promote` | Promotes a worker node to a manager. | |
| | `demote` | Demotes a manager node to a worker. | |
| | `rm` | Removes a node from the swarm. | `--force` (Forces the removal of an unreachable node) |
| | `update` | Updates a node's availability or role. | `--availability <active\|pause\|drain>`, `--role <worker\|manager>` |