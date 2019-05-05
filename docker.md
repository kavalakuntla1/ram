### Docker Tutorial

# Physical Machine
1.less handware
2.we cant change RAM

PM--->OS(ubuntu)-->App(tomcat)
PM--->OS(redhat)-->App(nodejs)

# Virtualization
1.we can change RAM
2.isolated system(execute on their own)
3.isolation at system level
4.own os for apps
5.every app has its own kernal
6.control over os level

HyperVisor(VMWare)--->VM--->OS(ubuntu)-->App(tomcat)
               |
                ----->VM---> OS(redhat)--->App(nodejs)

# Containerization
1.isolation are of execution of your apps(like tomcat or nodejs)
2.isolation at app level
3.same os for all apps
4.host kernel shared across containers
5.control over app level
6.docker is used to build container of your APP

PM-->OS-->Docker----->App(tomcat)
                |
                 ---->App(nodejs)
7.best place to host micro services(essentially one functionality) is docker container
8.if we too many containers use kubernetes(it is container orchestration wher you try to manage multiple containers running on different set of machines)
9.no limit on app size in  docker
10.docker size depends on RAM size of OS
11.containers created from images(thats specify their contents)

# Docker
1.Docker is a computer program that performs operating-system-level virtualization, also known as "containerization"
2.Docker is used to run software packages called "containers"
3.It was first released in 2013 and is developed by Docker, Inc
4.Docker is a platform for developers and sysadmins to develop, deploy, and run applications with containers

# Docker Installation
1.Linux

sudo apt-get update
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker ubuntu/centos
sudo service docker start
sudo service docker status


2.windows server 2016 and later

Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
(Install-WindowsFeature Containers).RestartNeeded
Restart-Computer

# Docker commands(basic)
docker --version
docker info
docker --help
docker container --help
docker container run --help
docker container run <ImageName>
docker ps

# Docker Image
1.It is a package with all dependencies and necessary information to create container
2.it is immutable once created

docker image --help
docker images(old)
docker run <options> <image>
  1. first check locally if image is there then run
  2. if image is not there it pulls form docker hub and run

example:-
docker hub ---> jenkins

docker pull jenkins
docker container run -p 8080:8080 jenkins                -p --> publish container ports to the host
docker container run -p 8081:8080 jenkins  
docker container exec -it <containerid> or <container-name> /bin/bash or /bin/sh

- to create docker image we have 2 ways
1.do the manual configuration    (create container and take snap of it)
2.export to docker image         (dockerfile which we need to create)

docker file --> it is text file which have some scripting

after writting docker file

1.to build image using docker file is

docker image build -t <folder where Docker file is stored> .

2.to get more information of one or more images

docker image inspect <folder where Docker file is stored> | more

-image is combination of layers stacked each others and it will have manifest
-manifest arranging layers or meta data of image
      docker image ls --digests
      sha<id> --> every image has this using hashing
