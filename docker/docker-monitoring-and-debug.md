## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <a href="https://workshops.emanuelciuca.com/docker/pre-requisites">Pre-requisites</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-build">Building a docker image</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-run">Running a docker container</a>
* <span>**Monitoring and debugging containers**</span>
* <a href="https://workshops.emanuelciuca.com/docker/docker-volume">Working with volumes</a>

## Monitoring and debugging containers

> As with any piece of software there are a lot of things that can go wrong with a container also. 
> To ease our suffering docker provides a set of utilities to have a better view of what happens with the container

### Know everything with one command

```cmd
$ docker inspect crazy-cow
```
See output [here](https://workshops.emanuelciuca.com/docker/docker-inspect-output.json). This command will provide every bit of information about how the container was set up except the running processes

### Displaying application logs

```cmd
$ docker logs crazy-cow

... (trimmed log for readability)
2019-11-25 20:02:38.320  INFO 1 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2019-11-25 20:02:38.715  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2019-11-25 20:02:38.720  INFO 1 --- [           main] c.e.w.d.GreetingsWebserviceApplication   : Started GreetingsWebserviceApplication in 7.606 seconds (JVM running for 8.51)

```

As it is, the command will display the latest log entries and exit. If you want to tail (follow) the logs you can add the `-f` argument like this `docker logs -f crazy-cow`

### Displaying running processes inside the container

```cmd
$ docker top crazy-cow

PID       USER    TIME    COMMAND
15030     root    0:39    java -jar greetings-webservice-0.0.1-SNAPSHOT.jar
```

### Displaying current usage statistics

```cmd
$ docker stats crazy-cow

CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
28b68a0c15c6        crazy-cow           0.51%               222.5MiB / 4.833GiB   4.50%               1.39kB / 0B         0B / 0B             38
```
These statistics will be printed as a continuously until `CTRL + C` is pressed

### Executing a terminal inside the container

```cmd
$ docker exec -it crazy-cow /bin/bash
```

This is the equivalent of ssh-ing into another machine. From there you can go anywhere you want inside the container and check if everything is as expected

### Killing a container

```cmd
$ docker kill crazy-cow
```

Because that's what you do to cattle when they don't serve you anymore :)
