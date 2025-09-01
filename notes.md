Optimization:
===================
docker login -u lakshmi315
cd roboshop-docker

for i in $(ls -d */);
do cd $i;
name=$(basename "$i);
docker build -i lakshmi315/$name:v1 . ;
docker push lakshmi315/$name 

this is build image and push images
base name means it removes the / in names it gives  only names

1. use minimal official images
catalogue --> FROM node:20-alphine3.21
here we are using minimal image
-v host-dir:container-dir
/usr/share/nginx/html ----> nginx html dir

un-named/un-manged volumes:
===============================
it we create dir and manage it then those are called un-named/un-manged volumes
if docker creates and dir and manag then those are called named/ managed volumes