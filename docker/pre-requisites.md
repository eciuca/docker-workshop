## Contents

* <a href="https://workshops.emanuelciuca.com/docker">Goals</a>
* <span>**Pre-requisites**</span>
* <a href="https://workshops.emanuelciuca.com/docker/docker-build">Building a docker image</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-run">Running a docker container</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-monitoring-and-debug">Monitoring and debugging containers</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-volume">Working with volumes</a>
* <a href="https://workshops.emanuelciuca.com/docker/docker-multi-stage-builds">Multi-stage builds</a>

## Pre-requisites

### Checkout the following repository:

```cmd
$ git clone https://github.com/eciuca/greetings-webservice.git
```

### Go to the project root directory:

```cmd
$ cd greetings-webservice
```

## Build the project

### Linux/MacOS
```cmd
$ ./build.sh
```
Note: you might need to run `chmod +x *.sh` before to make all the .sh files executable

### Windows
```cmd
$ build.bat
```

## Run the webservice

### Linux/MacOS
```cmd
$ ./run.sh
```

### Windows
```cmd
$ run.bat
```
