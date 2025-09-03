Optimization:
===================
docker login -u lakshmi315
cd roboshop-docker

for i in $(ls -d */); do
  cd "$i"
  name=$(basename "$i" /)
  docker build -t lakshmi315/$name:v1 .
  docker push lakshmi315/$name:v1
  cd ..
done

this is build image and push images
base name means it removes the / in names it gives  only names

1. use minimal official images
catalogue --> 

=======================================
-v host-dir:container-dir
/usr/share/nginx/html ----> nginx html dir

un-named/un managed volumes:
=============================
if we create dir and manage it then those are un named / un managed volumes
if docker creates volumes and managed them theyare named and managed volumes

stateless vs statefull
============================
we have web tier,appliction tier, db tier 

where the data is imp is called statefull applications.
where the data is not imp like web and app we canget the data from git so this not imp data this is called stateless 

volumes should be created to statefull applications ---db tier
The data can be stroed in server/any location. Database store the actual data in host directories.
while rendering for user it will show tables and columns

where to store the data from official image:
--------------------------------------------

docker login -u lakshmi315

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

docker compose run 
docker rmi `docker images -a -q `----> to remove all images

now we will descrese the image size:
catalogue: FROM node:20-alphine3.21
docker build -t lakshmi315/catalogue:v1 .
docker push lakshmi315/catalogue:v1
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
docker build -t lakshmi315/catalogue:v1 .
docker images
cd ..
docker compose up -d

optimised docker layers:
=========================
cd users
DOCKER_BUILDKIT=0 docker build -t lakshmi315/catalogue:v1 --no-cache .

OUTPUT:
---------
FROM node:20(1st intrscution) ----> pull ----> from this it will create a container(intermediate contnaer) ---> on top of this it will run second command (WORKDIR /opt/server/) -----> from this again it creates the image (c10b18d0862b) ----> from this image again it run the container(63f71edeecde) ------> same process for remaing .....

If you trying push image another time it will not push the entrie code beacuse it conatin all layers.
docker push -d lakshmi315/user:v1

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
docker build -t lakshmi315/catalogue:v1
docker login -u lakshmi315
docker push lakshmi315/catalogue:v1
docker compose up -d
docker ps
docker images

for i in cart catalogue user ; do cd $i; docker build -t lakshmi315/$i:v1 . ; docker push lakshmi/$i:v1 ;cd ..;   done
docker images
docker compose up -d
docker restart frontend


source code ---> complie ----> bytecode(intermediate language).
for develping this source code we need jdk
JDK ---> java development kit
jdk ---> no need of developement env(runs bytecode)
in java we did everything using maven

cd shipping
