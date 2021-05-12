
# DOCKER
> Follow me on,  [LinkedIn](https://www.linkedin.com/in/vivek-bombatkar/), [Github](https://github.com/vivek-bombatkar)  



### Useful links 
> Docker toolbox: https://docs.docker.com/toolbox/toolbox_install_windows/#what-you-get-and-how-it-works  
> https://www.youtube.com/watch?v=99d0O2AitLk  
> https://www.youtube.com/watch?v=YFl2mCHdv24  
> https://www.youtube.com/watch?v=WvmF-WsmzfQ  
> https://runnable.com/docker/python/dockerize-your-python-application  
> https://github.com/buildkite/python-docker-example  
> https://amaysim.engineering/the-3-musketeers-how-make-docker-and-compose-enable-us-to-release-many-times-a-day-e92ca816ef17  
> https://serversforhackers.com/s/docker-in-development  
> https://djangostars.com/blog/what-is-docker-and-how-to-use-it-with-python/  
> https://medium.com/@chadlagore/conda-environments-with-docker-82cdc9d25754  
> https://www.youtube.com/watch?v=JBtWxj9l7zM  
> 


### Docker In the simple illustration 

<img src="https://github.com/vivek-bombatkar/MyLearningNotes/raw/master/Docker/docker_concept.JPG" />


- Note: after installing Docker on Windows, need to add below path to Path environment variable before start using Docker commands in command prompt.  

``` C:\Program Files\Docker\Docker\resources\bin ```



### VMs vs Containers
Source : https://akfpartners.com/growth-blog/vms-vs-containers

- [img](https://akfpartners.com//uploads/blog/VM_Image.PNG)
- [img](https://akfpartners.com//uploads/blog/virtual_vs_container_benefits.PNG)



## Below steps to build and execute very basic docker container


### 1. list all running docker images

``` 
$ docker ps -a

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS                     PORTS               NAMES
```

### 2. create Dockerfile and dummy script

***Dockerfile***
```
FROM python:3

ADD dummy_script.py /

CMD [ "python", "./dummy_script.py" ]
```

***dummy_script.py***
```python
print("Hello Vivek!)
```

### 3. build docker images

```
$ docker build -t my_test_docker .

Sending build context to Docker daemon   7.68kB
Step 1/3 : FROM python:3
 ---> 17453243214e
Step 2/3 : ADD dummy_script.py /
 ---> 5abf4e3f9fab
Step 3/3 : CMD [ "python", "./dummy_script.py" ]
 ---> Running in 2d8e0e769db7
Removing intermediate container 2d8e0e769db7
 ---> 38a783dda97c
Successfully built 38a783dda97c
Successfully tagged my_test_docker:latest
SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host. All files and directories added to build context will have '-rwxr-xr-x' permissions. It is recommended to double check and reset permissions for sensitive files and directories.
```

List docker image and see the entry.

``` 
$ docker ps -a

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS                     PORTS               NAMES
e36dd51ad184        my_test_docker                                    "python ./dummy_scri…"   3 minutes ago       Exited (0) 3 minutes ago                       kind_heyrovsky
```

### 4. run docker image as container

```
$ docker run my_test_docker

Hello Vivek!

```

### 5. Killing 'docker image' vs 'docker container'

#### delete container
```
$ docker rm e36dd51ad184
e36dd51ad184
 
$ docker ps -a

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS                     PORTS               NAMES

```

> note though the container is deleted you still can run the docker image !

```
$ docker run my_test_docker
```


### delete image/s
```
$ docker rmi my_test_docker
Untagged: my_test_docker:latest
Deleted: sha256:38a783dda97c8b188abc2c1d9cb6ed594ee0d06e77e393326f5fe59cb0ebea70

```

### Clearn cache for all images.
If docker had cached the dependencies that he wants to download already then it might not download it again, which could lead to some issues in production.
> https://forums.docker.com/t/how-to-delete-cache/5753/2  
```
docker kill $(docker ps -q)
docker_clean_ps
docker rmi $(docker images -a -q)
```


## Notes on docker

> ***docker***


> **Container**
```
- running instance with required application. 
- Containers are always created from images. 
- Container could expose ports and volumes to interact with other containers or outer world. 
- Container could be easily killed / removed and re-created again in a very short time.
```

> **Image**
```
- basic element for every container. 
- Uses copy-on-write model. 
- During building images every step is cached and could be reused. 
- Building images could take some time - it depends on particular image. 
- While containers can be started from images very quickly.
```

> **Port**
```
- is a TCP/UDP port in it’s classical meaning. 
- There are a lot of modes for networking in Docker containers, 
- so to make things simpler let’s assume that ports could be exposed to 
 outer world (accessible from host OS) or connected to other containers 
- accessible only from that containers and invisible from outer world.
```

> **Volume**
```
- the best explanation is shared folder. 
- Volume keeps data outside of its container but accessible from this container or other connected containers. 
- Data stored in volumes is persistent.
```

> **Registry**
```
- server that keeps images. 
- Could be compared with git - you can pull image from registry to deploy it locally 
- and you can push locally built images to registry 
- (create new image or update image version on registry). 
- Docker registry application is Open Source app like the main application 
- so you could deploy your private Docker Registry on any server you want
```

> **Docker hub**
```
- is a registry with web-interface. 
- It keeps a lot of Docker images with different software. 
```

- ***docker composer***
> manages Docker containers in a very neat way. 

> It allows multiple Docker commands to be written as a single one, which allows our Makefile to be a lot cleaner and easier to maintain.

- ***make - makefile***
> Make is a cross-platform build tool to test and build software and it is used as an interface between the CI/CD server and the application code.

> A single Makefile per application defines and encapsulates all the steps for testing, building, and deploying that application.

> Having a clean Makefile is key. It helps to understand it quickly and to maintain. Therefore, having some conventions like target vs _target, Pipeline targets, and Pipeline targets really aim to make developers’ life easier.


- ***dockerfile***
> FROM — set base image

> RUN — execute command in container

> ENV — set environment variable

> WORKDIR — set working directory

> VOLUME — create mount-point for a volume

> CMD — set executable for container

> ENTRYPOINT - set the starting script 

## Common issues 

### failed: Forbidden path outside the build context:
- So this fails :  ``` COPY ../tmp/ /tmp ```  
Solution:  
> https://howtovault.com/posts/how-to-fix-copy-failed-forbidden-path-outside-the-build-context-in-docker/  
```
#build.sh
#!/bin/bash
mkdir app

cp -r ../src/* app

docker build --force-rm -t vkbomb/gfk-spark-submit:v2 -f Dockerfile .

rm -rf app

docker run  vkbomb/gfk-spark-submit:v2 src/algorithm_2.py
```
And Then  
``` COPY /app /src/ ```

