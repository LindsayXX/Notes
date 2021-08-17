## Section 3 Containers
>Check Docker Install & Config
>Create a Nginx (web server) container
>Common container management commands
>Docker Networking Basics
### 3.1 Intro
#### 3.1.1 Container and other terms
- Image VS. Container:
	- Image: the application we want to run
	- Container: an instance of that image running as a process
	- we can have many containers running off the same image
	- Image are kernel specific
- Container VS. VM:
	- Container is just a process (no need for a VM in linux)
- Container
	-  We can only run things in the container that already existed in its image when you started it (or sth add later through the ```exec``` command)
	- Different container cannot run in the same port if you are exposing them at the host
- Distribution based (Linux) images
	- e.g. Ubuntu (129MB)
	- e.g. CentOS
	- e.g. Alphine: small security-focused distribution (~5MB) (doesn't have bash, but have sh)
	- They are not full OS's but just the base utilities 
	- we all tend to start our Dockerfile's from those distributions, otherwise empty image/make from scratch means nothing to start from (no package manager/apt, no shell to get a prompt inside it, etc)
- Docker Deamon
	- a persistent background process that manages the containers on a single host.
- Registry
	- A container **image registry** is a service that stores container images, and is hosted either by a third-party or as a public/private registry such as Docker Hub, Quay, and so on.
	- A **Docker registry** is a storage and distribution system for named Docker images. The same image might have multiple different versions, identified by their tags.
	
#### 3.1.2 Docker Networks basic
- Traffic flow and firewalls -- Packets moving around virtual networks
	- *Start*: container connected to a **private vitural network** "bridge/docker 0" (by default)
		- ```--network bridge```: default docker virtual network which bridges through the *NAT firewall* to the *physical network* that your Host is connected to.
	- Each virtual network routes through NAT firewall on host IP (physical network)
	- All containers on same virtual network (inside our host) can talk to each other without opening their ports up (using ```-p```) to the rest of your physical network
		- Put related containers on the same network
		- Put unrelated containers on different network
		- you cannot listen on more than one port for multiple containers
	
- "Batteries included, but removable"  
	- Create multiple virtual networks
	- Attach container to more than one virtual network (or none)
		- Default: ```--network bridge``` allow containers to communicate with each other when running on the same docker host
	 	- ```--network host```: attach container directly to the host interface -- gains performance by skipping virtual networks but sacrifices security of container model
	 	- ```--network none```: having an interface on your computer that is not attached to anything -- removes eth0 and only leaves you with localhost interface in container
	- Skip virtual networks and use host IP (-net=host)
	- Use different Docker network drivers to gain new abilities
	- multi-host private networking, sub-interfaces, etc..

- Default Security
	- Create your apps so frontend/backend sit on same Docker network
	- Their inter-communication never leaves host (only communicate with outside through port)
	- All externally exposed ports closed by default, you must manually expose via ```-p```, which is better default security!
	- This get even better later with Swarm (multi-host networking) and Overlay networks. 

- DNS instead of IP's
	- Forget IP's: you can't talk with each other based on ip address cuz containers change too dynimac and complicated
	- DNS is the standard for inter-communication between containers on same host or cross-host
	- Docker daemon has a built-in DNS server that containers use by default
	- DNS default names: Docker defaults the hostname to the container's name, but you can also set aliases
	- Always use custom networks
		- the default bridge network does not have build-in DNS, 
		- ```--link list```
	- How container find each other? by hostname/container name?
	- this get way easier with Docker Compose in future Section
	
### 3.2 Commands 
#### 3.2.1 From a 'outside' view
- Check Docker Install & Config
	- Check version: ```docker version```
	- Configuration: ```docker info```
	- Command line structure: 
		- New: ```docker <command> <sub-command> (options)```
		- Old: ```docker <command> (options)```
- Run/stop/remove containers (*Nginx web server* as example)
	- **Run**: ```docker container run --publish 8888:80 nginx```
		1. Look for that image locally in image cache
		2. If doesn't find, look remote image repository (defaults to Docker Hub)
		3. Downloaded lastest version (if not specified) image 'nginx'
		4. **Started** a **new** container from that image 
		5. Opened port 8888 on the host IP (left--localhost, "**binding**")
		6. Routes that traffic to the container IP, port 80
	- Run in background: ```docker container run --publish 8888:80 --detach --name webhost nginx```
		- ```--detach``` /```-d``` to run in background, or ```ctrl-c``` on Windows?
		- name is unique
		- return an ID ```690303xxxxx```
		- ```--env```/```-e```  pass environment variables. E.g. ```docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true```
	- **stop**: ```docker container stop <container ids/names>``` 
	- **remove**: ```docker container rm 690 63f 0de``` 
		- accept multiple names/ID's
		- cannot remove the running ones
		- ```docker container rm -f 0de``` force remove the running one

- CLI Process Monitoring (see logs and other)
	- ```docker container ls``` / ```docker ps``` check which one is running
		- ```docker container ls -a``` / ```docker ps -a```  check which has started (including stopped ones)
	- ```docker container log```
	- ```docker top webhost``` 
		- process list in *one* container
	- ```docker container inspect <name>``` 
		- metadata of *one* container (startup config, volumes, net working, etc)
	- ```docker container stats```
		- live performance stats for all containers
		
#### 3.2.2 Getting a Shell inside (connect a Shell from outside)
- ```docker container run -it --name proxy nginx bash```
	- start new container interactively
	- using Shell to do things interactively (as doing SSH to a server)
	- ```exit``` to stop
- ```docker container exec -it```
	- run additional command in *existing* (running) container
	- ```exit``` won't stop the container
	
#### 3.2.3 Network
- Private and public
	- ```docker container run --publish``` / ```-p```
		- e.g. ```docker container run -p 8888:80 --name webhost -d nginx```
		- publishing ports is always in HOST:CONTAINER format
		- exposing ports on yout host to the physical network
	- ```docker container inspect --format```
	 	- e.g. ```docker container inspect --format '{{.NetworkSettings.IPAddress}}' webhost```
		- ```--format```: a common option for formatin the output of commands using "Go templates"
		- windows use double quotes rather than single quotes
	- Quick port check: ```docker container port <container>```
		- look at the ports exposed for paticular containers
		
- CLI Management of Virtual Networks
	- Show networks: ```docker network ls```
		- ```bridge```
		- ```host```
		- ```none```
	- Inspect a network: ```docker container inspect```
	- Creat a network: ```docker network create```
		- Spawns a new virtual network for you to attach containers to 
		- creat your own network ```docker network create <name>```
		- Build-in or 3rd party extensions that give your virtual network features: ```docker network create --driver```
	- Detach/Attach a network from/to container: ```docker network disconnect/connect```
		- dynamically removes/creates a NIC from/in a container on an specific/existing virtual network
		
### 3.3 Assignment
- Manage multiple containers
	- Run a nginx, a mysql, and a httpd(apache) server on different port
	- use ```--env``` / ```-e``` to pass variables
	- use ```docker container logs``` on mysql to find the random password it created on startup
	- clean it all up with ```stop``` and ```rm```
	```
	docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes MYSQL_RANDOM_ROOT_PASSWORD
	docker container logs db
	docker container run -d --name webserver -p 8080:80 httpd
	docker ps
	docker container run -d --name proxy -p 80:80 nginx
	docker ps
	docker container ls
	docker container stop TAB COMPLETION
	docker ps -a
	docker container ls -a
	docker container rm
	docker ps -a
	docker image ls
	```
- CLI App Testing (use two different terminal windows)
	- ```docker container run --rm```
	- ubuntu: 
	```
	docker ps -a
	docker container run --rm -it ubuntu:14.04 bash
	apt-get update && apt-get install -y curl
	curl --version
	exit
	```
	- centos:
	```
	docker container run --rm -it centos:7 bash
	yum update curl
	curl --version
	exit
	```
- DNS Round Robin Test
	- have mulitple different hosts that have DNS aliases respond to the same DNS name/address (e.g. google.com has more than one servers)
	- ```elasticsearch:2``` image
	- ```--network-alias search``` (or ```--net-alias```) when creating to give them an additional DNS name to respond to
	- run ```alpine nslookup search.``` with ```--net``` to see the two containers list for the same DNS name
	```
	docker network create dude
	docker container run -d --net dude --net-alias search elasticsearch:2
	docker container ls
	docker container run --rm -- net dude alpine nslookup search
	docker container run --rm --net dude centos curl -s search:9200
	docker container ls
	docker container rm -f TAB COMPLETION
	```

## Section 4 Container Images
>Images -- Building blocks of containers
>What's in an image and what isn't
>Using Docker Hub registry 
>Managing our local image cache
>Building our own images

### 4.1 Image
- Definition:
	- App binaries and dependencies 
	- Metadata about the image data and how to run the image
	- Offical definition: an ordered collection of root filesystem changes and the corresponding execution parameters for use within a container runtime

- Docker hub registry images
	- one image can have multiple tags (tag point to image ID)
	- official repositories: live at the "root namespace" of the registry so they don't need account name in front of repo name
	
- Image layers
	- ```docker image history``` show layers of changes made in image
	- never storing the entire stack of image layers more than once
	- each layer is uniquely identified and *only stored once* on a host
	- copy on write
	- ```docker image inspect``` returns JSON metadata about the image
	- A container is just a single read/write layer on top of image

- Image Tagging and Pushing (to Docker Hub)
	- ```docker image tag --help```
	- proper format: 	
		- ACCOUNT_ID(ORGINIZATION_ID)/IMAGE_NAME
		- official repositories
	- tag point to image ID
			- you can have many tags for the same image ID 
			- retag does change image ID
			- by retagging image we can create out own image: ```docker image tag SOUCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]```
			- "latest tag" is the default tag that image owners assign it to the newest stable version
	- ```docker image push``` uploads changed layers to a image registry (default is Hub) 
		- login first ```docker login <server>```
		- always logout from shared machines or servers when done ```docker logout``` to remove the saved local config
		- If you want to creat a *private* Docker Hub image, create the repository first before uploading
		
### 4.2 Building Images
- Dockerfile (.D) -- instruction on how to build your image 
	- ```docker build -f some-dockerfile``` specify a docker build file other than the default 'Dockerfile'
	- 5 basic (must) **stanzas** (Command): 
		- ```FROM debian:jessie``` start with a minimal Linux distribution 
		- ```ENV NGINX_VERSION 1.11.10-1-1-jessie``` set evniroment variables (they work everywhere, on every OS and config)
		- (optional) ```RUN``` commands to run at shell inside contianer at build time, e.g. add package reop and install
		- ```RUN``` forward request and error logs to docker log collector (stdout & stderr)
		- (optional) ```EXPOSE 80 443``` port to expose on docker virtual network, still need to use ```-p``` or ```-P``` to open/forward these ports on host
		- ```CMD``` run this command when container is launched, only one CMD allowed.
	- Each of these stanzas are *one* actual layers in the docker image. So the order of them matters.

- Running Docker Builds
	- ```docker image build -t <name> .``` 
	- steps in the build are cached -- it will not rerun if it's the same (hash keep in the build cache)  -- why docker is so fast
	- sanity: keep the things at the top that change the least
	
- Extending Official Images
	- ```FROM nginx:latest```
		 - highly recommend you always pin versions for anything beyond dev/learn
	- ```WORKDIR /usr/share/nginx/html``` change working directory (to root of nginx webhost)
		- using WORKDIR is preferred to using 'RUN cd /some/path'
	- ```COPY index.html index.html``` cope your source code from your local machine/build servers to your container images
	- no need to specify EXPOSE or CMD because they're in my FROM

- Use "prune" to clean up images, volume, build cache, and containers
	- ```docker image prune``` to clean up just "dangling" image
	- ```docker system prune``` will clean up EVERTHING
	- ```docker image prune -a``` remove ALL images you're not using
		- use ```docker system df``` to see space(docker disk) usage
	- ```docker-machine rm default``` then ```docker-machine create``` to delete Docker Toolbox Linux VM and recreate the toolbox default VM
	
### 4.3 Assignment
- Build your own image -- take existing Node.js app and Dockerize it
	- Use the Alpine version of the official 'node' 6.x image
	1. Make Dockerfile under directory ```dockerfile-assignment-1```
		- ```npm install && npm cache clean --force```: usually put this two line together, combined with a "&&" (execute if the first one successed) or ";" (execute the second one regardless)
		- [CMD format](https://docs.docker.com/engine/reference/builder/#cmd)
	2. Build it
	3. Test it (http://localhost:80)
		- use another one (e.g. 8080) if it's occupied 
	4. Push it 
	5. (rm it from local cache)
	6. Run it (again from Hub)

## Section 5 Container Lifetime & Persistent Data: Volumes
> Defining  the problem of persistent data
> Key concepts with containers: immutable, ephemeral (unchanging, temperally
> Learning and using Data Volume
> Learning and using Bind Mounts
### 5.1 Container lifetime and Persistent data
- Container Lifetime
	- containers are usually immutable and ephemeral
	- "immutable infrastructure": we don't change it when it's running, only re-deploy a whole new container 
	- "separation of concerns": the containers shouldn't contain your unique data mixed in with the application binaries. 
		- "presistent data": our unique data is still preserved when recycle the containers
		- Two solutions: ***Volumes*** and ***Bind Mounts***
		
- **Volumes** (mysql as example)
	- a configuration option for a container that creates a special location outside of that container's union file system to store unique data -- it allow us to attach it to whatever container we want
	- "The databases outlive the executable" (will not be deleted when deleting container, need manual delation)
	- *Create* Volume
		- Dockerfile stanzas: ```VOLUME /var/lib/mysql```
	- *Named* Volume
		- use custom drivers and labels: ```docker volume create``` before "docker run"
		-  ```docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql```
	- *Delete* Volume:
		- ```docker volume prune``` to cleanup unused volumes 
	- The actual location of the data (on Mac and Win it's in a VM)
		- ```docker volume inspect <VOLUME NAME>``` -- "Mountpoint"
		- ```docker container inspect <CONTAINER NAME>``` -- "Mounts"-"Source" (location of the container "Mounts"-"Destination”)
		- ```docker image inspect <IMAGE NAME>``` -- “Config"-"Volumes"
		
- Bind Mounting
	- Sharing and mounting files/directories between a host and a container
	- mapping a local host file path/directory path to the container, i.e. two locations pointing to the same file(s)
	- Again, skips UFS, and *host* files overwrite any in container
	- Can't use in Dockerfile, must be a ```container run```
		- ```... run -v /Users/username/stuff:/path/container``` (mac/linux)
		- ```... run -v //c/Users/username/stuff:/path/container``` (windows)
	- Bind mount doesn't require a volume to work
	- Allow you to attach an existing directory on your host (which you can rewrite) to a directory inside of a container -- Works best for local development & testing
	
### 5.2 Assignment
- Database upgrade with Named Volumes
	- Create a postgres container with named volume psql-data using version 9.6.1
		- Use Docker Hub to learn VOLUME path and versions needed to run it 
		- ```docker run -d --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -v psql:/var/lib/postgresql/data postgres:9.6.1```
	- Check logs, stop container
		- ```docker container logs -f some-postgres```
	- Create a new postgres container with same named volume using version 9.6.2
	- Check logs to validate
		- ```docker container logs <contianer ID>```
		- Should only has a couple of lines of startup logs without the initial startup (4/5 log entries)
		
- Edit code Running in Containers with Bind mounts (source code fom directory ```bindmount-sample-1```)
	- Use a Jekyll "Static Site Generator" to start a local web server
	- We edit files with editor on our host using native tools
	- Container detects changes with host files and updates web server
		- ```docker run -p 80:4000 -v ${pwd}:/site bretfisher/jekyll-serve```
		- Refresh browser to see changes
		- Change the file in _posts\ and refresh browser to see changes
- ```$(pwd)``` for linux, ```${pwd}``` for window

## Section 6 Docker Compose
### 6.1 Docker Compose and docker-compse.yml File
- Why?
	- configure relationships between containers
	- save our docker container run settings in easy-to-read file
	- create one-linear developer environment startups
- Comprised of 2 separate but related things
- Two ways
	- **YAML**-formatted file that describes our solutions options for:
		- containers
		- networks
		- volumes
	- A CLI tool ```docker-compose``` used for local dev/test automation with these YAML files
	
- docker-compse.yml
	- Compose YAML format has it's own versions: 1, 2, 2,1, 3.1
		- Default is v1
		- Recommend minimum v2
		- v2.x is better for local docker-compose use
		- v3.x is better for use in server clusters (Swarm and Kubernetes)
	- YAML file can be used with ```docker-compose``` command for local docker automation or..
	- With ```docker``` directly in production with Swarm (as of v1.13)
	- ```docker-compose --help```
	- ```docker-compose.yml``` is default filename, but any can be used with ```docker-compose -f```
	- Template:
	```
	version: '3.1' 
	services:  # containers. same as docker run
	  servicename: # a friendly name. this is also DNS name inside network, same as --name in docker run
	    image: # Optional if you use build:
	    command: # Optional, replace/overwirte the default CMD specified by the image (all the things we do from the docker run command)
	    environment: # Optional, same as -e in docker run
	    volumes: # Optional, same as -v in docker run
	  servicename2:
	  
	volumes: # Optional, same as docker volume create
	networks: # Optional, same as docker network create (is created at runtime by default even if not specified)
	```
	-  examples

### 6.2 Basic docker-compose command
*docker-compose CLI tools comes with Docker for Windows/Mac, but is actually separate from the Docker tool (need separete download for Linux)
- Not a production-grade tool but ideal for local development and test
- 2 most common commands:
	- ```docker-compose up``` # setup volumes/networks and start all containers
		- Detached mode ```docker-compose up -d``` (run containers in the background)
	- ```docker-compose down```# stop all containers and remove contaienrs/volumes/network (not image tho)
		-  ```docker-compose down -rmi <type>``` remove the images afterwards (e.g. local, all) 
- With Dockerfile and docker-compose.yml, the "new developer onboarding" would be:
	```
	git clone github.com/some/software
	docker-compose up
	```
- Compose: "Talking to the Docker API in the background on behalf of the Docker CLI."
- Other commands
	- ```pcat docker-compose.yml```
	- ```docker-compose logs```
	- ```docker-compose --help```
	- ```docker-compose ps```
	- ```docker-compose top```
	
- Compose can also build your custom images
	- Add image building to the compose .yml file
	- ```docker-compose up``` won't rebuild if already exist
	- To rebuild: ```docker-compose build``` or ```docker-compose up --build```(build images before starting containers)
	- Great for complex builds that have lots of vars or build args
	- Example senario:  build custom image locally, mount some files into your app so that you can edit them at runtime, (add 3rd service) for a database-backed app.
	- ```docker-compose down -rmi <type>``` remove the images (e.g. local) 
	- Always add the directory name on the begining of all the assets to prevent name conflict (default)
	

### 6.3 Assignments
- Writing A Compose File for a Multi-Container Service (Drupal content management site)
	- Use the ```drupal``` image along with the ```postgres``` image
	- Use ```ports``` to expose Drupal on 8080 so you can localhost:8080 (key publishes the particular service on whatever port you specify, is the docker run equivalent to the -p flag)
		- Be sure to set ```POSTGRES_PASSWORD``` for postgres
		- If you don't know drupal's exposed ports:  check it on docker-compose file on Hub or pull it and do image inspect (Config-ExposedPorts)
		- Walk through Drupal setup via browser
	- Tip: Drupal's defual DB is ```localhost```, but we should change it to the Drupal service
		- use service name as their container DNS name
		- if use the default ```localhost``` for DB, we need our DB and web server in the same container, which is not the best practice for docker
	- Bonus: use volume to store Drupal unique data (Modules)
		- [Volume configuration reference](https://docs.docker.com/compose/compose-file/compose-file-v3/#volume-configuration-reference)
	
- Compose For Run-Time (On-The-Fly) Image Building and Multi-Container Development (Build a custom drupal image for local testing)
	- Use-case senario: testing apps, learning Drupal admin, etc.
	- Start with Compose fie from previous assignment. Make your ```Dockerfile``` and ```docker-compose.yml``` in dir ```compose-assignment-2```
		- ```apt-get install -y git``` automatically say Yes
		- ```\``` include the next line in this command
		- ```git clone --branch 8.x-3.x --single-branch --depth ....``` only download the lastest copy on that particular branch -- to save time (and space)
	- Build the dockerfile with a bootstrap template which allow you to add the template into the GUI of Drupal
	- ```build: .``` shorthand for buiding in this dir and use the dockerfile with the default name, otherwise should specify where the Dockerfile of the image is supposed to build from
	- add ```volumes``` in ```postgres``` to preserve our data (from ```docker-compose down```) across compose restarts
