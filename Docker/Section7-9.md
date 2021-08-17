## Section 7-9 Swarm 
### Section 7 Swarm Intro
#### 7.1 Swarm Mode: Buil-In Orchestration 
- Problem
	- Automate container lifecycle
	- Easily scale out/in/up/down
	- Ensure containers are re-created if they fail
	- Replace containers without downtime -- blue/green deploy
	- Control/track where containers get started
	- Create cross-node virtual networks
	- Ensure only trusted servers run our containers
	- Store secrets, keys, passwords and only get them to the right container
	
- Swarm Mode: a server clustering solution built inside Docker
	- not enabled by default, new commands once enabled
		- Commands: docker swarm / node / service / stack / secret
		- (design goal) none of the swarm code should affect the existing docker deamon
		- ```docker info``` to check if it's activated
		
- Manager Node: Service -- Worker node: tasks, container
	- Manager Nodes (with local RAFT DB) and Worker Nodes (both are VM/physical host) communicating through control plane. Manger/worker can be demoted/promoted, so basically a manger is a worker with permissions to control the swarm.
	- In a Swarm the ```docker service``` replace the ```docker run```, and allow us to add extra features when we run it, e.g. replicas, i.e. tasks	-- each of the tasks will launch a container 
	- Manager node will decide when in the swarm to place the replicas 
		- API: Accepts command from client and creates service object
		- Orchestrator: Reconciliation loop for service objects and creates tasks
		- Allocator: Allocates IP addresses to tasks
		- Scheduler: Assigns nodes to tasks
		- Dispatcher: Checks in on workers
		- RAFT DB
	- Worker Node
		- Worker: Connects to dispatcher to check on assigned tasks
		- Executor: Executes the task assigned to workder node
		
- Other new concepts
	- a new network driver -- Overlay (8.1) -- other than host, bridge, null
	- 
#### 7.2 Creating a Swarm Node
- ```docker swarm init``` a **single-node** swarm
	- Automatically joined as a manager
	-  Lots of PKI and security automation
		- Root Signing Certificate created for our Swarm
		- Certificated is issued for *first* Manager node
		- Created *tokens* that other nodes can use to join
	- Background: Raft database created to store root CA, configs, and secrets
		- Raft is a protocal that ensures consistency across multiple nodes
		- All of the traffic is encrypted by default on disk
		- No need for additional key/value storage system or config db to hold orchestration/secrets, Swarm build it straight into Docker right into the deamon.
		- Replicated logs amongst Managers via mutual TLS in "control plane“
	- ```docker node ls```
		- There can only be one leader (node) at a time
	- join or leave ```docker swarm join```
	
- ```docker service```
	- replace ```docker run```, cuz we dont want to break the existing Docker run functionality
	- ```docker service create alpine ping 8.8.8.8```
		- return the service ID
		- ```--detach``` defualt changed to false after v17.12
	- ```docker service ls```
		- REPLICAS: <#is running>/<#specified>
		- ```docker service ps <NAME>``` show the tasks/containers for this service
	- **Scale up**: ```docker service update <ID/service name> -- replicas 3``` 
		- ```docker update``` update certain variables on our running container without kill and restart it (most of the options are related to limiting & controlling resource usage for the container)
		- fit the need for blue/green update
		- a shorter way is ```docker service scale``` command
	- Roll back possiblities, failure mitigation and so on
		- It make sure to recover from the failure (if ```docker container rm -f <name>.1.<ID>```, service will restart a new container, which is very different from docker run)
		- remove the service (```docker service rm <name>```), the orchestration process will clean up the containers service and the tasks behind it

