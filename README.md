# Image Optimization

## Login to Docker Hub

```bash
docker login -u lakshmi1092
```

---

## Go to the Project Directory

```bash
cd roboshop-docker
```

---

## Build and Push All Images

```bash
for i in $(ls -d */)
do
    cd "$i"
    name=$(basename "$i")
    docker build -t lakshmi1092/$name:v1 .
    docker push lakshmi1092/$name:v1
    cd ..
done
```

### Explanation

- `ls -d */` → Lists all directories in the current location.
- `cd "$i"` → Changes to each application directory.
- `basename "$i"` → Removes the trailing `/` and returns only the directory name.
- `docker build -t lakshmi1092/$name:v1 .` → Builds the Docker image and tags it.
- `docker push lakshmi1092/$name:v1` → Pushes the image to Docker Hub.
- `cd ..` → Returns to the parent directory and continues with the next directory.

> **Note:** `basename` removes the trailing `/` from the directory name and returns only the directory name.

# Stateless vs Stateful Applications

We have three tiers:

- Web Tier
- Application Tier
- Database Tier

---

## Stateless Applications

- Applications where the data is **not important to persist** are called **stateless applications**.
- Even if the container is removed, we can recreate it from the application source code (for example, from Git).
- Since no important data is stored inside the container, volumes are not required.

**Examples:**

- Web Tier
- Application Tier

---


## Stateful Applications

- Applications where the data is **important and must be preserved** are called **stateful applications**.
- If the container is removed, the data should not be lost.
- Therefore, volumes should be created for stateful applications.

**Example:**

- Database Tier

---

## Data Storage

- The data can be stored on the server or any persistent storage location.
- The database stores the actual application data in tables and columns.
- The application reads the data from the database and displays it to the user.

where to store the data from official image:
--------------------------------------------

docker login -u lakshmi1092/

/data/db
we can check:
docker volume
docker volume create nginx
docker volume ls
docker inspect nginx

we will details stored details
/var/lib/docker/volumes/nginx/_data ----> it create the volumes floder ans stored the data

docker run -d -v nginx:/usr/nginx/share/nginx/html -p 80:80 nginx

nginx: ---> created volume
/usr/share/nginx/html ---> This is the directory inside the container where the volume will be mounted.

docker exec -t <id> bash
cd /usr/share/nginx/html/
echo "hello" > hello.txt
exit
we can remove the <id>
sudo su -
cd /var/lib/docker/volumes/nginx/_data
here we can see our files
we can run:
docker run -d -v nginx:/usr/nginx/share/nginx/html -p 80:80 nginx
Here diff container but same dir
docker compose run ----applicaton will delete bu volumes are not delete
docker compose up -->
include volumes in compose file:
----------------------------

docker compose run -d
docker compose down
# still volumes are not removed
# next remove all images
docker rmi `docker images -a -q `----> to remove all images

now we will descrese the image size:
catalogue: FROM node:20-alphine3.21
docker build -t lakshmi1092//catalogue:v1 .
docker push lakshmi1092//catalogue:v1
docker exec -it catalogue sh --> for alpine
cd ..
docker compose up -d --> recreating 
docker images
now the image size is decreased 

use minimal images
use official images
run it as non root ---> complted

multi stage build:
=================
now we will decraese the size  of memory
multi stage builds introduce multiple stages in your Dockerile each with specfic purpose

multi stage builds are keeping multiple docker files in a single docker file,
One docker file use it as builder and another docker flle we can use it as final image 
we can copy what you want from builder to final image.
this reduces the memory un nesaray instalation

cd catalogue
docker build -t lakshmi1092//catalogue:v1 .
docker images
cd ..
docker compose up -d

optimised docker layers:
=========================
cd users
DOCKER_BUILDKIT=0 docker build -t lakshmi1092/user:v1 --no-cache .

OUTPUT:
---------
FROM node:20(1st intrscution) ----> pull ----> from this it will create a container(intermediate contnaer) ---> on top of this it will run second command (WORKDIR /opt/server/) -----> from this again it creates the image (c10b18d0862b) ----> from this image again it run the container(63f71edeecde) ------> same process for remaing .....

If you trying push image another time it will not push the entrie code beacuse it conatin all layers.
docker push -d lakshmi1092//user:v1

all intermediate layer are deleted and it gives final moutput

------------------------------------
docker best practices:
==========================
minimum and offical images
multi stage builds
optimising layers and combining RUN commands
non root containers
use customized networks
implementing volumes
COPY over ADD
docker igonre not load everything into docker
impelementing health cheks
limiting resources
getting secreat from secreat manager
implementing volumes

cd catalogue/
docker build -t lakshmi1092//catalogue:v1
docker login -u lakshmi1092
docker push lakshmi1092/catalogue:v1
docker compose up -d
docker ps
docker images

for i in cart catalogue user ; do cd $i; docker build -t lakshmi1092//$i:v1 . ; docker push lakshmi/$i:v1 ;cd ..;   done
docker images
docker compose up -d
docker restart frontend


source code ---> complie ----> bytecode(intermediate language).
for develping this source code we need jdk
JDK ---> java development kit
jdk ---> no need of developement env(runs bytecode)
in java we did everything using maven

Docker Architecutre:
===================
client ---> docker CLI where we can run our docker commands
host ----> where docker is running, docker deamon(continousy running)
repos ---> local and central repo

what happen when we run 
docker run nginx
----------------
1. 1st it checks the image is in local or not
2. if exstis then it will create the conatiner
3. if not exsit then it wil pull from registry and  create the conatiner and send the o/p to client
4. they are docker volumes and networkig we can configure 

Disadvanatges
==============
auto scaling: There is no deafult auto scaling methods
load blancing: no load blancing components to blance the traffic b/w the containers
reliablity: If container crashes it will not automatically restart(no self healing)
what if docker host crash: all conatiner are goes down
what about storage: if docker host crashes we loose data also beacuse docker is manging volumes on the same host
networking is in bridge mode, if you have multiple docker hosts bridge host will not work
