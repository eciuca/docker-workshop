## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <a href="https://workshops.emanuelciuca.com/docker/pre-requisites">Pre-requisites</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-build">Building a docker image</a>
* <span>**Running a docker container**</span>
* <a href="https://workshops.emanuelciuca.com/docker/docker-monitoring-and-debug">Monitoring and debugging containers</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-volume">Working with volumes</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-network">Inter-container communication</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-multi-stage-builds">Multi-stage builds</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-push">Pushing a container to a registry</a>

## Running a docker container

### The image we created previously can be used to run a container like this

```cmd
$ docker run -p 8080:8080 --name crazy-cow greetings-ws
```

NOTE: if the name argument is not specified docker will assign a random name

### Working with containers 

```cmd
$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
3c1c2adad63a        greetings-ws        "/bin/sh -c 'java -j…"   8 seconds ago       Up 7 seconds        0.0.0.0:8080->8080/tcp   crazy-cow
```

As you can see the current container execution is tied to the terminal session, this means that if we press `CTRL + C` in the current terminal window or we close it, the container will stop.

Lets's do this ...

If you run `docker ps` again you will see that no container appears in the list.

In order to display stopped containers we need to add the `-a` argument to the previous command like this:

```cmd
$ docker ps -a

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                            PORTS      NAMES
3c1c2adad63a        greetings-ws        "/bin/sh -c 'java -j…"   9 minutes ago       Exited (130) About a minute ago              crazy-cow
```

But can we run the container as a daemon so we don't need to keep a terminal session open? Of course! Here's how you do it:

```cmd
$ docker run -d -p 8080:8080 --name crazy-cow greetings-ws
docker: Error response from daemon: Conflict. The container name "/crazy-cow" is already in use by container "81738da2fe91526285508a21e945f969c342c72b16059670d3766229cb11368a". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
```

When running the container as a daemon, if it starts successfully, the container id will be printed in the console, otherwise the docker cli will display an error.

To fix the error above we need to do as docker suggests and remove the container that has the same name

```cmd
$ docker rm crazy-cow
crazy-cow

$ docker run -d -p 8080:8080 --name crazy-cow greetings-ws
28b68a0c15c66fbf415fe45ecda21cbd93bd9471fa7f25cabec245f432d87af4

$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
28b68a0c15c6        greetings-ws        "/bin/sh -c 'java -j…"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp   crazy-cow
```

Now docker is happy and starts the container.

When you run `docker ps` you can see that each container has a `CONTAINER ID` and a `NAME`. Any of these 2 can be used to reference act upon the container with several commands.

You can stop/start a container using the `docker stop/start` commands like this:

```cmd
$ docker stop 28b68a0c15c6
```

A stopped container will not lose its data. If you start it again using 
```cmd
$ docker start 28b68a0c15c6
```
