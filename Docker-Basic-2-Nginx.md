# Docker Nginx

## Pre-requisite

A DockerHub account  
Log in on playground:

 - https://labs.play-with-docker.com/

Line with # mark a command line command

## Nginx - Run and bind

### Overview

In this section, you will: 
 - Pull an existing image
 - Run a container on a port
 - Stop a container
 - Run a container with a binded volume
 - Launch a web server capable of serving static files

### Command used

 - docker ps: list existing container
 - docker pull: retrieve an image from a docker repository
 - docker run: launch a container with the specified image
 - docker stop: Stop the specified container

### Hands on

    # docker pull nginx
    # docker run -it --rm -d -p 8080:80 --name web nginx

-i + -t keep STDIN open, if if container is detached + allocate a terminal  (shorthand for --interactive and --tty)  
--rm Automatically remove the container when it exits  
-d Activate daemon mode. the container will run as a background task (shorthand for --detach)  
-p publish a container, and allow setup port forwarding on the server. (shorthand for --publish)  
--name To identify a container via name rather than its ID
  
full list of option: <https://docs.docker.com/engine/reference/commandline/run/>

    # docker stop web

Create a index.html that the nginx will use

    # mkdir website
    # cd website
    # touch index.html
    # vi index.html 
    - Press l
    - type <h1>Hello World!</h1>
    - Press ESC
    - Enter ":wq" to save and exit (:w to save, :q to exit)
    # cat index.html
 
 Run the container with a volume attached (the volume is persistent storage that will not be destroyed with the container), in this case, the volume will contain index.html

    # docker run -it --rm -d -p 8080:80 --name web -v ~/website:/usr/share/nginx/html nginx
    
 (-v ~/website:/usr/share/nginx/html - bind the folder ~/website to the folder /usr/share/nginx/html)

 Navigate to your 8080 port with the right IP and you should see Hello World!
