# Docker 101

## **Docker Overview**
* On a high over, the components Docker consists of are:
  * Dockerfile
    * A text file containing instructions (commands) to build a Docker image.
  * Docker Image
    *  A snapshot of a container, created from a Dockerfile. Images are stored in a registry, like Docker Hub, and can be pulled or pushed to the registry.
  * Docker container
    * A running instance of a Docker image.
  * 

## **Dockerfile**
* Image environment blueprint
* It marks the building blocks of an image 
* `FROM` 
  * A dockerfile always starts with this
  * It specifies on which other image it is built
  * e.g. `ubuntu` or `python`
  * This installs ubuntu or python in the containerddd
* `ENV`
  * Optionally define environment variables
  * Better practices to define these in the docker compose 
* `RUN` 
  * Run any bash command 
  * `RUN mkdir -p /home/app`
  * It executes on the containers
* `COPY`
  * This executes on the HOST
  * This should NOT contain the absolute path
* `CMD ["node", "server.js"]`
  * Run a command inside the container
  * e.g. start server.js with node 
  * It is the entrypoint command
  * There is just one CMD command, while there can be multiple RUN commands

Creating docker images
* To create a web server container app:
  * Add OS 
  * Update packages
  * Install dependencies
  * Install python dependencies using `pip`
  * copy source code to /opt
  * run web server using `flask`


## **Docker images**
* After creating a dockerfile, an image can be created
* `docker build` - is the base command for this
* `-t` - add a tag or image name 
* The command is always executed against the directory in which the dockerfile exists (along with the app code etc)
* A docker image is often nothing more than a specific version of an application
* `docker images` 
  * List images
* `docker pull` 
  * Download image
* `docker rmi`
  * Remove image
* `docker build -t <name>`
  * Build image


## **Docker containers**
### **Creating Docker containers**
* After creating the Docker image, the image can be used to spin up one or more instances of it
  * aka containers
* This is done with `docker run`
  * This downloads an image if it hasn't been already, creates and starts the docker container
  * `docker run nginx` - This runs an instance of nginx 
  * This can be run also without having a custom image 
  * In that case an image is downloaded from the Docker Hub 
    * This is a central repository for docker images
  * By default, it always uses the latest version of the application 
    * To make it explicit, run `docker run nginx:latest`
    * Alternatively, you can specify any version of the application
    * Online, on the docker hub page for the application a list is provided with all possible versions

### **Managing Docker containers**  
* By default the container will run attached to the terminal
  * This means that any output of the application will be directed to the user's terminal
  * However, this output is not interactive, but purely informative, and that the terminal cannot be used besides monitoring the logs coming in
  * To exit this view press ctrl+C
    * This will also stop the containers
* `-d` - Run a container detached 
* Containers are designed to exit after their process has completed
  * For instance, a container running the application running nothing more than a `sleep 5` script, will complete after 5 seconds
  * After this the container will exit, or shutdown, and will no longer be running
* `docker ps` - will output all running containers
* `docker ps -a` will output all containers, running or not 
* `docker rm <container name>` will remove a container
* `docker rmi <image>` will remove the image of a container 
  * `docker image rm <image>` is an alternative way of removing an image
* To interact meaningfully with a container that uses ports (web server, database), ports on the host must be exposed to the service
  * `docker run -p <host port>:<container port>` will take care of this
    * `docker run -p 80:80 nginx` will spin up a container of nginx running on port 80
    * Then, nginx can be accessed from any device on the network using the container host's IP address
    * Otherwise, the webserver will only be accessible from the host itself, using the docker container's IP address
    * See Networking below
* A container's data is non-persistent by default, meaning it will not survive reboots or the container being destroyed
  * To make it persistent, volumes must be mapped to the host
  * `docker -v /path/to/host/directory:/container/path/to/container/directory`
  * This way you can edit and use files on the host and the container will use them for its purposes
  * See data persistence below
* To run a detached container and print the docker logs output run:
  * `docker logs <container>`
  * `-f` - flag to follow the logs as the appear 
* To interact with the container run
  * `docker exec -it <container> <command>` - the flag stands for interactive terminal
  * To go inside the container, run it with the `sh` command

**More docker run flags**
* `docker run -e` - run the container with added environment variables
* `docker run --rm` - Remove the container when it exists, rather than just exiting/ stopping


## **Docker Networking**
* **Default**
    * Bridge
    * None
    * Host
  * Bridge is used when no network is specified
  * Otherwise, specify the network by running `docker run Ubuntu --network=none/host`

  * **Bridge**
    * Private internal network 
    * Isolated from the host network 
  * **Host**
      * Directly attached to the host network
      * No isolation
      * Ports are immediately exposed on the host 

  * **None**
      * Completely isolated 
      * No network

* **User-defined networks**
  * You can also create multiple, separate networks for some docker containers to share
  * `docker network create --drive bridge --subnet 182.18.0.0/16 custom-isolated-network`
  * `docker network ls` - list them
  * `docker network connect <network> <container>`
    * Connect connect to network

## **Embedded DNS**
* Containers on the same network can always resolve each other's container name
* They have an internal DNS on  127.0.0.11