-layers:- read only(can't change)
        - change made in layers not in image
-AUFS(old version File system and FS combines all layers) --> storage drivers 
-Overlay2(algorithm to create layer concept)--> storage drivers
        cd /var/lib/docker
        cd overlay2
        ls

# Docker Container
it is docker image instance which represent app or service



1.to run container

docker container run <foldername where Dockerfile is stored>

docker container run -d <imagename>

2.to go into container 

docker container run -it <imagename> /bin/bash

3.to stop container or delete

docker stop <containerid>

# Docker Registry
1.used stored images
2.it is service which provide access to docker images collection
3.default registry is docker hub
4.companies prefer private repositories to store docker images

private repositories - hosted docker registry, docker trusted regitry
public repositories - docker hub registry, AWS container registry, Azure container registry, docker trusted regitry, google container 


### Docker internals

container contains isolation with some resource limits

# Namespace
Isolations on the linux machines are created using a linux kernel feature called Namespaces
1.pID namespace (Process Namespace) creates the isolated process tree inside container 
2.net namespace (Network Namespace) creates the isolated networking for each container with its own network interface
3.mount namespace creation allows each container to have a different view of entire systems mount point, this allows containers to have their own file system view which starts from root 
4.user namespace allows to create whole new set of user & groups for the containers

# Control groups
Resource Limits are applied using kernel feature called as cgroups (Control groups)
Control groups is used to impose limits. We can impose limits of disk io, RAM & cpu’s using ControlGroups

## Docker Underlying Components
                               
                                REST                     GRPC
Client(used to write commands)--------> docker daemon ---------> Containerd---------> OCI(OpenContainerInitialization-used to create manage con)

in linux OCI is replaced with C RUNC

in windows Containerd is replaced with compute services


## Writing Docker files

first need to know how to install/configure the app manually

# task1
ubuntu server-install apache

manual steps:-

sudo apt-get upadate
sudo apt-get install apache2 -y

dockerfile:-

FROM ubuntu:18 or trusty
RUN apt-get update & apt-get install apache2 -y
CMD ["echo","hi"]

# Docker file contains
1.instructions
2.base image--->every container has base image
3.configuration
                
docker pull <imagename>
image name format is <registryname>/repository:<imagetag>

docker pull docker.io/enkins:latest

# Docker instructions
1.FROM ---> base image

FROM <baseimage>:<tag>

example:-
FROM Ubuntu:18.04 or trustry

2.LABEL ---> adds metadata to image

LABEL <key>=<value>

example:-
LABEL "name":"lokesh"

3.COPY ---> copies new files or directories from <src> & adds them to <dest>
            it is used in local systems

COPY <src> <dest>

example:-
COPY file1 directory

4.ADD ---> copies new files, directories or remote file URLs from <src> and adds them to <dest>
           it can be used for remote urls also

ADD <src> <dest>

example:-
ADD <url> directory

5.EXPOSE --->informs Docker that the container listens on the specified network ports at runtime
             You can specify whether the port listens on TCP or UDP, and the default is TCP if the protocol is not specified
             display ports to be run
  
EXPOSE <port>/<tcp or udp>

example:- 
EXPOSE 80/udp
EXPOSE 80/tcp

6.CMD ---> provide defaults for an executing container
           which get executing while container running
           we can change the msg (can be over written)
1.exec form ---> CMD ["executable","param1","param2"]
               CMD["echo","hi"]
               preferred this
2.CMD ["param1","param2"]
3.shell form ---> CMD command param1 param2
                  CMD echo hi

docker container run cmd --> hello
docker container run cmd pwd --> /


7.ENTRY POINT ---> freezes the app which gets started while you are running container
                   add anything is arguement to EP
                   apps cant change
                   arguements can change

                   ENTRYPOINT["echo","hi"]

docker container run cmd --> hello
docker container run cmd pwd --> hello pwd

***Life time of your container is time which CMD/EP are alive
***CMD/EP--> should have something which runs till the your app is alive

8.RUN ---> instruction that will be executing while image is building

RUN <command> ---> shell form
RUN ["executable", "param1", "param2"] ---> exec form
                

9.ENV --->  sets the environment variable <key> to the value <value>
            to get these go inside the container typre set | more will get env in last line
            even after building image


ENV <key>=<value>

example:-
ENV name="lokesh"

10.ARG --->  defines a variable that users can pass at build-time
             can be called while building images

ARG <name>[=<default value>]

ARG location="/var/lib/tomcat/gameoflife.war"

11.STOPSIGNAL ---> sets the system call signal that will be sent to the container to exit
                   just type "exit"

STOPSIGNAL signal

12.HEALTHCHECK ---> when can you tell container run
                    tells Docker how to test a container to check that it is still working
we have 3 states 
1.starting --> initial state
2.healthy ---> success healthcheck (0)
3.unhealthy ---> fail healthcheck (1)

HEALTHCHECK [OPTIONS] CMD command
HEALTHCHECK NONE

13.USER ---> it creates to user & you will switch as user
             sets the user name (or UID) and optionally the user group (or GID) to use when running the image and for any RUN, CMD and ENTRYPOINT instructions that follow it in the Dockerfile

             ENV not used

USER <user>[:<group>] 
or
USER <UID>[:<GID>]

14.WORKDIR ---> pwd
                changing your starting directory to something else
                ARG used
                 sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile

15.dockerignorefile ---> exclude or ignore files from root
                    
### Building Images

# Task:-1 Build java application on tomcat

1.From scatch
  - will copy all OS components
  - install java
  - install tomcat
  - deploy app
2.From Java
  - install tomcat
  - deploy app
3.From Tomcat
  - deploy app

we mostly use 3rd approach
1st approach used in IOT devices

First take any java app(gameoflife) build it and upload the .war file to S3 bucket 

Manual steps:

1.install java8(windows) openjdk8(linux)
2.install tomcat7
3.copy .war file in  usr/local/tomcat/webapps/
4.restart tomcat
5.tomcat works on port 8080


install tomcat pull image from docker 

docker image pull tomcat:8
docker container run -it tomcat:8 /bin/bash
cd webapps
wget<s3bucket url of gameoflife.war file>

Dockerfile looks like as shown below

FROM tomcat:8
LABEL "author"="lokeshcraig"
RUN cd webapps/ && wget <s3url of gameoflife.war>
ADD <s3url> /usr/local/tomcat/webapps/gameoflife.war
EXPOSE 8080
CMD ["catalina.sh","run"]


cd /images/gol/Dockerfile
docker image build -t gol .
docker container run -d -p 8080:8080 gol

browser ---> publicip:8080 ---> tomcat page display
        ---> publicip:8080/gameoflife ---> gameoflife page display

Docker file using ARG

FROM tomcat:8
LABEL "author"="lokeshcraig"
ARG downloadlocation="<s3url of gameoflife.war>"
ARG containerlocation="/usr/local/tomcat/webapps/gameoflife.war"
ADD <s3url of gameoflife.war> $location 
EXPOSE 8080
CMD ["catalina.sh","run"]



## Docker execution modes
1.Default mode(attached mode) ---> initially we have this mode
                                   this is used for Dev purpose

    docker container run alpine

2.Detached mode --> to run container

docker container run -d alpine

3.interactive mode ---> go into container

docker container run -it alpine bin/sh

## Best practice to write dockerfiles
1.docker file os step by step manually doing
2.dockerfile should be committed/placed at developers code(root directory of app)

steps to CI:- 
- build code
- build docker image
- push docker image to registry


## Persistent Storage in Docker Containers (Volumes)

- container states
1.started
2.stopped
3.restarted
4.terminated(everything will lost)

lets create a container alpine

create container 
stop container
start container --> is everything is available ---> Yes

terminate container --> is everything is available ---> No

**so docker container expects to be immutable(dont change) and ephemeral(short live)
**What if i want some data(files/folders) from container even after terminating This is possible with docker volume

- docker volume
volume is persistent storage on container
docker volume command used
docker has a special command for this docker volume
=> create
=> delete(detach or remove)
=> attach


docker container run --name cont1 -it alpine /bin/sh
mkdir logs
cd logs
touch 1.txt 2.txt
exit

docker container ls
alpine

docker stop cont1
docker start cont1

docker container exec -it cont1 /bin/sh
cd logs
ls
1.txt 2.txt
exit
docker container rm -f cont1

docker volume create v1
docker volume ls
v1
docker volume inspect v1

docker container run --mount "type=volume,source=v1,destination=/logs" -it alpine /bin/sh
cd logs
echo "hi" > 1.txt
echo "hi" > 2.txt
docker container rm -f alpine
docker volume inspect v1
sudo ls /var/lib/docker/volumes/v1/_data
1.txt 2.txt

docker container run --mount "type=volume,source=v1,destination=/logs" -it --name cont2 alpine /bin/sh
cd logs
ls
1.txt 2.txt

docker rm $(docker ps -a -q) ---> to delete all stopped containers

### Docker Networking

** network range --> how many containers within the network

docker container run -d --name c1 alpine sleep 1d
docker container run -d --name c2 alpine sleep 1d

docker inspect c1
display ip 172.17.0.2

docker inspect c2
display ip 172.17.0.3

docker network ls
we have bridge

# drivers defined network types
1.bridge
2.overlay
3.MACVLAN
4.IPVLAN

# 1.Bridge 
default network in use

docker network inspect bridge
docker exec -it c1 sh
ping 172.17.0.2 --> will ping
ping c1 ---> will not ping

by default we can ping ip not name
** container can speac with network(bridge)

-d --> driver

docker network create -d bridge --ip-range 192.168.0.0/16 mybridge --subnet 192.168.0.0./24
docker network ls --> mybridge created
docker container run -d --name p1 --network mybridge alpine sleep 1d
docker container run -d --name p2 --network mybridge alpine sleep 1d
docker exec -it p1 sh
ping p1
ping p2
ping ip of p1
ping ip of p2

all 4 are ping this type

** every bridge is isolated network

# 2. Overlay
** overlay network is in build in docker
node1(web) node2(DB) can be pingable
connected 2 nodes internally
underlay--> connect 2 nodes externally
# 3. MACVLAN
doent work on cloud
used in open stack
# 4. IPVLAN
not fully developed


** orchestration ---> maintain multiple docker machines
we can use kubernetes or docker swarm
kubernetes are widely used to manage container running on multiple nodes

## Docker swarm
Enable multi host orchestration
master(initialize) nodes(join to master)

conditions:-
1.all 3 should install & work on same version of docker
2.all 3 should be pingable to each other

AWS ---> EC2 ---> ubuntu 16.04 ---> 3 (master, node1, node2)---> SG (All TCP/ All UDP) ---> Launch
do docker installation in all 3 VM's

 
curl -sSL https://get.docker.com/ | sh
sudo service docker start
sudo usermod -aG docker ubuntu

Master ---> docker swarm init
                      
node1/node2 --->  docker swarm join --token SWMTKN-1-346v9l36kb8wdh6b8tp3ls4ralw4httiv7pa0m5vq8g3pu9cpy-clmumwya4n5izgl0g73n26b1g 172.31.80.86:2377

Master ---> docker node ls ---> we get leader along with 2 nodes
            docker network ls ---> overlay
            docker network create -d overlay my-overlay
            docker network ls ---> my-overlay
            docker service create --name my-svc --network my-overlay --replicas 3 alpine sleep 1d
            docker container ls --> alpine

node1/node2 ---> docker container ls ---> alpine
                 docker network ls ---> my-overlay

Master ---> docker service ps my-svc ---> master with 2 nodes 

if we shutdown node2

node2 --> actions ---> instance state ---> stop(it consider as failure)

Master ---> docker service ps my-svc ---> one node is shutdown in place of that another node created


** Dont use same service names in docker
** we can change using same docker service
**if docker master dies --> we have internal election between nodes to elect one node as master







