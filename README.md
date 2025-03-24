# Docker


### What is docker 
Docker is a platform that enables developers to develop, ship, and run applications in containers. Containers are lightweight, standalone, and executable packages that include everything needed to run an application: code, runtime, system tools, libraries, and settings.

### Explain Docker architecture and its components:

docker has 2 components:
* docker-cli
* docker-daemon

![img](img/1.png)


### what is hypervisor
A hypervisor is a software that makes virtualization possible, it divides the host system and allocations the resources to each divided virtual environment, you can basically have multiple os on a single host system.
there are two types of hypervisor:
* type1: it also called native hypervisor or bare metal hypervisor. it runs directly on the underlying host system. It has direct access to your hosts system hardware and hence does not require a base server operating system. Example of  type one hypervisor is : Esxi, hyper-v, kvm.

* type2: this kind of hypervisor makes use of underlying host operating system, is also called hosted hypervisor. Example: viraualbox, vmware-workstation.

### what is virtualization

Virtualization is a technology that allows you to create multiple virtual instances of physical hardware resources, such as servers, storage devices, or network resources, on a single physical machine. These virtual instances, known as virtual machines (VMs) or virtual environments, operate independently from each other and from the underlying physical hardware.


### what is containerization
Containerization is a lightweight form of virtualization that allows you to package and run applications and their dependencies in isolated environments called containers. Containers are self-contained units that include everything needed to run an application, such as code, runtime, system tools, libraries, and settings.

### What is difference between VMs and Docker

![img](img/2.png)


### what is docker image

It is a template that will have all the dependencies along with your application binaries.

### what is docker container
running instance of a docker image, or is an isolated process running in an isolated environment created by cgroups and namespace. the processes running within this container are visible to your host machine, but the process that runs in host machine is not visible in docker container.

### what is cgroups & namespace
namespace provide the container necessary resources like storage, network, file system, ...
cgroups provide resources like RAM, CPU. so with cgroups and namespaces your container are usually created.


### what is container Runtime environment
container runtime environment is also known as container engine which is responsible for creating containers. container Runtime uses cgroups and namespaces to create isolated environment for running containers.



### some important docker container command

```bash

docker --version
docker version
docker info
docker system df
docker system prune
docker volume prune

docker run -tid --name myweb-server -p 80:80 hub.hamdocker.ir/nginx:alpine
docker ps 
docker ps -a
docker logs -f myweb-server
docker stats myweb-server
docker stop myweb-server
docker kill myweb-server
docker rm myweb-server
docker rm -f myweb-server

docker image inspect mydb
sudo docker container inspect -f '{{ .NetworkSettings.Ports }}' mydb
sudo docker container inspect -f '{{ .Config.Env }}' mydb



sudo docker container ls -aq
sudo docker rm -f $(sudo docker container ls -aq)
docker container prune # remove all stopped containers.


docker container run --name mydb -e MARIADB_ROOT_PASSWORD=passwd -e MARIADB_USER=iman --env MARIADB_PASSWORD=imanpass --env MARIADB_DATABASE=zbx -p3307:3306 -d hub.hamdocker.ir/mariadb

docker exec -ti mydb bash
mariadb -u root -p
mariadb -u iman -p
```

```sql
show databases;
use mysql;
show tables;
select user,password,host from user;



CREATE DATABASE school_db;
USE school_db;



CREATE TABLE students (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    enrollment_date DATE NOT NULL
);

CREATE TABLE grades (
    grade_id INT AUTO_INCREMENT PRIMARY KEY,
    student_id INT,
    course_name VARCHAR(100) NOT NULL,
    grade CHAR(1) NOT NULL,
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);


INSERT INTO students (first_name, last_name, enrollment_date) VALUES
('John', 'Doe', '2023-09-01'),
('Jane', 'Smith', '2023-09-01'),
('iman', 'Johnson', '2024-09-02'),
('jack', 'Johnson', '2024-06-01'),
('jafar', 'Johnson', '2022-12-08'),
('sam', 'Johnson', '2021-02-01'),
('bob', 'Johnson', '2025-01-01');

INSERT INTO grades (student_id, course_name, grade) VALUES
(1, 'Mathematics', 'A'),
(1, 'Science', 'B'),
(2, 'Mathematics', 'C'),
(3, 'Mathematics', 'C'),
(5, 'Science', 'C'),
(6, 'Science', 'C'),
(6, 'Science', 'B'),
(7, 'Science', 'B'),
(7, 'Science', 'A');



```

```bash

docker cp <host_address> container_name:/tmp  #copy a file from host to a container
docker cp container_name:/tmp/web.conf .      # copy a file from container to host


sudo docker cp sakila-schema.sql mydb:/tmp
sudo docker cp sakila-data.sql mydb:/tmp
sudo docker exec -ti mydb bash
mariadb -u root -p
CREATE DATABASE sakila;
SHOW DATABASES;
exit
mariadb -u root -p sakila < /tmp/sakila-schema.sql
mariadb -u root -p
use sakila;
show tables;
select * from actor;
mariadb -u root -p sakila < /tmp/sakila-data.sql
mariadb -u root -p
use sakila;
show tables;
select * from actor;
quit
exit






usermod -aG docker <USER>
newgrp docker



# backup
tar -zcvf docker-backup-$(date +%s).tar.gz /var/lib/docker



docker stats
docker top container_name



docker container run --name container_name -e test=devops -e pass=password nginx:latest
docker run --name container_name --env-from=env.txt nginx:latest



```

### docker volume

1. Host Volumes (Bind Mounts)
`Description:` Maps a directory from the host machine directly into the container


```bash
docker run -v /host/path:/container/path nginx

```

2. Named Volumes

`Description:` Volumes with explicit names managed by Docker