## **Docker Storage**
### Overview
* When Docker is installed, a folder structure is created on the host
* `/var/lib/docker` - so this is on the host
* Here docker data is stored related to images, containers, volumes
* **Layered architecture**
  * When a image is built it is built on a layered structure
  * e.g. OS, apt packages, pip packages, source code being copied, updating the entrypoint
  * Each layer is stored separately 
  * **note** These layers can be interchangebly used, meaning creating them for container A, they can be also used for container B
    * No need to download and store them twice
    * Saving disk space and speeding things up
    * This also means updating is quicker 
  * All these layers are also called **image layers**
  * They are **read only**
  * To build a container from this docker file, run `docker build`
    * This creates a new layer - the **container layer** 
    * This layer is **read write**
    * All interaction with the container, happens in this laye
    * Adding files, adds files to this layer
    * This does not mean files, like code files added to containers in the underlying layers, cannot be changed
    * When editing these files, docker copies them to the container layer where they can be written to 
      * **copy on write mechanism**
      * 
### **Data persistence**
* Containers are ephemeral by default, which means any data stored in the container will be lost once it is terminated
* To overcome this, there are two ways:
  * **Volume Mounts**
  * **Bind mounts**
* **Volume Mounts**
  * One way is by making a persistent volume or **volume mounting**
      * `docker volume create data_volume_name`
        * This creates the volume called data_volume_name in `/var/lib/docker/volumes`
      * To run a container with this volume run `docker run -v data_volume:/var/lib/mysql mysql` - for mysql specifically   
* **Bind mounts** 
    * A volume mount is mounted from the volume directory anywhere specified on the host's file system  
    * `docker run -v /data/mysql:/var/lib/mysql mysql`
    * This mounts the volume to the `/data/mysql`
    * Better yet, `docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql`  
    * Both do the same, but the latter is preferred
* **Storage drivers**
  * This is the crucial element enabling this mounting of volumes and making storage persistent
  * Some popular storage drivers are:
    * AUFS
    * ZFS
    * BTRFS
    * Device Mapper
    * Overlay
    * Overlay2
  * This is dependent on the underlying Linux distribution 
  * This is always known as Union Filesystems, or UnionFS. 
  * Docker chooses this automatically
* **Docker volume commands**
  * `docker volume ls`
    * List volumes
  * `docker volume create <name>` 
    * Create volume
  * `docker run -v <volume>:/data <image>`
    * Run docker image with a mounted volume 

* **cgroups**
  * Linux kernel features that limit and manage system resources like CPU, memory, and I/O for process groups
  * Docker uses cgroups to enforce resource constraints on containers, ensuring predictable performance and preventing containers from consuming excessive system resources.

## Docker security
### Docker security commands
* `docker scan <image>`
  * Scan image for issues
* `docker history <image>`
  * Get image change log
* `docker inspect <container>`
  * Get container details
* `docker run --read-only <image>`
  * Run in read-only mode

## Cleaning up Docker
* `docker system prune -a`
  * Remove all unused data (containers, images, volumes)
* `docker image prune -a`
  * Remove all unused images
* `docker container prune`
  * Remove stopped containers
* `docker volume prune`
  * Remove unused volumes

## **Docker compose**
* Docker compose is a way of creating config files in yml format 
* A powerful aspect of Docker compose is that you can spin up multiple containers all in one go
  * Rather than running `docker run` for the DB, the frontend, and the backend, all these components of the same app can be defined in a single file and be treated/ managed as a single entity
* Starting a file with `services:` creates a bridged network between all the containers specified in the file 
  * When `services:` would not be specified, links between the containers should be explicitly mentioned with the `links:` array
* Networks can also be explicitly created in a docker compose file
``` 
networks:
    front-end:
    back-end:
```

* This creates two networks, one called front-end, another called back-end
* **Essential docker compose commands**
  * `docker compose up`
    * Build and start a container attached to the terminal
  * `docker compose up -d`
    * Build and start a container detached from  the terminal
  * `docker compose --project <name> up -d` 
    * Give a project name to the docker file
    * By default it will use the name of the directory 
  * `docker compose -p <name> up -d`
    * same but shortcut
  * `docker compose ls`
    * List all projects
  * `docker compose pull`
    * Pull updated image versions from the registry 
  * `docker compose ps`
    * Show running services

### Resource limits
* In a docker compose file, resource limits can be provided 
* **limits** are the limit which a container can use
* **reservations** are the initial allocation
```
services:
    redis:
        image: redis:alpine
        deploy:
            resources:
                limits:
                    cpus: '0.50'
                    memory: 150M
                reservations:
                    cpus: '0.25'
                    memory: 75M
```


## **Docker registry** 
* Docker central repo for all images
* When `docker run nginx` is run, an image is pulled from this repo
* Such image is always comprised of a Registry, User and Image 
  * The User and Image together are called **Account Repository**
* `nginx` above stands for `nginx/nginx` where it is both the user and image 
  * Not specifying both, assumes that both are the same
  * Not specifying the registry, makes docker assume it is the Docker registry
* Other popular registries include `gcr.io`
* Registries can also be private


## **Docker engine**
* It is the bread and butter of Docker 
* It is the core of Docker that is installed on a host
* Actually installing the Docker Engine on a host consists of installing three pieces of software
  * Docker CLI
    * The command line interface that the user uses to manage Docker
  * REST API
    * Facilitates tools (like the CLI) talking with the daemon
    * This could also be on another host
      * `docker -H=remote-docker-engine-IP:2375`
  * Docker Daemon
    * The element that manages containers, images, networks, and volumes     
  
## **Namespaces**
* An element that is used to isolate workspaces
* Process IDs, networks, mounts, interprocesses, and unix timesharing are created in each namespace
  * This is important because on both the host and the container there will be a process id 1
  * With namespaces this is mapped to a higher process ID 
  * E.g., running an nginx container will show nginx with `pid 1` inside the container
    * On the host, nginx will run on a higher process id 
  * 