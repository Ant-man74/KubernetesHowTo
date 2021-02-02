# Docker DockerFile

## Pre-requisite

A DockerHub account  
Log in on playground:

 - https://labs.play-with-docker.com/

Line with # mark a command line command

## DockerFile

### Overview

In this section, you will: 
 - Create a dockerfile
 - Build an image from a dockerfile
 - execute a container and connect to it
 - execute a web server and serve a file defined in a dockerfile on port 80
 - connect to the server and change its content at runtime

### Command used

 - docker ps: list existing container
 - docker run: launch a container with the specified image
 - docker stop: Stop the specified container
 - docker build: build an image from a dockerfile
 - docker image ls: List all available built images
 - docker exec: Run a command in a machine (sh will open a prompt)
 - docker container run: Run a command in a container from an image
 - docker rm: delete the specified container 

---

### Hands on: Part 1

Create the dockerfile

    # cat > Dockerfile
    - type or paste the following

    FROM alpine 
    MAINTAINER jean-luc@hidora.com

    RUN apk update && apk add nano && apk add curl
    ENV ABC=123
    
Line by line:
 
 - From alpine: From the image "alpine" in the repository either local or remote
 - Maintainer (deprecated): Info available in docker ps, display the name of the author
 - RUN ...: run the following command in the container
 - ENV ...: Set the environnement variable

After this,

    - exit with ctrl + c
    # docker build -t alpine-abc .
    # docker image ls

-t (shorthand for --tag) - Tag the build with a name that follow  
note that uppercase is not allowed in image name

The image defined in the dockerfile is now built, an image tagged alpine-abc is now available to use as a base docker image to run  
You can start and access a command line using the container run command, and see that there is an env variable ABC containing 123

    # docker container run -it alpine /bin/sh
    # echo $ABC
    # exit

Exiting the command line with exit will stop the container, you can keep it running by starting it in detached mode (-d), and check that the container is running using ps, then connect to it using exec

    # docker container run -it -d alpine-abc /bin/sh
    # docker ps
    # docker exec -it <name> sh

The name you use in "name" is either the one specified in the container run or the one generated and listed in the docker ps

    # echo $ABC
    # exit

You will need to stop the container manually, otherwise it will keep running

    # docker stop <name>
    # docker ps

---

### Hands on: Part 2: centos and Apache server

Create the dockerfile and a directory "website" containing a index.html file

    # cat > Dockerfile-apache
    - type or paste the following

    FROM centos
    RUN yum -y install httpd
    COPY website/index.html /var/www/html/
    EXPOSE 80
    CMD ["usr/sbin/httpd", "-D", "FOREGROUND"]

Line by line:
 
 - From centos: From the image "centos" in the repository either local or remote
 - RUN yum ...: yum is a package manager (like apt, nugget or npm) to install software, here httpd is apache
 - Copy ...: Copy the local file at website/index.html to /var/www/html/, you can also copy folder
 - EXPOSE 80: Open the port 80 of the machine
 - CMD ["executable","param1","param2"]: Execute the executable with parameter

After this,

    - exit with ctrl + c
    # docker build -t apache-hello-world -f Dockerfile-apache .
    # docker image ls

-f (shorthand for --file) allow to build a specific dockerfile instead of the default Dockerfile
note the dot at the end of the command, it's the folder that is necessary for the command to run

    # docker run -it -d -p 8080:80 --name apache apache-hello-world

After the server start, you should be able to see the hello wolrd on the port 80 of your IP

To access the server inside the container

    # docker exec -it apache sh
    # cd /var/www/html
    # vi index.html
    - Add some text ex:
    type under </h1>: <p>Inside the machine</p>
    - Press ESC
    - Enter ":wq"

Wait for a bt for apache to reload the file, after a while (a minute) your page at port 80 should update with the appropriate text

---

### Note

A stopped container is not deleted and can be restarted at anytime
If you want to rebuild a container with the same name you yill need to delete the container with the existing name.
Simply stopping the container is not enough, you will need the rm command

    # docker rm <name>