#### 7.3 Creat a 3-Node Swarm Cluster: Host Options 
>resources e.g. firewall ports necessary for Swarm to work
1. Need 3 (OS) nodes, other ways:
	- play-with-docker.com (reset every 4 hours)
		- docker in docker
	- docker-machine + VirtualBox (require 8GB RAM)
		- docker-machine: automation tool for provisioning VMs locally and on the internet
		- ```docker-machine create node1```
		- access to machine: ```docker-machine ssh node1``` or ```docker-machine env node1```
	- Digital Ocean + Docker install 
		- Most like a production setup 
		- Ubuntu 16.04, 10$/month, SSH key => 3 droplets
		- ```ssh root@ip_address``` or setup a .ssh config with nodes info and call them by their name
		- install docker using script on get.docker.com
	- Other place u can get VMs 
		- e.g. Amazon, Azure, Google, DO, etc.
		- Install docker anywhere with get.docker.com
2. Init: specify an IP address to advertise the Swarm service on
	- ```docker swarm init --advertise-addr <IP address>```
	- e.g. public IP address for digital ocean host
3. Join the service 
   - Add as a worker ```docker swarm join \```
	   - To premote: ```docker node update --role manager <node2>``` from a manager node
	 - Add as a manager by default:
		 -  ```docker swarm join-token manager``` to get the token
		 - this token is part of the Swarm config and stored encrypted on disk
		 - token can be changed later
	 - ```docker node update --role``` to update role from a manager
 4. Create tasks
	 - ```docker service create --replicas 3 alpine ping 8.8.8.8```
	 - use ```docker service ls``` and ```docker node ps <node name>``` to check it, and ```docker service ps <service name>``` to see full list
	
### Section 8 Swarm Basic Features
#### 8.1 Scaling Out with Overlay Networking and Routing Mesh
- Scaling Out with Overlay Networking 
	- **Overlay**: A new networking driver
		- A swarm-wide bridge network where containers across hosts on the same virtual network can access each other like they're on a VLAN
		- only for intra swarm communication (contrainer-to-container traffic)
		- Optional IPSec (AES) encryption on network creation
	- Each service can be connected to multiple networks (e.g. Web servers on a front-end network, DB on back-end network, an API between on both networks)
	- Example with Drupal and how it listens on all 3 nodes: 
		- ```docker network create --driver overlay <mydrupal>```
		- ```docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=mypass postgres```
		- ```docker service ls```, ```docker service ps psql``` to see that the database is running on node1
		- ```docker container logs psql```
		- ```docker service create --name drupal --network mydrupal -p 80:80 drupal```
		- ```watch dokcer service ls```, ```docker service ps drupal``` to see that the drupal website is running on node2 
		- all nodes can visit web browser though
	
- Scaling Out with Routing Mesh
	- **Routing Mesh**: Global Traffic Router
		- Rountes ingress (incoming) packets for a Service to proper Task
		- Spans all nodes in Swarm using IPVS (kernel primitives from Linux Kernel) 
		- Load balances Swarm Services across their Tasks (load balancing on all the nodes and listen on all the nodes for traffic
	- ***Way 1:*** container-to-container in a Overlay networks (uses VIP)
		- when having two replicas of back-end DBs, instead of talking directly to their IPs, front-end actually talk to Virtual IP that Swarm puts in front of all services -- a private IP inside the virtual networking of Swarm and it ensures the load is distributed amonst all the tasks of the services
	- ***Way 2***: External traffic incoming to published ports (all nodes listen)
		- The external traffic can hit any of the noded in your swarm. Any of the worker node will have their published port open and listensing for that container's traffic and will reroute that traffic to the proper container based on its load balancing
		- each nodes has a built-in swarm load balancer on the externel IP address
		- benefits of VIP over DNS Round Robin: a lot times DNS caches *inside* our apps prevent us from properly distributing the load
	- [Example](https://docs.docker.com/engine/swarm/ingress/) with elasticsearch (multipal tasks) and how the load balancer working:
		- elasticsearch: search database that's accessible via a JSON web API
		- ```docker service create --name search --replicas 3 -p 9200:9200 elasticsearch:2```
		- ```docker service ps search```
		- ```curl localhost:9200``` (name is a randomly created feature)
		- we see 3 different names -- the VIP acting as a load balancer and distributing my load across the 3 tasks
	- Currently (17.03) a *stateless* load balancing
		- everytime hit service with multiple tasks, it's going to give u potentially different results
	- This LB is at OSI Layer 3 (TCP - IP and port) not Layer 4 (DNS)
		- *cannot run multiple websites on the same port on the same Swarm*
		- Solution 1: use Nginx or HAProxy: containers sit in front of routing mesh and act as a stateful LB/Layer 4 LB
		- Solution 2: the web interface Docker Data Center/UCP (L4 web proxy) from Docker Enterprise Edition
		
#### 8.2 Swarm Stacks: Production Grade Compose
- Stacks: A new layer of abstraction to Swarm
- Accept Compose files as their declarative definition for services, networks, and volumes (and secrets)
- New command: ```docker stack deploy``` 
	- To import the compose file and run the command that we put in it 
	- rather than docker service create
	- If you want to create things manually (networks, volumes), we can use **external** in our Compose file. (but usually it's easier to just let Stacks doing it all for u)
- New key in Compose file: ```deploy```
	- To specify things are specific to Swarm
	- but we cannot do ```build``` in Swarm, it's not designed to be on the production of Swarm. Stack will be the one to pull down images from Hub and deploy then with the deploy options
	- *on local machine*: Compose ignores deploy; *on Swarm*: Stack ignores build
- ```docker-compose``` CLI not needed on Swarm server
	- compose was not design to be a production tool, but a developer and sysadmin helper tool
- Commands (Min. **Version 3**)
	- ```docker stack deploy -c example-voting-app-stack.yml voteapp``` create all those objects in the scheduler, which will then go through the process of creating the network -> (secret ->) services -> tasks -> containers 
	- simple commands: ```docker stack deploy/ls/ps/rm/services``` (cuz all the functionality is in the Compose file)
 - Voting-app example: voting page, result page, visualizer 
 - Update Compose file
	 
#### 8.3 Swarm Secrets Storage: securely store your config vars
- Secrets:
	- What is a Secret: Usernames and passwords, TLS certificates and keys, SSH keys, and so on
	- Supports generic strings or binary content up to 500Kb in size
	- Doesn't require apps to be rewritten
	- Only stored on disk on Manager nodes 
		- and they are the only one who have the key to unlock/decrypt it
		- 1.13.0 Swarm Raft DB is encrypted on disk
	- Get the key down to the containers:
		- through the control plance/encrypted TLS network communication between managers and workers, which is secured using TLS and mutual PKI authentication
		- First store secrets in the Swarm DB using ```docker secrets```
		- Then assign to Service(s) using service command or a Stack file
		- Only containers in assigned Service(s) can see them
	- It looks like a file in container but is actually in-memory fs (file system)
		- under ```/run/secrets/<secret_name>``` or ```/run/secrets/  
	<secret_alias>```
		- like a "key-value" store: key is the name of the file and value is what's in it
		- we can have multiple names for the same key by setting up aliases
 - **Using secrets with Local docker-compose**
	-  Secrets is a Swarm-only thing (depends on Swarm, stored in Swarm DB)
	- However can (and only) work with file-based secrets (in stack) (not the external)
	- but is faking security: the local docker-compose actually mounts the secretes in a clear text file into the local container
	- ```docker-compose exec psql cat /run/secrets/psql_user``` will show the secret

- **Using Secrets in Swarm Services**
	- Creat a secret inside Swarm **Way 1**:
		- Give it a file ```docker secret creat psql_user psql_user.txt``` 
		- Notice that with this command we're storing the password on the hard-drive of the host 
		- Use the remote API from that local command line on ur machine then pass in the file instead
	- Creat a secret inside Swarm **Way 2**:
		- Pass a vaule at the command line ```echo "myDBpassWORD" | docker create secret create psql_pass - ``` 
		- ```-``` tells the command to read from the standard input (what we give with the echo)
		- Notice that this will be in the history of Bash file for root user
	-  ```docker secret ls``` or ```docker secret inspect psql_user```
	- ```docker service create --name psql --secret psql_user --secret psql_pass POSTGRES_PASSWORD_FILE=/run/secrets/psql/psql_pass -e POSTGRES_USER_FILE=/run/secrets/psql/psql_user  postgres```
		- first map the secret to the service (show up as RAM file system/tmpFS)
		- FILE path: by standerd will pull that file in and store it in the env variable 
	- See the actual secret (Only from the container and services that we assgined):
		- ```docker service ps psql``` to see which node it's running on (and name)
		-  ```docker exec -it <container name> bash``` to see the container
		- ```ls /run/secrets/``` and ```cat /run/secrets/psql_user``` to see the value
	- The service will re-deploy the container if removing the secret
		- ```docker service update --secret-rm```
		- "Secrets are part of the immutable design of the services" -- if anything in the container has to change for the service, the servie will stop the container and redeploy a new one (instead of just changing it).
		- how to update database password without recreating/redeploying (later section)
		
- **Using Secrets with Swarm Stacks** (Min. **Version 3.1**)
	- Simple template docker-compose.yml
	```
	services:
	  psql:
	    image: postgres
	    secrets:
	      - psql_user
	      - psql_password
	    environment:
	      POSTGRES_PASSWORD_FILE: /run/secrets/psql_password
	      POSTGRES_USER_FILE: /run/secrets/psql_user

	secrets:
	  psql_user:
	    file: ./psql_user.txt
	  psql_password:
	    file: ./psql_password.txt
	```
	- ```docker stack deploy -c docker-compose.yml mydb```
	- ```docker stack rm mydb``` will remove secret as well (v.s. docker service update --secret-rm)
	
#### 8.4 Assignments
- Creat Multi-Service App using Docker's Distributed Voting App
	- simulate a real-life App, has at least 5 roles, special function, web front-end, worker back-end, key value in a DB, etc. 
	- 1 volume, 2 networks, and 5 services needed
	- Creat the commands needed, spin up services, and test app
	- Everthing is using Docker Hub Images (suppose images are build elsewhere) so no data needed on Swarm 
	- ```docker service create --network``` tiwce for frontend and backend networks
	
- Create A Stack with Secrets and Deploy
	- Use compose-assignment-2
	- Rename image back to official drupal:8.2
	- Remove build, Add secret via **external**, use environment variable POSTGRES_PASSWORD_FILE, Add secret via cli ```echo "<pw>" | docker secret create psql-pw - ```
	- Copy compose into a new yml file on ur swarm node1
	
### Section 9 Swarm App Lifecycle
#### 9.1 Full App Lifecycle
0. Single set of Compose files
	- docker-compose.override.yml
	- docker-comose.test.yml
	- docker-compose.prod.yml
	- docker-compose.yml (base/core config)
	- Dockerfile
	- psql-fake-password.txt
1. Local development: local ```docker-compose up```
	- will first run the ```docker-compose.yml``` then the ```docker-compose.override.yml``` by default
	- [extends](https://docs.docker.com/compose/extends/#multiple-compose-files) can also override
2. Intergration testing (in CI environment): remote ```docker-compose up```
	- ```docker-compose -f docker-compose.yml -f docker-compose.test.yml up -d```
	- no bind mounts cuz we don't need drupal to save info in testing (CI) run
3. [Production](https://docs.docker.com/compose/production/): remote ```docker stack deploy```
   - ```docker-compose -f docker-compose.yml -f docker-compose.prod.yml config > output.yml``` push multiple config file into a single compose file equivalent which will be the one use officially in production to create/update our stack
   
#### 9.2 [Sevice Updates](https://docs.docker.com/engine/reference/commandline/service_update/)
- Rolling update pattern: 
	- roll through all replicas one at a time, update each ***container*** by ***replacing*** it with the new settings in the update command
	- Limits downtime 
	- rollback and *healthcheck*
	- scale & rollback subcommand for quicker access
	- **Stack updates**: rerun ```docker stack deploy -c file.yml <stackname>``` on edited YAML file
	
- Examples:
	- change (update) image for a service
		- ```docker service update --image myapp:1.2.1 <servicename>```
	- adding an env variable and remove a port (doing multiple thing in one command)
		- ```docker service update --env-add NODE_ENV=production --publish-rm 8080```
		- to change a port: ```docker service update --publish-rm 8088 --publish-add  9090:80 <servicename>```
	- change number of replicas of two services (Apply changes on multiple service at the same time)
		- ```docker service scale web=8 api=6```
- Tip for Rebalancing challenge
	- you can force an update of a service even without changing anything in that service
	- then it will reissue tasks, where they pick the least used nodes
- To cleanup
	- ```docker service rm <servicename>```
	
#### 9.3 Docker Healthchecks
- New feature in 1.12, together with Swarm Kit and Swarm Mode
	- supported in Dockerfile, Compose YAML, docker run, and Swarm Services
	- work with ```exec``` inside a container 
	- return exit 0 (OK) or exit 1 (Error)
	- 3 container states: starting (first 30s that hasn'r run a healthy check), healthy, unhealthy
	- but it's not an external monitoring replacement
- Healthcheck status 
	- shows up in ```docker container ls```
	- check last 5 healthchecks with ```docker container inspect```
	- docker run does nothing with healthchecks
		- docker run does not take action on an unhealthy container
		- we expect Swarm Services to take action -- replace the unhealthy container with a new task (possibly on a new host)
	- ```docker service update``` will consider the healthcheck as a part of the readiness
	- Healthcheck v.s. defualt
		- 3 stages: preparng (downloading) - starting - running 
		- ```docker service create --name p1 postgres``` *starting* and *running* are very quick
		- ```docker service create --name p2 --health-cmd="pg_isready -U postgres || exit" postgres``` *starting* will wait 30s for the first healthcheck run before *running* 
		
- Options for [healthcheck command](https://docs.docker.com/engine/reference/builder/#healthcheck)
	- --interval=DURATION (default: 30s）
	- --timeout=DURATION (default: 30s）
	- --start-period=DURATION (default: 30s)
		- gives a longer wait period than the first 30s 
		- from docker 17.09+ and used in version 3.4+
	- --retries=N (default: 3）
	- Basic command using default options: ```HEALTHCHECK curl -f http://localhost/ || false```
		- have to specify CMD when giving custom options