```bash
docker volume create mydata
docker run -v mydata:/container/path nginx

```


3. tmpfs Mounts

`Description:` Stores data in host memory only (not persisted to disk)

```
docker run --tmpfs /app/cache nginx

```

4. Volume Drivers (Plugins)

`Description:` Extends Docker volumes with external storage solutions

`Common Drivers:`
* local: Default driver (for named volumes)
* nfs: Network File System volumes
* azurefile: Azure File Storage
* gcepd: Google Compute Engine Persistent Disk
* aws: Amazon EBS/EFS

```bash
docker volume create --driver local \
  --opt type=nfs \
  --opt device=:/path/to/nfs/share \
  --opt o=addr=nfs-server.example.com,rw \
  nfs-volume

```

```bash
# List all volumes
docker volume ls

# Create a named volume
docker volume create my_volume

# Inspect a volume
docker volume inspect my_volume

# Remove a volume
docker volume rm my_volume

# Remove unused volumes
docker volume prune

# Mount a volume in a container
docker run -v my_volume:/path/in/container nginx

# Mount with read-only access
docker run -v my_volume:/path/in/container:ro nginx

```


### docker network

1. Bridge Network
`Description:` The default network driver that creates an internal private network for containers on the same Docker host.

`Use Case:` Best for standalone containers that need to communicate with each other on the same host.

Features:
Containers get automatic DNS resolution by container name
Isolated from the host network by default
Containers get IP addresses from a private subnet (usually 172.17.0.0/16)

```bash
docker run --network bridge -d nginx
```

2. Host Network

`Description:` Removes network isolation between container and host, using the host's network directly.

`Use Case:` When you need maximum network performance and don't need network isolation.

Features:

Container uses host's IP address and ports directly

No NAT overhead

Less secure as container has full access to host network

```bash

docker run --network host -d nginx
```

3. None Network

`Description:` Completely isolates the container from all networks.

Use Case: For containers that don't need any network access (security-sensitive applications).

`Features:`
Only has a loopback interface (no eth0)
Completely isolated


```bash

docker run --network none -d alpine sleep 3600

```

4. Overlay Network

`Description:` Enables communication between containers across multiple Docker hosts (for Swarm clusters).

`Use Case:` Docker Swarm services that need to communicate across multiple hosts.

Features:

Creates a distributed network across multiple hosts

Encrypted by default in Swarm mode

Requires swarm mode to be enabled

5. Macvlan Network

`Description:` Assigns a MAC address to containers, making them appear as physical devices on the network.

`Use Case:` When containers need to appear as physical hosts on your network (legacy applications).

Features:

Containers get their own MAC and IP addresses

Bypasses Docker's network isolation

Requires specific network configuration on host

```bash
docker network ls

docker network create mynet
docker network inspect mynet

docker run -d --name web1 --network mynet hub.hamdocker.ir/nginx 

docker run -d --name web2 --network mynet hub.hamdocker.ir/nginx 

docker run -tid --name busybox1 --network mynet hub.hamdocker.ir/busybox sh


docker exec -ti busybox1 sh
nslookup web1
nslookup web2
nslookup busybox1

docker network connect your-network-name container-name

```


### docker compose
Docker Compose is a tool for defining and running multi-container Docker applications. It allows you to configure all your application's services in a single YAML file and manage them with simple commands.


```bash





```


### docker image

```bash

docker images
docker image ls

docker image save -o mariadb.tar hub.hamdocker.ir/mariadb:latest
ls

scp mariadb.tar root@192.168.1.1:/data/images

docker image load -i mariadb.tar



```










### Setup Harbor 
```sh
wget https://github.com/goharbor/harbor/releases/download/v2.12.2/harbor-offline-installer-v2.12.2.tgz
tar xzvf harbor-offline-installer-version.tgz
cd harbor
cp harbor.yml.tmp harbor.yml
sudo su -
./install.sh

docker compose up -d

# add insecure-registry in /etc/docker/daemon.json
#{
#  "insecure-registries": ["192.168.56.10:80"]
#}


=docker login http://192.168.56.10:80
docker login https://hub.docker.com

# create a project in harbor ui
# tag the container 
docker tag hub.hamdocker.ir/nginx:alpine 192.168.56.10:80/myapps/nginx:v1

docker push 192.168.56.10:80/myapps/nginx:v1

```



### how do you check the total size occupied by docker home

```
docker system df


```

### what is difference between docker stop and kill
docker stop will give 10 seconds of time for stop a container(gracefully terminated) but docker kill imadiately sent SIGKILL(kill -9) a container.


### what is Dockerfile
Its a set of instructions that builds a docker image.


###  what is .dockerignore
when you try to build a docker image from dockerfile, the content in .dockerignore will ignore to send these file to docker daemon.



### what is difference between ADD and COPY
COPY only use local file system but ADD can download from remote server(ADD can be copy a tar or a zip file and untar o r unzip them when copying.)



### what is difference between CMD and ENTRYPOINT
both CMD and ENTRYPOINT defines the starting of your docker container.

### what is difference between RUN and CMD
RUN run when building image, but CMD run when running a container



#### Tip
```

ARG TAG
FROM nginx:$TAG

docker build --build-arg TAG=1.4 -t web:ver1 .
```



### Can we have multiple CMD in Dockerfile
yes, but last one will run when we start a container.



### what is docker network
it will enable networking between your containers. whenever you install docker, docker0(bridge) will create.


```
docker network create net1
docker network create net2

docker network connect net1 container_name

```



### is docker compose is a production tools?
no, the production tools is docker swarm or kubernetes.


### Is it possible use json instead of yaml for writing docker compose?
yes, we can use it. but we have to specify the filename with the following command:

```
docker-compose -f docker-compoes.jso up -d

```