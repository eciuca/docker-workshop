## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <a href="https://workshops.emanuelciuca.com/docker/pre-requisites">Pre-requisites</a>
* <span>Building a docker image</span>

## Building a docker image

### First, we need to create a file named `Dockerfile` with the following contents

```
# the base image
FROM openjdk:13

# sets up the working (this is where all subsequent commands will execute)
WORKDIR /app

# copy the files from the host inside the image
COPY target/greetings-webservice-0.0.1-SNAPSHOT.jar /app

# command executed when the container starts
CMD java -jar greetings-webservice-0.0.1-SNAPSHOT.jar
```

Let's look at the command output. The first thing you can notice are the 4 steps being executed:
```
Step 1/4 : FROM openjdk:13
13: Pulling from library/openjdk
Digest: sha256:22bda09e13ca9b3f951ad028b2c20b375d1792a55036097e7b9aa9eaede1a675
Status: Downloaded newer image for openjdk:13
 ---> e9ee535ba1b1
Step 2/4 : WORKDIR /app
 ---> Running in 236da3c84646
Removing intermediate container 236da3c84646
 ---> 83605da6c53f
Step 3/4 : COPY target/greetings-webservice-0.0.1-SNAPSHOT.jar /app
 ---> 1841358158c7
Step 4/4 : CMD java -jar greetings-webservice-0.0.1-SNAPSHOT.jar
 ---> Running in e65656cd8a33
Removing intermediate container e65656cd8a33
 ---> 99db7fcab4c3
Successfully built 99db7fcab4c3
Successfully tagged greetings-ws:latest
```

### Let's have a closer look at the command output

Because the docker client cannot find the openjdk image locally it downloads it from the registry and after it is downloaded the image digest is printed (you can verify this digest on Docker Hub, if you really want to double check docker's integrity check). The last thing it prints at this stage is a Status.

```
Step 1/4 : FROM openjdk:13
13: Pulling from library/openjdk
Digest: sha256:22bda09e13ca9b3f951ad028b2c20b375d1792a55036097e7b9aa9eaede1a675
Status: Downloaded newer image for openjdk:13
```

### Docker image layers

The building blocks of a docker image are the image layers. A layer contains the differences between the previous and the current layer. All the layers are read-only, except the last one.

After each build step you can notice that docker prints `Removing intermediate container <hash>`. In order to execute some commands docker needs to create intermediate containers. When the build step execution is finished the intermediate container is removed and replaced by a read-only layer (intermediate).

The layers that will make up the image are printed on the lines starting with `--->`.

```
Step 2/4 : WORKDIR /app
 ---> Running in 236da3c84646
Removing intermediate container 236da3c84646
 ---> 83605da6c53f
Step 3/4 : COPY target/greetings-webservice-0.0.1-SNAPSHOT.jar /app
 ---> 1841358158c7
Step 4/4 : CMD java -jar greetings-webservice-0.0.1-SNAPSHOT.jar
 ---> Running in e65656cd8a33
Removing intermediate container e65656cd8a33
 ---> 99db7fcab4c3
```

### The output

The last 2 lines print the last layer (container layer) hash and the image name and tag.

```
Successfully built 99db7fcab4c3
Successfully tagged greetings-ws:latest
```
