## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <a href="https://workshops.emanuelciuca.com/docker/pre-requisites">Pre-requisites</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-build">Building a docker image</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-run">Running a docker container</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-monitoring-and-debug">Monitoring and debugging containers</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-volume">Working with volumes</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-network">Inter-container communication</a>
* <span>**Multi-stage builds**</span>
* <a href="https://workshops.emanuelciuca.com/docker/docker-push">Pushing a container to a registry</a>

## Multi-stage builds

Before we continue let's go back to our Java project and switch to a different branch

```cmd
$ git checkout multi-stage
(make sure you're on this branch)
```

Let's see how our `Dockerfile` looks now

```dockerfile
FROM openjdk:13-alpine

ENV EXECUTABLE_JAR=greetings-webservice-0.0.1-SNAPSHOT.jar
ENV APP_LANG_TEMPLATES_LOCATION=/greeting_templates

WORKDIR /app
COPY target/$EXECUTABLE_JAR /app

VOLUME /greeting_templates
VOLUME /app_data

CMD java -jar $EXECUTABLE_JAR
```

If we take a look at it we see that the 5th instruction we can see that to build the image we always need to have the project build already.
This means that we need to have to correct Maven and Java versions installed on the PC where the image is built.
We are going to hit again the 'It works on my machine' issue.

Couldn't we build the project in a docker container also? :-)

```dockerfile
FROM maven:3.6-jdk-13

WORKDIR /workspace
COPY src /workspace/src/
COPY pom.xml /workspace/

RUN mvn clean package

ENV EXECUTABLE_JAR=greetings-webservice-0.0.1-SNAPSHOT.jar
ENV APP_LANG_TEMPLATES_LOCATION=/greeting_templates

WORKDIR /app
RUN cp /workspace/target/$EXECUTABLE_JAR /app

VOLUME /greeting_templates
VOLUME /app_data

CMD java -jar $EXECUTABLE_JAR
```

Let's try to build it

```cmd
$ docker build -t greetings-ws:with-maven-build .
```

It should end with build success anywhere we run it as long as we have access to internet and maven central is up :-)

Everything works now but something happened, our image size is bigger.

```cmd
$ docker images

REPOSITORY       TAG               IMAGE ID            CREATED              SIZE
greetings-ws     multi-stage       38e448ae5924        About a minute ago   648MB
greetings-ws     mysqldb           faf22c3694ef        8 hours ago          377MB

```

It size is bigger because now we have the sources and maven binaries in our image.

How could we create the `Dockerfile` in such a way that we only keep the executable jar and use the smallest java image base

```dockerfile
FROM maven:3.6-jdk-13 AS greetings-build

WORKDIR /workspace
COPY src /workspace/src/
COPY pom.xml /workspace/

RUN mvn clean package

FROM openjdk:13-alpine

ENV EXECUTABLE_JAR=greetings-webservice-0.0.1-SNAPSHOT.jar
ENV APP_LANG_TEMPLATES_LOCATION=/greeting_templates

WORKDIR /app
COPY --from=greetings-build /workspace/target/$EXECUTABLE_JAR /app

VOLUME /greeting_templates
VOLUME /app_data

CMD java -jar $EXECUTABLE_JAR
```

Let's check our new image

```cmd
$docker images

REPOSITORY       TAG               IMAGE ID            CREATED              SIZE
greetings-ws     multi-stage       90f3fda0d811        10 seconds ago       375MB
greetings-ws     mysqldb           faf22c3694ef        8 hours ago          377MB
```

It looks like it is a little bit smaller than the mysqldb image version also :-) 
