## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <a href="https://workshops.emanuelciuca.com/docker/pre-requisites">Pre-requisites</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-build">Building a docker image</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-run">Running a docker container</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-monitoring-and-debug">Monitoring and debugging containers</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-volume">Working with volumes</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-network">Inter-container communication</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-multi-stage-builds">Multi-stage builds</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-push">Pushing a container to a registry</a>

## Pushing a container to a registry

In order to push a docker image to a registry we must use the docker tag to give it a spcific name.

> An image name is made up of slash-separated name components, optionally prefixed by a registry hostname.
>  If not present, the command uses Docker’s public registry located at registry-1.docker.io by default.

More info about docker names here [here](https://docs.docker.com/engine/reference/commandline/tag/#extended-description)

Knowing this let's rename the image so we can push it to our Docker Hub account.

First, we need the docker image id

```cmd
$ docker images

REPOSITORY        TAG                 IMAGE ID            CREATED             SIZE
greetings-ws      multi-stage         90f3fda0d811        38 minutes ago      375MB
greetings-ws      mysqldb             faf22c3694ef        9 hours ago         377MB
greetings-ws      h2db                67b05c56fbfb        10 hours ago        375MB
greetings-ws      latest              67b05c56fbfb        10 hours ago        375MB

```

Let's tag `67b05c56fbfb`

```cmd
$ docker tag 67b05c56fbfb eciuca/greetings-ws:latest
$ docker images

REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
greetings-ws             multi-stage         90f3fda0d811        38 minutes ago      375MB
greetings-ws             mysqldb             faf22c3694ef        9 hours ago         377MB
greetings-ws             h2db                67b05c56fbfb        10 hours ago        375MB
greetings-ws             latest              67b05c56fbfb        10 hours ago        375MB
eciuca/greetings-ws      latest              67b05c56fbfb        10 hours ago        375MB
```

Now let's try to push it

```cmd
The push refers to repository [docker.io/eciuca/greetings-ws]
f1a3c342ffe5: Preparing 
11cca9d19539: Preparing 
a44b2646cf7d: Preparing 
1bfeebd65323: Preparing 
denied: requested access to the resource is denied

```

Before we can push an image to DockerHub we need to authenticate against it like this"

```cmd
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: eciuca (Press Enter)
Password: <password here> (Press Enter)
Login Succeeded
```

Let's try to push again

```cmd
$ docker push eciuca/greetings-ws:latest

The push refers to repository [docker.io/eciuca/greetings-ws]
f1a3c342ffe5: Pushed 
11cca9d19539: Pushed 
a44b2646cf7d: Mounted from library/openjdk 
1bfeebd65323: Mounted from library/openjdk 
latest: digest: sha256:723f268067ca84038a5be2edc52808ce9eacdaa48600dcb12ec4aa49ccde4847 size: 1159
```

As you can see docker is not pushing an image as a whole, it is pushing all the layers that make that image.
Some of the layers exist already on Docker Hub so it shows this with the message `Mounted from ...`

If we go now to https://hub.docker.com/r/eciuca/greetings-ws we can see that the image is available on DockerHub and anyone can use it. 
