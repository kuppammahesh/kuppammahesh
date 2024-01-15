DOCKER:

WHY DOCKER: docker ensures that your application will be same on your machine and any other machine in the world.

DOCKER DAEMON: it receives the commands from the clients and manages the objects like images, containers, networks and volumes.


CONTAINER:
	It is a way of packing an application with all the necessary dependencies and configuration files.
	It a directory which has all the libraries/binaries to run the service.
	directory has its own IP address and port numbers to access the services.
	Container is a virtual  machine which doesn’t have any OS because it shares the host machine OS system.
	Docker containers are standard, security and lightweight. 
	Docker engine combines the namespaces(name of the container), Cgroups(CPU, Memory & networks) and UnionFS(File systems) to form a container.

IMAGES:
	Docker images are used to pack the application.
	It consists of multiple layers. Each layer will be some set of data. If you add some files it will add a layer to the image, when you change any configuration files that will create a layer. 
	When we create image from the container. That image will be read only mode. 
	Images becomes containers when we run it.
	When you add some data in a container and commit it will automatically create a layer.

Docker exec - used to run any command in the container from the outside of the container
Docker it - interactive terminal, if we are running a container without ‘it’ container will be exited state  because it runs on bin/bash command, due to the absence of ‘it’ the shell was dead and container also dead. 
ctrl+p+q ——> used to exit from the container without stopping the container.
===================================================================

RUN: it is used to execute the commands while we build the images and add a new layer into the image.

FROM centos:centos7
RUN yum install git -y 
	or
RUN [“yum”, “install”, “git” “-y”]

CMD: it is used to execute the commands when we run the container. 
	   It is used to set the default command.
	   if we have multiple CMD’s only last one will gets executed.

FROM centos:centos7
CMD yum install maven -y
	or
CMD [“yum”, “install”, “maven”, “-y”]

If you want to overwrite the parameters:
	docker run image_name  httpd (FAILED)
	docker run image_name yum install httpd -y (only httpd will gets installed)

ENTRYPOINT: it overwrites the CMD when you pass additional parameters while running the container.

FROM centos:centos7
ENTRYPOINT [“yum”, “install”, “maven”, “-y”]

If you want to overwrite the parameters:
	docker run image_name  httpd (both maven and httpd will gets installed)
	docker run image_name yum install httpd -y (both maven and httpd will gets installed)



FROM centos:centos7
ENTRYPOINT [“yum”, “install”, “-y”]
CMD [“httpd”]

Bydefault it will executes httpd command, if you specify the command while running the container it will gets executed.
	docker run image_name (httpd will install)
	docker run image_name git (only git will install)
	docker run image_name git tree(both git & tree will install)

COPY: It copies the files from local server to container.
ADD: It will also copies the files but the difference is it can handle remote URLs & it can auto-extract tar files.
ARG: it is used to define a variable that is used to build a docker image, it will not available once we build it. In containers also it’s not possible to access it.
	  we can change these variables in command line arguments (docker build -t test --build-arg var_name=value)
ENV: These variables are used for inside the container
	  we can’t change the values in command line arguments. f we need to change the ENV variable using CMD line then we have to use ARG and place ARG variable in ENV variable

ARG abc=devops
ENV xyz=aws
RUN echo $abc
RUN echo $xyz
===========================================================================

DOCKER NETWORK: it allows you to attach your container into many networks, it is used to isolate the containers

TYPES:
* Bridge: This is the default network driver and is used to create a standalone network for containers running on a single host. Containers on the same bridge network can communicate with each other directly and can also access external networks through the host.
* Host: This network driver removes network isolation between the container and the host, allowing the container to directly access the host's network stack and all its connected networks.
* Overlay: This network driver enables multi-host networking and is used to connect containers running on multiple Docker hosts. This allows containers to communicate with each other across hosts as if they were on the same network.
* Macvlan: This network driver allows a Docker container to have its own unique MAC address, effectively giving it a directly routable IP address. This is useful for scenarios where a container needs to appear as a physical device on the network.


