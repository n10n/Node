# Dockerfiles 

Please visit for the latest version: https://github.com/synereo/dockernode

## Synereo Backend Dockerfiles

Dockerfiles for easily setting up a Synereo node and the following instruction are for building backend from the source code (This takes sometime to build i.e. around 30-40 minutes). If want to use existing Docker image (preferred method) then use the image from Docker hub. 

  [https://hub.docker.com/r/livelygig/backend/](https://hub.docker.com/r/livelygig/backend/)
  
MongoDB is required to run a standalone node. MongoDB and RabbitMQ are required to run a full node.

## Prerequisites

  * git client installed and git command in path
  * docker installed (https://www.docker.com/) and running (start Docker Quick Terminal. Make a note of the default IP address assigned when starting up Docker and for example, default IP address may be 192.168.99.100). Using  [Kitematic](https://docs.docker.com/kitematic/) is very helpful. On Linuxes with modern kernels, such as Arch Linux, you can just use plain [Docker](https://wiki.archlinux.org/index.php/Docker)
  * mongodb running version: 2.6.4 (https://www.mongodb.com/) but it worked with the latest version. Follow the instruction below if want to use Docker image. 

    `docker pull mongo` then 
    `docker run --name mdb1 -p 27017:27017 -d mongo`
  - rabbitmq running version: 3.0.2 erlang version : 5.9.1 (15B03) (http://www.rabbitmq.com/) but works with the latest version by editing rabbitmq.config file (add this entry [{rabbit, [{loopback\_users, []}]}] ). Follow the intruction below if want to run Docker image. (https://hub.docker.com/_/rabbitmq/) 

    `docker pull rabbitmq` then 
    `docker run --name rabbitmq1 -p 4369:4369 -p 5671:5671 -p 5672:5672 -p 25672:25672 -d rabbitmq`

## Source files
Download files in a directory of your choice or use command as below to build Docker image (make sure docker is running and available). Windows users, run "git config --global core.autocrlf false" command before running the git clone command otherwise container may fail to execute properly.

    1. git clone https://github.com/synereo/dockernode.git SpliciousBKND

## Build docker image using: 
Run the following command 

    2a. cd SpliciousBKND
    2b. docker build -t spliciousbkendimage . 

  Use "spliciousbkendimage" as image name in subsequent steps where image id is required. You can use image name of your choice but it must be all lowercase. 
 
## Running docker image - manual process: 

    3a. docker run -it -e MONGODB_HOST=IP_ADDRESS -e MONGODB_PORT=27017 --name SpliciousBKEND -p 8888:9876 spliciousbkendimage /bin/bash
  
At the # command prompt
    
    3b. cd /usr/local/splicious
    3c. ./run.sh start
  
## Running docker image - automated process: 
Please replace the IP_ADDRESS appropriately.

    3a. docker run -it -e MONGODB_HOST=IP_ADDRESS -e MONGODB_PORT=27017 \
              --name SpliciousBKEND -p 8888:9876 -d spliciousbkendimage \
              /usr/local/splicious/run.sh
  
To see log files, go to /usr/local/splicious/logs folder after login in.

## Accessing container:

Visit the webpage http://<docker_quick_terminal_assigned_IP>:8888/agentui/agentui.html?demo=false and if this don't work then find the mapping URL (ipaddress:port from Kitematic screen - select your container there i.e. SpliciousBKEND). For example, you may see the access URL like 192.168.99.100:8888 then access backend using http://192.168.99.100:8888/agentui/agentui.html?demo=false

The default user name/password is admin@localhost/a and can be changed in /usr/local/splicious/eval.conf file

See screenshot 
https://drive.google.com/open?id=0B1NrzDY6kx1JTzdPNVFlU19xekk

## Running MongoDB:


    
## Running RabbitMQ:

     docker pull rabbitmq
     docker run --name rabbitmq1 -p 4369:4369 -p 5671:5671 -p 5672:5672 -p 25672:25672 -d rabbitmq


## Running a complete node (including MongoDB and RabbitMQ)
Copy eval.conf file (https://github.com/synereo/gloseval/blob/1.0/eval.conf) into a Docker host folder and will map this folder later on. Update the following keys/values in eval.conf file appropirately:

- Update with remote RabbitMQ node IP Address: `DSLCommLinkServerHost`, `DSLEvaluatorPreferredSupplierHost` and  `BFactoryCommLinkServerHost`

- Update with local RabbitMQ IP Address: `DSLCommLinkClientHost`, `DSLEvaluatorHost`, `DSLEvaluatorPreferredSupplierHost` and `BFactoryCommLinkClientHost`

After updating ip addresses, run the following command in a sequence: 

    docker run -it --name mdb1 -p 27017:27017 -d 
    docker run --name rabbitmq1 -p 4369:4369 -p 5671:5671 -p 5672:5672 -p 25672:25672 -d rabbitmq 
    docker run -it --link mdb1:mongo --link rabbitmq1:rabbitmq -v /Users/n/tmp/dockerspliciousconfig:/usr/local/splicious/config -e MONGODB_HOST=192.168.99.100 -e MONGODB_PORT=27017 -e DEPLOYMENT_MODE=distributed -p 8888:9876 --name backendNode -d livelygig/backend /usr/local/splicious/run.sh

## Other notes:
To access UI from outside of the docker host, you would need to map the dockerhost ip/port to docker guest ip/port in Virtual Box (Network -> Port Forwarding) by adding rules.

To save a container to be used as an image

    docker commit [container_id] [name_in_lowercase]

To save an image to use in different docker installation

    docker save [name_in_lowercase] > [directory_location_to_save]/[image_name].tar

To load an image created in different docker installation 

    docker load < [image_name].tar

Running with the latest RabbitMQ version - edit rabbitmq.config file by adding [{rabbit, [{loopback_users, []}]}] to provide "guest" user access. This file mostly will be non existent and read more about RabbitMQ access control [here](https://www.rabbitmq.com/access-control.html)

