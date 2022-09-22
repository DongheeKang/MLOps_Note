# Docker/Kubernetes


## Contents

* [Docker container](#docker)
* [Kubernetes](#kubernetes)



<br/><a name="docker"></a>

# Docker container

The underlying technology

    Docker is written in Go and makes use of several kernel features to deliver the functionality we’ve seen.

Namespaces

    Docker takes advantage of a technology called namespaces to provide
    the isolated workspace we call the container. When you run a container,
    Docker creates a set of namespaces for that container.
    This provides a layer of isolation: each aspect of a container runs
    in its own namespace and does not have access outside it.
    Some of the namespaces that Docker uses on Linux are:

    The pid namespace: Used for process isolation (PID: Process ID).
    The net namespace: Used for managing network interfaces (NET: Networking).
    The ipc namespace: Used for managing access to IPC resources (IPC: InterProcess Communication).
    The mnt namespace: Used for managing mount-points (MNT: Mount).
    The uts namespace: Used for isolating kernel and version identifiers. (UTS: Unix Timesharing System).

Control groups

    Docker on Linux also makes use of another technology called cgroups or control groups.
    A key to running applications in isolation is to have them only use the resources you want.
    This ensures containers are good multi-tenant citizens on a host.
    Control groups allow Docker to share available hardware resources to containers and,
    if required, set up limits and constraints. For example, limiting the memory available
    to a specific container.

Union file systems

    Union file systems, or UnionFS, are file systems that operate by creating layers,
    making them very lightweight and fast. Docker uses union file systems to provide
    the building blocks for containers. Docker can make use of several union file system
    variants including: AUFS, btrfs, vfs, and DeviceMapper.

Container format

    Docker combines these components into a wrapper we call a container format.
    The default container format is called libcontainer. In the future, Docker may support
    other container formats, for example, by integrating with BSD Jails or Solaris Zones.

### Docker procedure

1. Pulls the ubuntu image:

    Docker checks for the presence of the ubuntu image and,
    if it doesn’t exist locally on the host,
    then Docker downloads it from Docker Hub.
    If the image already exists,
    then Docker uses it for the new container.

2. Creates a new container:

    Once Docker has the image, it uses it to create a container.

3. Allocates a filesystem and mounts a read-write layer:

    The container is created in the file system and
    a read-write layer is added to the image.

4. Allocates a network / bridge interface:

    Creates a network interface that allows the Docker container
    to talk to the local host.

5. Sets up an IP address:

    Finds and attaches an available IP address from a pool.

6. Executes a process that you specify:

    Runs your application,

7. Captures and provides application output:

    Connects and logs standard input, outputs and errors for you
    to see how your application is running.


### Docker image

* Docker registry

      $ docker login                         : docker hub
      $ docker login docker-registry.io      : private registry 

* pull images

      $ docker pull hub.westeurope.cloud//debian_9_jenkins:stable
      $ docker pull hub.westeurope.cloud//debian_9_jenkins

      $ docker pull ubuntu:latest
      $ docker pull alpine:latest
      $ docker pull debian:8

* list of images

      $ docker images

### Starting a Shell in the Alpine Docker Container

* run vs exec

      docker run  — will start up a new container and run a process within that new container
      docker exec — will execute a command in a container is already running.

      $ docker run -it --rm --name myubuntu ubuntu:latest
      $ docker run -it -d --name myubuntu -e OS_ENV=container ubuntu:latest
      $ docker run -it alpine /bin/sh
      $ docker run -itd --name centos centos:latest

      $ docker exec -it myubuntu /bin/sh
      $ docker exec -it myubuntu /bin/bash
       
      -it             : interactive mode, to keep the standard input attached to the terminal
      --rm            : run a docker container and remove it once the process is complete
      -p 8080:80      : port
      -b              : Starts the container in the background. Recommended.
      -d              : detached container, detached container will stop when the root process is terminated

* install vi

      Debian
      $ docker exec -it myubuntu bash -c "apt list --installed | grep vim"
      $ docker exec -it ubuntu bash -c "apt-get update && apt-get install -y vim"

      Centos
      $ docker run -itd --name centos centos:latest
      $ docker exec -it centos bash -c "rpm -qa | grep vim"
      $ docker exec -it centos bash -c "yum update -y && yum install -y vim"

      Start vi 
      $ docker exec -it centos bash
      $ vi /baeldung.txt   
      
      or with one liner
      $ docker exec -it centos bash -c "vi /baeldung.txt"

### After starting docker, do configure it

* first check container:

      $ docker container ls -a

* download docker image, if it not exist:

      $ docker pull hub.cloud/debian_9_jenkins:stable
      $ docker pull hub.cloud/debian_9_jenkins

* run it:

      $ docker run -it hub.cloud/debian_9_jenkins:stable
      $ docker run --rm -ti --name debian_7_kl hub.cloud/debian_7_jenkins:latest bin/bash

* attach to an already running container:

      $ docker exec -it 81a69a4cb059 /bin/bash

* after running docker, set standard configuration inside debian_9
    - set .ssh/id_rsa    (copy from local)

          chmod 600 id_rsa
    - set gitconfig      (copy from local)

          git clone, pull, and push in the container mode

    - set virtualenv and pip-tools

          virtualenv --no-download --python=python3.6 venv
          source venv/bin/activate
          pip install pip-tools

    - set standard setting

          export LC_ALL=C.UTF-8
          export LANG=C.UTF-8

* need to do above all the develeopment in the container environment
  use VSCode with container mode, not in MacOS!



### Running Docker without sudo
* situtation

      $ docker run hello-world  
        ....
        connect: permission denied. See 'docker run --help'

      $ id donghee                         
        uid=1000(donghee) gid=1001(example_group) groups=1001(example_group),27(sudo)     
      $ group donghee

      $ sudo groupadd docker
      $ sudo usermod -aG docker donghee
      $ su - donghee

      $ docker run hello-world

### Docker monitoring 

      $ docker ps -a
      $ docker inspect -f '{{.State.Status}}' mycontainer
      $ docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' 1bd9e76f808a
      $ docker stats --no-stream
       
      - Docker lifecycle states:
        created - running - exited 

### Docker CMD Directive

      $ vi Dockerfile
        FROM ubuntu:latest
        CMD echo -e "\ttest1"; echo test2
      $ sudo docker build -t example1 .
      $ sudo docker run -it example1
            test1
        test2

      $ vi Dockerfile
        FROM ubuntu:latest
        CMD ["/bin/bash", "-c", "echo -e '\ttest1'; echo \$HOME"]
      $ sudo docker build -t example2 .
      $ sudo docker run -it example2
            test1
        /root

      $ vi Dockerfile
        FROM ubuntu:latest
        COPY startup.sh .
        CMD ["/bin/bash","-c","./startup.sh"]
      $ vi startup.sh
        #! /bin/bash
        echo $HOME
        date
      $ sudo docker build -t example3 .
      $ sudo docker run -it example3
        /root
        Tue Aug 16 08:15:25 UTC 2022


### Restart a stopped docker container

* scenario

      start 
        $ docker run -d --name mycontainer mycontainer
      stop
        $ docker stop mycontainer
      restart
        $ systemctl restart docker         <--- it doesn't allow to restart

* solution

      1st method 
        $ docker restart mycontainer
        $ docker start mycontainer
      
      2nd method 
        $ docker run -itd --restart=always --name mycontainer centos
        $ docker run -itd --restart=always --name mycontainer centos:7 sleep 5
        $ systemctl restart docker

### Connecting from docker containers to resources in the Host
our goal is to make the host and the containers (DB & API) share the same networking!

* Let's check 

      $ ifconfig                  : network interfaces list for a host with Docker installed

      docker0   Link encap:Ethernet  HWaddr 02:42:A7:6A:EC:A9  
                inet addr:172.17.0.1  Bcast:172.17.255.255  Mask:255.255.0.0
      ...
      eth0      Link encap:Ethernet  HWaddr 00:15:5D:40:01:0C
      ...  
      lo        Link encap:Local Loopback
      ...  
     
* DB will be connected with docker in the same network, DB configuration will have bind-address

      bind-address = 172.17.0.1              <----- can be found in the DB configuration
      $ mariadb -h 172.17.0.1

* docker setup

      By default, Docker will create a bridge network. This default network doesn’t allow the containers to connect to the host. So, we’ll need to use '--network host'. Now, the localhost address (127.0.0.1) will be referencing the localhost interface of the host, instead of the one of the container. Therefore, we can access our MariaDB – from the container – just by connecting to localhost:

      $ docker run --rm -it --network host alpine sh                          : 
      $ mariadb -h 127.0.0.1

https://www.dongheekang.com/linux/nginx-docker-container


### Anaylze Disk Usage of a Docker Container

* Test setup first: two nginx docker containers

      $ docker image pull nginx:alpine
      $ docker container run --rm -it --name web-server-01 -d nginx:alpine
      $ docker container run --rm -it --name web-server-02 -d nginx:alpine

      - create and attch a volut to the container
      $ docker volume create web-server-vol 
      $ docker container run --rm -it --name web-server-03 -v web-server-vol:/shared-volume -d nginx:alpine

      $ docker image list                  : 1      
      $ docker container ls -a             : 3
      $ docker volume list                 : 1

      $ docker container ls --size

      $ docker exec -it web-server-03 sh
      docker03> cd shared-volume/
      docker03> fallocate -l 2G sample-file.img        : create 2GB volume
      docker03> exit

      $ docker system df --verbose

### Docker error bind: address already in use
* Scenario 1 : "address already in use" 

      $ docker run -itd -e POSTGRES_USER=donghee -e POSTGRES_PASSWORD=password \
                   -p 8080:5432 -v /data:/var/lib/postgresql/data \ 
                   --name postgresql-donghee postgres
        ....           
        Unable to find image 'postgres:latest' locally
        ....
        Error starting userland proxy: listen tcp 0.0.0.0:8080: bind: address already in use

* Scenario 2 : "port is already allocated" 

      $ docker run -itd -e POSTGRES_USER=donghee -e POSTGRES_PASSWORD=password \
                   -p 8080:5432 -v /data:/var/lib/postgresql/data \ 
                   --name postgresql-donghee postgres
      ....
      Bind for 0.0.0.0:8080 failed: port is already allocated.

* Solution: free up port or remove by netstat / ss -tnlp

      $ docker rm -f  postgresql-donghee              <---- remove DB first 
      $ lsof -i:8080
        COMMAND     PID  USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
        java      78812 root   66u  IPv6 0xe28ed26f1f48e597      0t0  TCP *:http-alt (LISTEN)
      $ 
      $ kill 78812
      $ docker run -itd -e POSTGRES_USER=donghee -e POSTGRES_PASSWORD=password \ 
                        -p 8080:5432 -v /data:/var/lib/postgresql/data \
                        --name postgresql-donghee postgres


### Assigning a port mapping to a running docker container



### Docker Setup nginx server (2 ways)
* first method) 

      $ vi Dockerfile 
        # Pull the minimal Ubuntu image
        FROM ubuntu
        # Install Nginx
        RUN apt-get -y update && apt-get -y install nginx
        # Copy the Nginx config
        COPY default /etc/nginx/sites-available/default
        # Expose the port for access
        EXPOSE 80/tcp
        # Run the Nginx server
        CMD ["/usr/sbin/nginx", "-g", "daemon off;"]

- build & run

      $ docker build . -t haidar/server
      $ docker run -d -p 80:80 haidar/server


* second method) using offcial image

      $ docker pull nginx
      $ docker run -d --name server -p 80:80 nginx
      $ docker ps
      CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                               NAMES
      816532f135c8   server    "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, :::80->80/tcp   nginx