- Examples
	- adding the healthcheck in at runtime
		-  ```docker run --health-cmd="curl -f localhost:9200/_cluster/health || false" --health-interval=5s --health-retries=3 --health-timeout=2s --health-start-period=15s elasticsearch:2```
		- ```|| false``` (or ```||exit 1```）to make sure the shell will always return false 1 exit code since the healthcheck can only handle 0 and 1.
	- Custom options with the command ```HEALTHCHECK --timeout=2s --interval=3s --retries=3 CMD curl -f http://localhost/ || exit  1```
		- have to specify CMD if giving custom options
	- Static website running in Nginx, just test default URL
	```
	FROM nginx:1.13
	HEALTHCHECK --interval=30s --timeout=3s \
	 CMD curl -f http://localhost/ || exit 1
	```
	- PHP-FPM running behind Nginx, test the Nginx and FPM status URLs ([PHP resources](https://github.com/BretFisher/php-docker-good-defaults))
	- Use a PostgreSQL utility to test for ready state
		- ```pg_isready``` is a postgres command to test connection of the DB server
	```
	FROM postgres
	# specify real user with -U to prevent errors in log
	HEALTHCHECK --interval=5s --timeout=3s \
	CMD pg_isready -U postgres || exit 1
	```
	
	- Healthcheck in Compose/Stack Files
	
### Apendix: command cheatsheet 
