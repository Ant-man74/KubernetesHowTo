# Docker cheatsheet

## The best command

 - docker ps: list existing container
 - docker pull: retrieve an image from a docker repository
 - docker run: launch a container with the specified image
 - docker stop: Stop the specified container
 - docker build: build an image from a dockerfile
 - docker image ls: List all available built images
 - docker exec: Run a command in a machine (sh will open a prompt)
 - docker container run: Run a command in a container from an image
 - docker rm: delete the specified container 

 ## Deploy an image
 
 - docker container commit: Create a new image from a container
 - docker tag: Apply a tag to an image
 - docker image push: Push an image to a repository
 - docker login: login into a repository manager
 - docker push: upload an image to the current account logged in