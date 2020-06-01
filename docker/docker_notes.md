# Docker notes

## Basics

Images are the filesystem based container. They're readonly (think class)
container is instance of image. Stateful (think object)


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

'ssh' into instance:

```
# get container id
$ docker ps
$ docker exec  -it 63e357bf14a0 bash
```


remove image:

```
$ docker rmi ImageID
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
MAINTAINER edwin
RUN apt-get update
RUN apt-get install -y nginx
```



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




## Querying remote docker repo / hub

```
docker images <docker registry>/<image>
```

## Dockerhub "official" image library

https://github.com/docker-library





