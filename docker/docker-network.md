## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <a href="https://workshops.emanuelciuca.com/docker/pre-requisites">Pre-requisites</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-build">Building a docker image</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-run">Running a docker container</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-monitoring-and-debug">Monitoring and debugging containers</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-volume">Working with volumes</a>
* <span>**Inter-container communication**</span>

## Inter-container communication

Before we continue let's go back to our Java project and switch to a different branch and build the project and the image again

```cmd
$ git checkout mysqldb
(make sure you're on this branch)

$ Linux: ./build.sh | Windows: build.bat
(wait for build to complete)

$ docker build -t greetings-ws:mysqldb .
```

Let's run a container

```cmd
$ docker run -d -p 8080:8080 --name crazy-cow greetings-ws:mysqldb
                                                              ^
                                                              |
                                                  be sure to specify the tag
```

```cmd
$ docker ps
<container is not present>

$ docker logs -f crazy-cow
```

As we can see the container failed to start because it cannot connect to the database.

Then let's spin up a database container from Docker Hub: https://hub.docker.com/_/mysql

```cmd
$ docker docker run --name mysqldb -e MYSQL_DATABASE=greetings -e MYSQL_ROOT_PASSWORD=greetings -d mysql:8
```

Now let's try to run the application container again

```cmd
$ docker run -d -p 8080:8080 --name crazy-cow greetings-ws:mysqldb
```

Strange, it still cannot connect to the database even though they're both on localhost, or ... maybe not?

### Network isolation!

When a docker container starts only the ports mapped to the host are accessible. 
When a computer tries to go to localhost it actually goes to its own localhost. So how can 2 containers communicate?

Two or more containers are accessible by their **names** and can communicate only if they are in the same **overlay network**.

Let's create a docker overlay network

```cmd
$ docker network create greetings-network
```

To run a docker container in a network we need to specify the ` --network` parameter
 
```cmd
$ docker rm mysqldb crazy-cow (cleanup the old containers)

$ docker run -d --name mysqldb --network=greetings-network -e MYSQL_DATABASE=greetings -e MYSQL_ROOT_PASSWORD=greetings mysql:8

$ docker run -d --name crazy-cow --network=greetings-network -e MYSQL_HOST=mysqldb -p 8080:8080 greetings-ws:mysqldb
```

If you run now 
```cmd
$ docker logs -f crazy-cow
```

The logs should look fine. 
You can go to http://localhost:8080/greetings/new?name=CrazyCow to give it a try
