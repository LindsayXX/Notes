## Section 10 Container Registries; Image Store and Distribution
### 10.1 Docker Registry
- Docker Hub: Docker Registry plus simple image Building
	- like GitHub
	- Creat **Web Hooks**: to send a webhook to other service (e.g. Jenkins, Codeshio, Travis CI, etc.) to have automated builds continue down the lines (code from Git/GitHub -> Docker -> your servers where you run them)
	- **Auto-build**: be up-to-date with the source code / re-build when there is an update
		1. *Creat->Creat Automated Build* when you are using code from github: allows docker Hub to create the CI path that will automatically build your image based on code commits (like a  reverse Web Hook). 
	    2. Add *Repository Links* when you are using FROM: your image get re-build when that software (your dependencies) changes.
	      3. Creat a *Build Trigger* when your source software is not Github

- Docker Registry: Image storage and distributions		
	- Can be a private image registry stored privately on your network
	- Part of the docker/distribution GitHub repo
		- Docker Hub has a pre-build official image for Registry, as simple as Docker pull registry (?)
	- The de facto backend registry for storing ur containers privately
	- Not as full featured as Hub or other 3rd-party service, basic auth only		
	- As its core: a web API (HTTPs server) and storage system, (re-)written in Go
	- Storage supports local, S3/Azure/Alibaba/Google Cloud, and OpenStack Swift
		- local by default, using local file system in the image or on your host using volumes
	- [Secure your Registry with TLS](https://docs.docker.com/registry/configuration/)
	- [Storage cleanup via Garbage Collection](https://docs.docker.com/registry/garbage-collection/)
	- [Enable Docker Hub caching via "--registry-mirror"](https://docs.docker.com/registry/recipes/mirror/)
		- enable proxy mode: let docker deamon to cache images inside their registry => optimize bandwidth, provide failover
	- Registry and Proper TLS
		- "Secure by Default": Docker don't talk to any registry unless it's running proper HTTPs security (TLS?)
		- Except localhost (127.0.0.0/8)
		- For remote self-signed TLS, enable "insecure-registry" in engine
	- [3 Docker options and 3rd-party options](https://github.com/veggiemonk/awesome-docker#hosting-images-registries)
		- Docker Hub, Docker Enterprise Edition DTR (Docker Trusted Registry), and Docker Registry
		- Cloud-based choices: Quay.io (most popular), AWS, Azure, Google Cloud
		- self-hosted options: Docker EE, Quay Enterprise, GitLab (Container Registry), etc
		
- Example: install and use Docker Registry as private image store (Run a private registry -- using docker registry locally)
	- Runs the registry image on default port 5000
		- ```docker container run -d -p 5000:5000 --name registry registry```
	- Re-tag an existing registry and push it to your new registry 
		- For images with Docker Hub, label it with account name/repository name
		- For any other Resigtry, label it with host/repository name
		- ```docker tag hello-world 127.0.0.1:5000/hello-world``` the equivalent of making my own official image at the root of my local registry
		- ```docker push 127.0.0.1:5000/hello-world```
	- Remove that image from local cache and pull it from new registry
		- ```docker container rm <container that run hello-world>``` and ```docker image remove 127.0.0.1:5000/hello-world``` and ```docker image remove hello-world```
		- ```docker pull 127.0.0.1:5000/hello-world```
	- Re-create registry using a bind mount (volumn) and see how it stores data
		- Cleanup registry: ```docker container kill registry``` and ```docker container rm registry```
		- creat a volumn ```docker container run -d -p 5000:5000 --name registry -v $(pwd)/registry-data:var/lib/registry registy```
		- ```ll registry-data``` there's nothing
		- ```docker push 127.0.0.1:5000/hello-world```
		- ```ll registry-data``` there's info
			(use ```tree resitry-data``` to show dirs and files visually)

- **Using Registry with Swarm**
	- Use ```docker service``` or use a stack file instead of ```docker run```
	- Because of Rounting Mesh, all nodes can see 127.0.0.1:500 as if it's local
	- Remember to decide how to store images (volumn driver)
	- NOTE: All nodes must be able to access images, that's why they all have to push and pulls from a central repository
	- Example on play with docker:
		- Set up 5 managers and no workder (use ```docker node ls``` to confirm)
		- Run the registry as a service ```docker service create --name registry --publish 5000:5000 registry``` (use ```docker service ps registry``` to confirm)
		- catalog of *port* return a JSON that shows repositories is empty
		- do the same as the local example (on one of the manager nodes)
			- ```docker pull hellow-world``` 
			- ```docker tag hello-world 127.0.0.1:500/hello-world```(specify an image with our custom registry)
			- ```docker push 127.0.0.1:500/hello-world```
		- refresh the port page, we now see "hello-world" in the "repositories"
	- Example on play with docker - **scale up** 
		- ```docker pull nginx``` 
		- ```docker tag nginx 127.0.0.1:5000/nginx```
		- ```docker push 127.0.0.1:5000/nginx```
		- ```docker service create --name nginx -p 80:80 --replicas 5 127.0.0.1:5000/nginx```
	- ProTip: Use a hosted SaaS registry if possible
		
### 10.2 Assignmnet
- Secure Docker Registry with TLS and Authentication
	- j
- Other 
	- Docker Store 
	- Docker Cloud
	- Swarm features
	
	
## Section 11 Docker in Production (Swarm talks)
 ### 11.1 DockerCon 2017 talk
 - Limit your simultaneous innovatioon
	 - don't make it first thing to put ur database to Swarm cluster (persistent data is a harder thing to deal with)
 - Legacy Apps work in containers too
	 - 12 factor is a horizon
 - What to focus on first: Dockerfiles
	 - more important than fancy orchestration
	 - new build and environment documentation
	 - study Dockerfile/ENTRYPOINT of Hub Officials
	 - FROM Officiall distros that are most familiar
	 - Dockerfile Maturity Model
		 - Start
		 - log all things to **stdout/stderr** 
		 - documented in file
		 - work for others
		 - lean
		 - scale
	 - Dockerfile Anti-pattern: Trapping Data
		 - Problem: Storing unique data in container
		 - Solution: Define VOLUME for each location
	 - Dockerfile Anti-pattern: Using Latest
		 - Always remeber to type the version (and avoid the latest!)
		 - Problem: image builds pull FROM latest
		 - Solution: Use specific FROM tags
		 - Problem: Image builds install latest packages
		 - Solution: Specify version for critical apt/yum/apk packages
	 - Dockerfile Anti-pattern: Leaving Default Config
		 - Problem: Not changing app defaults, or blindly coping VM conf 
		 - Solution: Update default configs via ENV, RUN, and ENTRYPOINT
	 - Dockerfile Anti-pattern: Environment Specific
		 - Problem: Copy in environment config at image build (and envs are infinite)
		 - Solution: Single Dockerfile with default ENV's, and overwrite per-environment with ENTRYPOINT script
		 - use the ```COPY``` stanza
	 - Containers-on-VM or Container-on-Bare-Metal
	 - OS Linux Distribution/Kernel Matters
	 - Container Base Distribution: Which One?
- Good Defaults: Swarm Architectures
	- Baby Swarm: 1-Node
	- HA Swarm: 3-Node 
		- Minimum for HA
		- All Managers
		- One node can fail
		- Use when very small budget
		- Pet projects or Test/CI
	 - Biz Swarm: 5-Node
		 - Better high-availability
		 - All Managers
		 - Two nodes can fail
		 - My minimum for uptime that affects $$$
	 - Flexy Swarm: 10+ Nodes
		 - 5 dedicated Managers 
		 - Workers in DMZ
		 - Anthing beyond 5 nodes, stick with 5 Managers and rest Workers
		 - Control container placement with labels + constraints (metadata that allows you to tell a service that you're creating for Docker that it needs to be over here with a SSD, security profile scanner you run on a particular server for PCI compliance)
	 - Swole Swarm: 100+ Nodes
		 - 5 dedicated managers
		 - Resize manager as you grow
		 - multiple worker subnets on Private/DMZ
		 - Control container placement with labels + constraints
	 - Don't Turn Cattle into Pets
	 - Reasons for Multiple Swarms
 - Outsource well-defined plumbing
	 - Outsourcing: For your consideration
		 - Image registry
		 - Logs
		 - Monitoring and alerting
		 - [Cloud native landscape](https://github.com/cncf/landscape)
	 - Pure Open Source Self-Hosted Tech Stack
		 - Swarm GUI
		 - Central Monitoring
		 - Central Logging 
		 - Layer 7 Proxy 
		 - Resitry
		 - CI/CD
		 - Storage
		 - Netwoking
		 - Orchestration
		 - Runtime
		 - HW/OS
	 - Docker for X: Cheap and Esay Tech Stack
	 - Docker Enterprise Edition + Docker for X
	 
 - Must we have an Orchestrator?
	 - One Container Per VM: Not New
 - Summary:
	 - Trim the optional requirements at first
	 - First, focus on Dockerfile/docker-compose.yml
	 - Watch out for Dockerfile anti-patterns
	 - Stick with familar OS and FROM images
	 - Grow Swarm as you grow
	 - Find ways to outsource plumbing
	 - Realize parts of your tech stack may change, stay flexible


### 11.2 Swarms
- [Orchestration Video](https://www.youtube.com/watch?v=Qsv-q8WbIZY)
	 - how you always need an odd number of managers
	 - recovery options when you "loose quorum" in Swarm cluster
 - [Swarm's New Features 2020](https://www.youtube.com/watch?v=L5N43aQQArw&t=223s)
	 - 
## Section 12 Kubernetes Intro
### 12.1 The What and Why of Kubernetes
- Kubernetes: the next container orchestrator after Swarm
- Why you *may* need orchestration?
	- servers + change rate = benefit of orchestration
	- Otherwise, Elastic Beanstalk, Heroku is alternatives to do ur own orchestration, 
	- other tranditional ones specific to cloud platform (ECS on AWS, Cloud Foundry, Mesos, Marathon)
	- hybrid solutions: Swarm and Kubernetes 
- [Which distribution](https://kubernetes.io/partners/#conformance) to use?
	- cloud or self-managed?(Docker Enterprise, Rancher, Openshift, Canonical, VMware Enterprise PKS)
	- Raw github upstream of Kubernetes (Pure): all the distributions are based on
	- Vendor solutions usually comes with other tools that make it easier to manage, e.g. authentication solutions, custom Web administrations, custom networking
- What?
	- Container Orchestration: Make many servers act like one
	- A set of APIs that run on apps in continaer to manager a set of servers and execute your container on Docker (by default)
	- a seriers of containers on top of container runtime that manage the multi-node system that it's controlling
	- Provides API/CLI to manage container across servers
	- kubectl tool
	- Many clouds/vendors provide it, which is a "upstream" "distribution" of it

- Kubernetes vs. Swarm
	- Both are container orchestrators
	- Both are solid platforms with vendor backing
	- Swarm: Easier to deploy/manage
		- Comes with Docker, single vendor container platform
		- Follows 80/20 rule, has 20% of features of Kubernetes that solve 80% use cases
		- Runs anywhere Docker does: local, cloud, datacenter | ARM, Windows, 32-bit
		- Secure by default
		- Easier to troubleshoot
	- Kubernetes: More features and flexbility to solve more problems
		- Clouds will deploy/manage Kubernetes for you
		- Infrastructure vendors are making their own distributions 
		- Widest adoption and community
		- Flexible: Covers widest set of use cases
		- "Kubernetes first" vendor support
		- *"No one ever got fired for buying IBM"*
		
### 12.2 Kubernetes Install
> K8s Local Install
> K8s Container Abstractions
> Kubectl Run, Creat, Apply
> Our First Pod
> Scaling ReplicaSets
> Inspectin Objects
- Kubernetes Architecture Terminology
	- Kubernetes: The whole orchestration system (K8s "k-eights" or Kube for short) 
	- Kubectl ("cube control"): CLI to configure Kubernetes and manage apps
	- Kubelet: Kubernetes agent running on nodes (Docker Swarm doesn't need one since it's built-in)
	- Control Plane (sometimes called Master): Set of container that manage the cluster. Each Master includes:
		- etcd (backend DB, distributed storage system for key values, similar to RAFT in Swarm)
		- API server
		- Scheduler: control how and where your container are placed on Nodes in Pods
		- Controller Manager: 
		- CoreDNS: to control DNS
		- more add-ons: networking, storage, web, etc.
	- Node ("worker"): Single server in the Kubernetes cluster
		- Kubelet (agent)
		- Kube-proxy
		- more add-ons
		
- Kubernetes local install
	- Docker Desktop: the easist way to install
		- set up everything inside Docker's existing Linux VM
		- Runs/configures Kubernetes Master containers
		- Manages kubectl install and certs
		- Easily install, disable, and remove from Docker GUI
	- Docker (Toolbox) on Windows: MiniKub
		- Download installers from Github
		- ```minikube start --kubernetes-version='1.17.4'``` in VirtualBox (default is to install the lastest Kubernetes release)
		- creates a virtualbox VM with kubernetes master setup
		- much like the docker-machine experience
		- doesn't install kubectl
		- unlike Docker Desktop that allows u to use localhost, minikube runs in VirtualBox and has its onw IP ```minikube ip```
		- ```minikube stop``` when you're not using it to save rescources
		- ```minikube status``` check the status of what is running
	- Your Own Linux Host or VM: MicroK8s
		- Using snap for intall (install snap through apt-get or yum first) ```sudo snap install microk8s --classic --channel=1.17/stable``` (by default the lastest)
		- before using it, enable the CoreDNS pod so it'll resolve serive DNS names later ```microk8s.enable dns```
		- control the MicroK8s service via ```microk8s.commands```
		- kubectl accessable via ```microk8s.kubectl```
			- add an alias to your shell (.bash_profile) ```alias kubectl=microk8s.kubectl```
		- check status with ```micriok8s.status```
	- Kubernetes in a Browser
		- http;//play-with-k8s.com or katacoda.com
		- katacoda already provide a setup clustering
	- P.S. there's many ways to install and packages different containers
	
- Pod and Controller
	- Pod: one or more containers running together on one Node
		- Basic unit of deployment. Containers are always in pods
	- Controller: for creating/updating pods and other objects
		- Usually we deploy a controller instead a Pod directly
		- a differencing engine that has many types: **Deployment** (similar to services in Swarm), **ReplicaSet**(make sure the number of pods we ask for are currently running), 3rd-party controller (StatefulSet, DaemonSet, Job, CronJob, etc.)
	- Service: network *endpoint* to connect to a pod
	- Namespace: Filtered group of objects in cluster (advance option)
		- a filter on your view on the command line
		- not the same thing as Namespace in Docker (which is a security feature)
	- Secrets, ConfigMaps, and more
	
### 12.3 Commands
- Create Pods from kubectl CLI
	- check if working: >kubectl version
	- Create pods 
		- ```kubectl run``` only for single pod creation after 1.18 (similar to docker run creat a single container) (before 1.18: creates a ReplicaSet which create a Pod)
		- ```kubectl create``` create some resources via CLI or YAML, e.g. ``` kubectl create deployment nginx --image nginx```
		- (less used) ```kubectl apply``` create anything via YAML
		- other ways
- 2 ways to deploy Pods (containers)
	- via commands 
		- ```kubectl run my-nginx --image nginx```
		- list the pods ```kubeclt get pods```
		- see all objects ```kubeclt get all```
	- via YAML
	- Pods -> ReplicaSet -> Controllers
		- when run, Deployment creat a ReplicaSet, and ReplicaSet create a Pod/Pods
- Cleanup
	- ```kubectl delete deployment my-nginx```
	- ```kubectl get all``` to check
- Scaling ReplicaSets: creating more pods with kubectl scale
	- Start a new deployment for one replica/pod 	
		- ```kubectl run my-apache --image httpd```
	- scale up to two
		- ```kubectl scale deploy/my-apache --replicas 2``` 
		- or ```kubectl scale deployment my-apache --replicas 2```
		- ```deploy/=deployment=deployments```
	- Backgroud of doing replicas command: 
		- updating the Deployment spec to 2 replicas
		- ReplicaSet Controller set pod count to 2
		- Control Plance assigns node to pod
		- Kubelet sees pods is needed, starts container
		- In Swarm everything happenes in a single binary so a log quicker. In Kubenetes we get additional layers of abstraction, but the delay is negligible, and we get more flexibility and control with different layers of abstraction
-  **Inspecting** deployments: getting details 
	- ```kubectl get pods```
	- Get container **logs** 
		- ```kubectl logs deployment/my-apache```
		- “Found 2 pods, using pod/<pod name> ...”
		- the default log won't pull all the different pods and combine them tgt
		- ```kubectl logs deployment/my-apache --follow --tail 1```
		- will wait for anything new to show up in the logs	of that pod (?)
		- return the last one only
	- Get MULTIPLE pods logs
		- Selector in Kubernetes, a technique to grab multiple pods based on a label that they all share
		- ```kubectl logs -l run=my-apache``` (```-l``` the label)
		- it can only pull up to 5 pods when using selector type pulling in the logs
		- [Stern](https://github.com/wercker/stern) tool for better log tailing 
	- **describe** a specific pod
		- ```kubectl get pods``` to get pod name
		- ```kubectl describe pod/my-apache-xxxx-yyyy```
		- will get a bunch of details about an object, including events
	- Watch a command (without needing watch)
		- ```kubectl get pods -w```
		- delete a pod in a seperate window
		```kubectl delete pod/my-apache-xxxx-yyyy```
		- watch the pod get re-created
