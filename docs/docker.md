# Docker

#### What is docker?

 Docker is an open source containerization platform designed to facilitate and simplify application development. It is a tool for creating, deploying, and running applications easily. It allows us to package our applications with all the dependencies, and distribute them as individual bundles. Docker guarantees that our application will run in the same way on every Docker instance.

 Two important concepts:
 - Images
 - Containers

A Docker **image** represent an application and its virtual environment at a specific point in time. You need to declare a file (Dockerfile) that contains the source code, libraries, dependencies, tools, and other files needed for an application to run. 

**Containers runs images**. These containers are compact, portable units in which you can start up an application quickly and easily.
 You can create, start, stop, move, or delete a container using Docker API or CLI. You can connect a container to one or more networks and attach storage/volumes to it.

To make a docker image, you have to write a script in a file called 'Dockerfile'. Yes, the name is without extension. Example:

```dockerfile
# Dockerfile
FROM node:16-alpine3.14 
ENV APP_HOME /training 
WORKDIR $APP_HOME
COPY . $APP_HOME 
CMD ["node"]
```
FROM: is used to set the base image. It uses a docker image base hosted in dockerhub. In this example it's a linux system called alpine with ruby 3.  
ENV: is used to set an environment var.  
WORKDIR: The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile. If the WORKDIR doesn’t exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.  
COPY: is used to copy files from a directory in your system to the image. Here it copies our files from the directory where we are executing the comand to the workdir.  
CMD: This command will be executed when we run an image into a container. It executes irb. IRB stands for "interactive Ruby" and is a tool to interactively execute Ruby expressions read from the standard input.

You are going to run a container with this docker image. 

Please, create a folder 'dockertutorial' with the Dockerfile example above explained.

Open a terminal, and go inside the folder.  
First you need to build the image with a tag (node-training):
```bash
docker build -t node-training .
```

Run a container with this image:
 ```bash
 docker run -it node-training
 ```
And it will open an irb!  
If you write 2 + 2, it returns 4 :P
If you want to exit, just write exit.

If you want to execute a different command than irb, you can make it explicit in the run command (and it avoids the command defined in the Dockerfile image).  
For example if you want to open a terminal (/bin/ash):
```bash
docker run -it node-training /bin/ash
```

And you are in a linux system terminal.
You can list the folders and files with ls command
```bash 
ls
Dockerfile          
```

You can create a folder with the command mkdir:
```bash 
mkdir test_folder
```

And then if you execute ls again:
```bash 
ls
Dockerfile       test_folder
```
test_folder was created! But only into this container. What does it mean? Well, let's get to it.
Execute exit to exit/finish terminal.

If you run a new container:
```bash
docker run -it node-training /bin/ash
```

and execute ls again:
```bash 
ls
Dockerfile        
```
test_folder is not here!!  
This is because the container runs the image that we built (defined in Dockerfile), and the image doesn't have the test_folder. When we created the folder, it was into a specific container and whatever we made into the container affect only this container and not the image. This is why we say that image is immutable -- once built, it’s unchangeable, and if you want to make changes, you’ll get a new image as a result --

If you want to change something in the container folder that it impacts in your own system folder, you can do it with **volumes**.  
We will explain volumes later.

Using the docker images command, we can view a list of images we have available in our filesystem:
```bash 
docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
node-training             latest               0562baf13e54       About an hour ago   60MB
node                 3.0.1-alpine3.13     d46c4b238695       6 months ago        60MB
```

Using the docker ps command we can check if our image is running:
```bash
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
9a802817e38f   documentation_docsify   "docker-entrypoint.s…"   13 minutes ago   Up 13 minutes   0.0.0.0:4005->4005/tcp, :::4005->4005/tcp   documentation_docsify_1
```

If we execute docker ps with the option -a we can list stopped containers:

```bash
docker ps -a
CONTAINER ID   IMAGE                   COMMAND                  CREATED          STATUS                      PORTS                                       NAMES
351ca3b1d260   documentation_docsify   "docker-entrypoint.s…"   18 seconds ago   Exited (0) 15 seconds ago                                               documentation_docsify_run_717c38a3a5c7
9a802817e38f   documentation_docsify   "docker-entrypoint.s…"   15 minutes ago   Up 15 minutes               0.0.0.0:4005->4005/tcp, :::4005->4005/tcp   documentation_docsify_1
```

We can execute docker container prune to clean stopped containers
```bash
 docker container prune

 WARNING! This will remove all stopped containers.
 Are you sure you want to continue? [y/N] y
 Deleted Containers:
 351ca3b1d260c6b3980fc38a469979e4de7e5cfecbb3d1dbf660625d1bc13b11

 Total reclaimed space: 5B
```
##### Docker Volumes

#### What is docker-compose

 Docker Compose is a tool for running multi-container applications on Docker. You need to define a docker-compose.yml. A Compose file is used to define how one or more containers (defined as services) make up your application.

a docker-compose.yml example:
```bash
version: "3.7"
services:
  node-training:
    build:
      context: .
      dockerfile: Dockerfile
    command: /bin/ash -c "echo hello world"
    volumes:
      - .:/training
```
It defines only one service: node-training.
*build: it defines the folder (the dot means that the folder is where the docker-compose.yml is) where it will find the dockerfile and the dockerfile name.
*command it will be executed when you up the service (it replace the command defined in the dockerfile)
*volumes: it will link your folder (where the docker-compose.yml is) with the /training folder when the service is up with docker-compose. It works as a symbolic link. If you create, edit, remove or do something with a file inside the /training on the container, it will be reflected in the defined volume (your folder).


Build the services:
```bash
docker-compose build
```

Run the services:
```bash
docker-compose up
```

It will create the default network for all services, then it will create services defined, and execute the command in each service. When the command exits, all containers are stopped.
```bash
Creating network "challenges_default" with the default driver
Creating challenges_node-training_1 ... done
Attaching to challenges_node-training_1
node-training_1  | hello world
challenges_node-training_1 exited with code 0
```



#### Useful commands

```bash
docker images 
```
```bash
docker-compose build
```
```bash
docker-compose run --rm node-training 
```
```bash
docker-compose up node-training
```
```bash
docker-compose down
```
```bash
docker exec -i recess_db_1 pg_restore -U postgres -v -d DATABASE_NAME < ../local-backup-database_jan_10_2021-202109281455
```
```bash
docker exec -it recess_api_1 /bin/bash
```
```bash
docker images
```
```bash
docker image rm ID
```
```bash
docker container ls
```
```bash
docker container rm ID
```
```bash
docker volume ls
```
```bash
docker volume rm
```
