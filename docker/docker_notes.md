# Docker notes

## Images

* **Images** are the filesystem based container. They're readonly (think class)
* **Container** is instance of image. Stateful (think object)


pull image from registry:

```
docker image pull  postgres
```

list installed images:


```
$ docker image ls
```


run default command on docker image:

```
docker run imagename
```

remove image:

```
$ docker rmi ImageID
```

## Containers

'ssh' into a container:

```
# get container id
$ docker container ps
$ docker container exec  -it 63e357bf14a0 bash
```


Docker leaves stopped container lying around. To clear them all out:


```
docker container prune
```

pause vs stop vs kill:

* pause is like SIGSTOP, will us cgroups freeze function. processes won't get any signal
* stop  is like graceful shutdown
* kill  is non-graceful shutdown


stop all containers:

```
docker ps -q | xargs docker stop
```
 

## Building images

Example

in Dockerfile:

```
# pull from 'ubuntu' base image
FROM ubuntu
COPY srcfiledir /opt/app/
RUN apt-get update
RUN apt-get install -y nginx

```

### COPY

Copies files from the build context into the image

* If <dest> doesn't end with a trailing slash, it will be considered a regular file and the contents of <src> will be written at <dest>.
* If <dest> doesn't exist, it's created, along with all missing directories in its path.


### RUN

Executes a command in the build phase and creates a new image layer.

Syntax has two forms:

* RUN ["executable","param1","param2"] (exec form)
* RUN command param1 param2 (shell form)






Build:

```
$ docker build -the myimage:0.1 .
```

Run: 

```
$ docker run myimage:0.1
```

## Working with images

see SHA digests of images

```
$ docker images --digests
```

download image:
```
$ docker pull <image>
```

But more convenient to use `run` since it will pull first if not found locally

```
$ docker run <image>   # will pull it from registry if not found locally
```

## Running images

Applications in docker images can be long running services or one-off commands.
In the case of one-off commands, the container will stop after command completed.

You can 'enter' a container but running it in interactive mode:

```
$ docker run -it --entrypoint /bin/bash <image>
```


## Querying remote docker repo / hub

```
docker images <docker registry>/<image>
```

## Dockerhub "official" image library

https://github.com/docker-library



## Base image tags


* alpine - micro alpine linux, apk, musl (WARN: musl c has limitations!)
* slim - small (100MB) debian distro, apt, glibc
* stretch, jessie - mediam (200MB), older/stable debian, apt, glibc
* buster, bullseye - medium (200MB), newer debiam, apt, glibc



## Docker best practices

### Managing secrets in docker

**NOTE**: it's not safe to do the following
* copying secrets to the docker image as part of the build process 
* referencing secrets via environment variables
All of these values can be recovered via docker inspect commaneds.


In stead, the only way to manage secrets is via multi-stage builds.



Multi-stage builds: https://docs.docker.com/build/building/multi-stage/


### Use .dockerignore files to filter out undesired files

Example file:

```
# Don't include any files that might have secrets
**/.env

# Don't include any of the test databases we use during development
**/*.sqlite3

# Don't include any test or temporary files
**/*.log
**/coverage/*
**/test/*

# We don't need documentation in our Docker image
**/*.md
```


### Use multi-stage builds to build binaries inside the Docker image
If you build a binary outside your container and then COPY/ ADD the dependency in during build time, it will be more difficult for you to support multiple architectures.

docker buildx supports command line arguments to build images that simultaneously support multiple architectures.

To make use of buildx multi-arch support, build dependencies/binaries in an early step of a multi-stage build process and then COPY them in during later steps.

This will ensure your binary is built ‘natively’ for each architecture you chose.


### Keep the images small


For speed.

### Set the EXPOSE directive in your Dockerfile
Use EXPOSE directive to document both for your team and for the Docker engine which ports your process binds to within the container.

```
EXPOSE 8080/tcp
```