* Advanced

      $ docker image pull nginx:alpine

### Docker setup for MongoDB
* using offcial image

      $ docker pull mongo

* or docker-compose

      $ vi docker-compose.yml
        version: '3.3'
        services:
            mongo:
                ports:
                    - '27017:27017'
            container_name: dkrcomp-mongo
            restart: always
            logging:
                options:
                    max-size: 1g
            environment:
                - MONGO_INITDB_ROOT_USERNAME=mongoadmin
                - MONGO_INITDB_ROOT_PASSWORD=bdung
            image: mongo

      $ docker-compose up
      $ docker ps | grep dkrcomp-mongo

* To access either below or pycharm!

      $ sudo apt install mongodb-clients -y
      $ docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' 1bd9e76f808a
      $ mongo --host 172.18.0.2 --port 27017

* (optitona) MongoDB
      > use admin
      > db.auth('mongoadmin', 'password')
      > use dkrcomp-comp
      > db.articles.insert({"name":"Author1"})
      > show dbs
      > db
      > show collections
      > db.articles.find()  


### How to use multiple databases with docker-compose

* Export schema and data with mariaDB

      This one backup contains both databases

      > mariadb-dump --skip-add-drop-table --databases db1 db2 > databases-backup.sql
      
      ...
      CREATE DATABASE /*!32312 IF NOT EXISTS*/ `db1`
      USE `db1`;
      CREATE TABLE `table1` ( ...
      ...
      INSERT INTO `table1` VALUES ( ...
      CREATE DATABASE /*!32312 IF NOT EXISTS*/ `db2`
      USE `db2`;
      ...
      CREATE TABLE `table2` ( ...
      ...
      INSERT INTO `table2` VALUES ( ...

* docker-compose

      $ vi docker-compose.yml
        version: '3.8'
        services:
            db:
                image: mariadb
                environment:
                  MYSQL_ALLOW_EMPTY_PASSWORD
                volumes:
                    - databases:/var/lib/mysql
                    - ./databases-backup.sql:/docker-entrypoint-initdb.d/databases-backup.sql
            app:
                build: ./app
                ports:
                    - 80:80
        volumes:
            databases:

* start 

      $ docker-compose up
      $ mariadb -h db

* (optitona) MariaDB
      
      > USE db1;
      > SELECT * FROM table1;
      > USE db2;
      > SELECT * FROM table2;


### Docker configuration for a single-replica Prometheus service

      $ docker service create --replicas 1 --name my-prometheus \
        --mount type=bind,source=/tmp/prometheus.yml,destination=/etc/prometheus/prometheus.yml \
        --publish published=9090,target=9090,protocol=tcp \
        prom/prometheus


### Docker compose 

* command set

      $ docker-compose up -d
      $ docker-compose down
      $ docker-compose start
      $ docker-compose stop
      $ docker-compose build
      $ docker-compose logs -f db
      $ docker-compose scale db=4
      $ docker-compose events

      $ docker-compose exec db bash
      $ docker-compose run byflow bash   

* example


* 



### Docker clean up

    docker system prune
    docker system prune --volumes

    docker container ls -a
    docker container rm cc3f2ff51cab



<br/><a name="kubernetes"></a>

# Kubernetes

* Deployment
  
    $ kubectl create -f ./load-balancer-example.yaml
    $ kubectl get deployments
    $ kubectl rollout status deployment/nginx-deployment
    $ kubectl describe deployments
    $ kubectl get pods --show-labels


<div><br/>
&raquo; Back to <a href="#contents">Contents</a> | <a href="../docs/README.md">Docs</a>
</div>
