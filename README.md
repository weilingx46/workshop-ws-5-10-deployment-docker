# CS52 Workshop, 5/10/18:  Learning to use Docker

![](https://giphy.com/gifs/rCQTCy4rvuxR6/html5)

Brief motivation here as well as in presentation

## Overview

Summary of what we're about to do.

## Setup

To set up for this workshop, please follow the steps below (the same steps that were slacked out last night). 
### If you have already downloaded Docker and completed the set-up, please skip to Setup - Step 2

*1. Download the Docker desktop app from the links below:* 
   
   MAC :apple:: https://docs.docker.com/docker-for-mac/install/

   WINDOWS :microsoft:: https://docs.docker.com/docker-for-windows/install/
 
   Please set up an account with Docker and follow all installation steps. 


*2. Test to see if Docker properly installed:*

Please run the following commands on Terminal to confirm that Docker is installed correctly. 

A. Run docker --version and ensure that you have a supported version of Docker:

```shell
 docker --version
```
You should get a response with this version or higher. 
```shell
 Docker version 17.12.0-ce, build c97c6d6
 ```
 
 B. Run docker info to view even more details about your docker installation:

```shell
docker info
```
You will get the following response: 
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 17.12.0-ce
 Storage Driver: overlay2
 ...
 
 C. Test to make sure Docker is working properly, run a simple hello world test: 
 ```
 docker run hello-world
```

You will see
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
ca4f61b1923c: Pull complete
Digest: sha256:ca0eeb6fb05351dfc8759c20733c91def84cb8007aa89a5bf606bc8b315b9fc7
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

You've made your first container, *hello-world*!

Next, run ```docker image ls``` to see confirm that the image was downloaded correctly, and then 
```docker container ls --all```  to confirm that the container was built properly. 

You will see a response on terminal that looks like: 

```
CONTAINER ID     IMAGE           COMMAND      CREATED            STATUS
54f4984ed6a8     hello-world     "/hello"     20 seconds ago     Exited (0) 19 seconds ago
```

## Step by Step

* Explanations of the what **and** the why behind each step. Try to include:
  * higher level concepts
  * best practices

Remember to explain any notation you are using.

```javascript
/* and use code blocks for any code! */
```

![screen shots are helpful](img/screenshot.png)

:sunglasses: GitHub markdown files [support emoji notation](http://www.emoji-cheat-sheet.com/)

Here's a resource for [github markdown](https://guides.github.com/features/mastering-markdown/).


## Summary / What you Learned

* [ ] can be checkboxes

## Resources

* cite any resources
