#Openshift Notes
---------------  

| Containers |
==============

Its a segregated user space env for running our application. This is made possible with the help of the below linux features

* *Namespace:* Generally used to isolate process to hide containers and protect system resources making it invisible to other containers and avoid interaction between each containers.Docker creates namespace for each containers. 

* *CGroups (Controll Groups):* Creates partition for the set of process to limit and make sure that the resources are available only for that particular container and its not stealing too much resources from the host machine which will end of performance issue and resource failures to the host machine

* *SELinux:* This is used to protect the access between each containers and also between containers to host machine. 

Container are not new concepts but dockers made it easy, friendly and usable.

----- 

| Docker | 
==========

* Docker is a new way of deployment strategy which will pack our app with all the needed dependencies & lib.

* Docker is an Opensource Container implementation supported by Red Hat and used in openshift

* *Docker File:* Contains a set of instruction on how to create a docker image & run our app. It defines all the needed dependences and lib that are required to run our app. 

** Docker Image:* It a template to create containers.

* *Docker Container:* Its an instance of Docker images running as an isolated process.

* *Docker Registry:* Docker images are stored in the registry 

| Docker commands |
===================

docker build -t <repository:tag_name> <path_to_dockerFile> <br />
docker images <br />
docker inspect <docker_image> <br />
docker pull <br />
docker run --name <container_name> -e ENV_VAR_NAME1=ENV-VAR_VALUE1 -e ENV_VAR_NAME2=ENV-VAR_VALUE2 -d <repository:tag_name> <br />
docker ps -a -q <br />
docker kill <container_id> <br />
docker exec -it <container_name> bash (enter exit to come out container it bash shell) <br />
docker rm <container_name> <br />
docker rmi <repository:tag_name> <br />

Best Practice to create Docker File
------------------------------------

* Passing credentials via env variables is not a best practice but we can use docker secrets to pass/configure the app credentials.

* Its a good practice to add the maintainer information in the dockerfile.

* Docker will create layers for each instructions in the docker file. Hence its a good practice to specify all commands grouped together in a single line to avoid docker not to create lots of complex layers and increasing the docker image size

* For readability purpose we can use line breaks \ in dockerFile

* Always use ADD instead of COPY

* Diff B/W ADD & COPY is ADD can do what COPY does + it has the ability to unzip or untar from archive. It can also download from the url.  

* Its a good practice to have both ENTRYPOINT & CMD in a dockerFile. Reason for this is if you give CMD in dockerFile then at the runtime we can override the arguments specified in the dockerFile at the time of running a docker image via docker run cmd.

----- 

| Kubernetes  |
===============

* Kubernetes is an container orchestration and management tool/software which provides Caas (Container As A Service)

* Kubernetes contains Etcd (key value store) where its store all its data, state of the containers and all information that is required to run the cluster.

Need:
-----

Container & Docker architecture are light weight and they will be spinning our application in seconds.
But at the same time they are bound to fail. 

* Kubernetes makes sure that our application deployed over the container & Docker architecture is highly available.

* Kubernetes maintains HA by spinning a new container automatically when a container fails and maintains the integrity of the cluster.

* Kubernetes also takes care of orchestration to connect multiples containers running in the cluster.

Kubernetes cluster consists of a master node & worker node.

* Master node takes care of orchestration, scheduling, monitoring, datastore, management, replication, authentication.

* Worker node is where our application are deployed as containers i.e, pods.

Task:
----- 

* *Orchestration:* How to make sure that containers talk to each other in the cluster. Solution to this problem is : Services (since containers ip address are not constant and transient)

* *Scheduling:* How to make sure that our application is highly available with the desired minimum number of instance running at any given time. Solution to this problem is Replication controllers. Also we can scale extra number of containers based on load.

* *Isolation:* Maintains the integrity of the cluster and makes sure that the failure of container is not affecting the other containers running in different nodes 


Features:
---------

* Pods
* Replication Container
* Services
* Persistence Volume
* Persistence Volume Claim
* Labels
* Configmaps
* Secrets
* Namespace

----- 

| Openshift |
=============

* Built on top of docker & kubernetes inheriting its core features and implements some extra features on top of to it making it user friendly for developers & administrators and implementing CI/CD pipeline for devops process.

Features: 
---------

* *s2i:* It a feature that allows us to build our application from the source code directly to docker image via the S2i scripts.
		
	Benefits of s2i
	---------------
		
	* User Efficiency : Developers dont need to spend time in creating docker file and images.
	
	* Helps with Patching & security : s2i can be configured to automatically rebuild and redeploy our app if any new patch or security updates provided to our base image stream.
		
	* Increase speed : s2i can perform lots of complex operations in an efficient manner in the most possible way it can be rather then the developer trying to manage the dockerfile. This will minimize any overhead that we might encounter while doing this activity manually.

* *Image Streams:* Its a resource file that compiles all of the related images into a single file. For example for MySQL image stream it contains all the version of MySQL and openshift actively monitors this image stream. 
	
	* If any patch or security update is release then openshift will automatically detect these changes and build our application with the latest updates by triggering a new rebuild and redeploy. This behavior can be configured 

* *Build Configuration:* After creating a new application, the build process starts creating a buildconfig pod.
	
	* The BuildConfig pod is responsible for creating the images in openshift and push them to the internal docker registry.

* *Deployment Configurations:* Represents a set of pods created from the same container image. This is used to manage Rolling updates, make an update to the base image and push it out for deployment via the CI/CD pipeline. 
	
	* The DeploymentConfig pod is responsible for deploying the pods into the openshift.
	
* The Build/Deployment config resources do not interact directly. The BuildConfig resources do not interact directly.

* The BuildCOnfig creates or updated a container image and the DeploymentConfig reacts to this new image or to the updated image event and creates pods from  the image.

* *Routes:* It exposes kubernetes services to the external world so that our application can be accessed via http url from outside of kubernetes/openshift cluster.

* *Devops Tools:* SCM integration, CI/CD pipeline.

* *Webconsole:* Provides user friendly interaction to scale pods set resource limits create and claim persistence volumes. Provide Role based access to the users.
	
	Benefits of webconsole
	----------------------

	* Openshift webconsole is available in the master node.
	
	* Webconsole provides the following features
		* create project and manage application 
		* scaling and managing users, teams
		* create & manage project quota, user membership, secrets, pvc
		* monitor build & deployements 
		* implement health checks
		* create DI/CD pipeline using jenkins 			
			

| Openshift commands |
======================

oc login -u <username> -p <password> os_server_url <br />
oc new-project <br />
oc project <project_name> <br />
oc adm policy add-scc-to-uset anyuid -z default (to allow apps to run as root) <br />
oc new-app --docker-image=registry.com:port/repository:tag <br />
oc new-app <imagestream:tag>~<git_url>  --name=<app_name> <br />
oc status <br />
oc get all <br /> 
oc get project <br /> 
oc get pods <br />
oc get pods -w <br />
oc describe pod <pod_name> <br />
oc get svc <br />
oc export <br />
oc create -f <br />
oc edit <br />
oc start-build <app_name> <br />
oc expose svc <service_name> --name <app_route_name> <br />
oc delete project <project_name> <br />
oc exec <br />

* oc-new cmd creates only the service for the app and will not create the route for the app.

* imagestream is optional & openshift will detect the language of the app code based of build configuration. But it will help us to control or chose the build for a particular version of image tag if given else openshift will use the latest available tag for the image stream.
