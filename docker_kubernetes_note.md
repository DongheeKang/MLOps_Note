# Docker/Kubernetes


## Contents

* [Docker container](#docker)
* [Kubernetes](#kubernetes)



<br/><a name="docker"></a>

# Docker container




* docker configuration for a single-replica Prometheus service
```
$ docker service create --replicas 1 --name my-prometheus \
    --mount type=bind,source=/tmp/prometheus.yml,destination=/etc/prometheus/prometheus.yml \
    --publish published=9090,target=9090,protocol=tcp \
    prom/prometheus
```





### Doker fundamental

* why chrooot? 
      $ apt-get install coreutils

      $ chroot /tmp/new_root /bin/bash
      $ ldd /bin/bash      

### Setup two dockers for network testing

   ?


### Connecting from Docker Containers to Resources in the Host
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

      bind-address = 172.17.0.1
      $ mariadb -h 172.17.0.1

* docker setup

      By default, Docker will create a bridge network. This default network doesn’t allow the containers to connect to the host. So, we’ll need to use '--network host'. Now, the localhost address (127.0.0.1) will be referencing the localhost interface of the host, instead of the one of the container. Therefore, we can access our MariaDB – from the container – just by connecting to localhost:

      $ docker run --rm -it --network host alpine sh                          : 
      $ mariadb -h 127.0.0.1

https://www.dongheekang.com/linux/nginx-docker-container



### Starting a Shell in the Alpine Docker Container

docker run — will start up a new container and run a process within that new container.
docker exec — will execute a command in a container is already running.

      $ docker run -it --rm --name myubuntu ubuntu:latest
      $ docker run -it -d --name myubuntu -e OS_ENV=container ubuntu:latest
      $ docker run -it alpine /bin/sh
      $ docker exec -it <container-name> /bin/sh
      $ docker exec -it myubuntu /bin/bash


### Setup two dockers for network testing









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
