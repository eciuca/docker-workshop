## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <a href="https://workshops.emanuelciuca.com/docker/pre-requisites">Pre-requisites</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-build">Building a docker image</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-run">Running a docker container</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-monitoring-and-debug">Monitoring and debugging containers</a>
* <span>**Working with volumes**</span>
* <a href="https://workshops.emanuelciuca.com/docker/docker-network">Inter-container communication</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-multi-stage-builds">Multi-stage builds</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-push">Pushing a container to a registry</a>

## Working with volumes

Before we continue let's go back to our Java project and switch to a different branch and build the project and the image again

```cmd
$ git checkout h2db
(make sure you're on this branch)

$ Linux: ./build.sh | Windows: build.bat
(wait for build to complete)

$ docker build -t greetings-ws:h2db .
```

Let's run a container

```cmd
$ docker run -d -p 8080:8080 --name crazy-cow greetings-ws:h2db
                                                            ^
                                                            |
                                                be sure to specify the tag
```

Go to http://localhost:8080/greetings/new?name=CrazyCow (create more by refreshing if you want)

It should create a new greeting with `id: 1` and `name: CrazyCow`. Check it at http://localhost:8080/greetings

LET'S NOW MAKE SOME SACRIFICES >:)

```cmd
$ docker stop crazy-cow 
$ docker rm crazy-cow
```

I'm sorry crazy-cow, please come back to life :-(

```cmd
$ docker run -d -p 8080:8080 --name crazy-cow greetings-ws:h2db
```
Go to http://localhost:8080/greetings. It should return `[]`. Crazy cow is gone #sad #nocow #missyou

### Treat your servers like cattle not pets!

You can find more on the above statement [here](https://devops.stackexchange.com/questions/653/what-is-the-definition-of-cattle-not-pets). 
What this means is that if something doesn't suit your needs anymore, replace it or add more like it.

We saw in the previous sections that we can stop and kill and remove containers. 
Stopping them and then starting them again is safe, we don't lose anything, 
but what if we remove them during a cleanup, then ALL DATA WILL BE LOST.

We can treat containers as cattle, but our data is precious, so how do we make sure we don't lose it.

### Volumes to the rescue

In order to save the data after a container is deleted we need to attach a volume to the container.

First, we need to create it
```cmd
$ docker create volume crazy-cows-soul
```

Then, we just need to attach the volume to the container (delete the previous container before)
```cmd
$ docker stop crazy-cow
$ docker rm crazy-cow

$ docker run -d -p 8080:8080 -v crazy-cow-soul:/app_data --name crazy-cow greetings-ws:h2db
```

Go to http://localhost:8080/greetings/new?name=CrazyCow to create some data

Execute these commands again 

```cmd
$ docker stop crazy-cow
$ docker rm crazy-cow

$ docker run -d -p 8080:8080 -v crazy-cow-soul:/app_data --name crazy-cow greetings-ws:h2db
```

Go to http://localhost:8080/greetings. It should return the data you created during the previous container execution #happy

The docker volumes are backed by some drivers. Volumes created by docker by default are created on the host in a special location.

### Mounting host folders in docker containers
 
Besides attaching volumes a docker client can also mount host volumes in docker containers.

The Greetings Webservice `/new` API rest endpoint can receive a second query parameter named `lang`. 
For now only 4 languages are available: ro, en, fr, de. Now the user has a request for Italian (it).

The application has been designed to read new greeting templates from a folder defined by an environment variable
which is now set in our to `/greeting_templates`

Let's see how we can provide our container with the templates.

```cmd
# First let's cleanup
$ docker stop crazy-cow
$ docker rm crazy-cow
```

```cmd
# Linux:
$ docker run -d -p 8080:8080 \
    -v $(pwd)/extra_greeting_templates:/greeting_templates \
    -v crazy-cow-soul:/app_data \
    --name crazy-cow \
    greetings-ws:h2db

# Windows:
$ docker run -d -p 8080:8080 ^
    -v "%cd%\extra_greeting_templates":/greeting_templates ^
    -v crazy-cow-soul:/app_data ^
    --name crazy-cow ^
    greetings-ws:h2db
```

Now go to http://localhost:8080/greetings/new?name=CrazyCow&lang=it to create some data

If you go to http://localhost:8080/greetings you should see an entry for 'Ciao, CrazyCow!' :-)
