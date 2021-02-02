# Docker Registry

## Pre-requisite

A DockerHub account  
Log in on playground:

 - https://labs.play-with-docker.com/

Line with # mark a command line command

## Docker image saving

### Overview

In this section, you will: 
 - Save an image of a container
 - Tag an image
 - Push an image to a registry
 - Check that the image has been pushed

### Command used

 - docker ps: List all running container
 - docker container commit: Create a new image from a container
 - docker tag: Apply a tag to an image
 - docker image push: Push an image to a repository
 - docker image ls: List all image available
 - docker login: login into a repository manager
 - docker push: upload an image to the current account logged in

---

### Hands on:

Commit the container you want to save as an image, fetch the id then commit using the id with a name of your choice
    
    # docker ps 
    # docker container commit <container_id or container_name> <name>:<tag>
    ex: docker container commit ba92003184b3 apache-hello-world-test:firsttry

Choose a name different from your base image because otherwise, it will overwrite the original image of the same name  
If :tag is omitted, the default tag will be set to latest  
A base image can have multiple subimage with different tag (apache-hello-world:firsttry, apache-hello-world:latest...)  
The new image that you generated reflect the current state of your machine, whatever you did after the start of the container  
For example, you can have a custom base centos image that you then build on top of with a dockerfile  
  
If you omit the tag but you dont want latest as a tag, use

    # docker tag <container_id or container_name (SOURCE_IMAGE)> <alias with different name or tag (TARGET_IMAGE)>
    ex: docker tag apache-hello-world-test:firsttry echo00/apache-hello-world-test:firsttry

This command just creates an alias (a reference) by the name of the TARGET_IMAGE that refers to the SOURCE_IMAGE. That’s all it does. It’s like assigning an existing image another name to refer to it. Notice how the tag is specified as optional here as well  
The image is now stored locally, we want to push it to our dockerHub repository, with docker image ls, you can see that you now have an image  (username/image name) with whatever tag you put

Log in your dockerhub account

    # docker login --username=echo00

The commit your image

    # docker push echo00/apache-hello-world-test:firsttry

Note that if you've changed the tag, you will need to specify it otherwise, docker will try to push an image named with the tag latest (echo00/apache-hello-world-test:latest)  
  
Check your dockerhub account repositor and your image should appear <https://hub.docker.com/repositories>  
You and everyone (if it's public) have now access to this image, make sure not to put credential in it! 