To create a network: docker network create —attachable network_name
To see the list: docker network ls
To delete a network: docker network rm network_name
To inspect: docker network inspect network_name
To connect a container to the network: docker network connect network_name container_id/name
To disconnect from the container: docker network disconnect network_name container_name
To prune: docker network prune
============================================================================


Docker file for springboot application:

From openjdk:8-jdk-alpine
Copy target/*.jar code/
Entyrpoint [“java”, “-jar”, “jar_filename”]

============================================================================

DOCKER RENAME: 
To rename docker container: Docker rename old_container new_container
To rename docker port: 
	stop the container
	go to path (var/lib/docker/container/container_id)
	open hostconfig.json
	edit port number
	restart docker and start container


============================================================================

DOCKER SECRETS: by using this docker secrets, we securely store data that can be read by applications at runtime. his includes data such as passwords, hostnames, SSH keys, and more. 

let's say our application requires a database connection. To do this, it needs a hostname, username, and password. 
Furthermore, there's a different database server for development, testing, and production.
With docker secrets, each environment can provide its own database information to the applications. 

Create a file which contains sensitive content:  vim docker/password/secrets/file1
Username: Mustafa
password: 1234
Hostname: 111.23.45.2

To create a secret: docker secret create secret_name path(docker/password/secrets/file1)
To inspect a secret: docker secret inspect secret_name
To se the list: docker secret ls
To remove a secret: docker secret rm secret_name
docker service create --name my_app --secret mustafa openjdk:19-jdk-alpine

============================================================================
DOCKER EXPORT: It is used to save the docker container to a tar file
Create a file which contains will gets stored:  touch docker/password/secrets/file1.txt
TO EXPORT: docker export -o docker/password/secrets/file1.txt container_name
SYNTAX: docker export -o path container

============================================================================

DOCKER SWARM: It is a group of servers that runs the docker application. 
This can be implemented by the cluster. 
The activities of the cluster are controlled by a swarm manager, and machines that have joined the cluster is called swarm worker.
It is used to manage the containers on multiple servers

To create a service: docker service create —name devops —replicas 2 image_name
Note: image should be present on all the servers
To update the image service: docker service update —image image_name service_name
Note: we can change image, 
To rollback the service: docker service rollback service_name
To scale: docker service scale service_name=3
To check the history: docker service logs
To check the containers: docker service ps service_name
To inspect: docker service inspect service_name
To remove: docker service rm service_name


============================================================================
SOME EXTRA POINTS:
docker run -itd —rm —name Mustafa ubuntu - if you use —rm in a container, it will work since it Is in run state, when it goes to exited, it will gets deleted automatically
docker exec -it cont_name /bin/bash - used to enter into daemon containers

==========================================================================
version: "3.1"
services:
  mobile_recharge:
    image: nginx
    ports:
      - "9999:80"
    volumes:
      - "mobile-recharge-volume1"
    networks:
      - "mobile-recharge-network"

  mobile_recharge2:
    image: nginx
    ports:
      - "9998:80"
networks:
  mobile-recharge-network:
   driver: bridge

docker-compose up -d 	-	used to run the docker file
docker-compose build 		- 	used to build the images
docker-compose down		-	remove the containers
docker-compose config	- 	used to show the configurations of the compose file
docker-compose images	-	used to show the images of the file
docker-compose stop 		-	stop the containers
docker-compose logs		-	used to show the log details of the file
docker-compose  pause	-	to pause the containers
docker-compose unpause  	-	to unpause the containers
docker-compose  ps		-	to see the containers of the compose file



DOCKERIZING SPRING BOOT APPLICATION APP VS JAVA APPLICATION

SPRING BOOT: 
- [ ] When we build the code, It will generates a jar file.
- [ ] It will have a embedded(internal) server, we don’t need to configure separately.
- [ ] Command to run app: [“java”, “-jar”, “jar filename.jar”] 

JAVA APP:
- [ ] When we build the code, It will generates WAR file
- [ ] It needs to configure a web server like tomcat to run the application.

DOCKER FILE FOR SPRING BOOT APPLICATION

FROM java:8-jdk-alpine
COPY target/app.jar /usr/app/app.jar
WORKDIR /usr/app
ENTRYPOINT [“java”, “-jar”, “app.jar”]














