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

A. Please make a new directory, *Test*, that is not in the main workshop directory. ```Cd``` into the directory. 

Please run the following commands on Terminal to confirm that Docker is installed correctly. 

B. Run docker --version and ensure that you have a supported version of Docker:

```shell
 docker --version
```
You should get a response with this version or higher. 
```shell
 Docker version 17.12.0-ce, build c97c6d6
 ```
 
 C. Run docker info to view even more details about your docker installation:

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
 
 D. Test to make sure Docker is working properly, run a simple hello world test: 
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
Congratulations! Docker is working correctly. You can delete the test directory now. 

## Step by Step

Now that you're set up, we're going to start building our app! We'll start with the bottom of a heirarchy, 
building a new *container*. We'll then move on to build a *service*, the second level of the app that defines relationships
between multiple containers. Finally, we'll deploy our app using a *stack*, which defines interactios between all of the services. 

We'll first begin by writing an *image*, which explains how to build a container. *Images* are defined by something called a 
*Dockerfile*. 

1. ```cd``` into the *ws-5-10-deployment-docker* directory. Create a new dockerfile by typing ```atom Dockerfile``` into the command line. 

2. Copy and pase the following code into Dockerfile. The comments creates 


         # Use an official Python runtime as a parent image
         FROM python:2.7-slim

         # Set the working directory to /app
         WORKDIR /app

         # Copy the current directory contents into the container at /app
         ADD . /app

         # Install any needed packages specified in requirements.txt
         RUN pip install --trusted-host pypi.python.org -r requirements.txt

         # Make port 80 available to the world outside this container
         EXPOSE 80

         # Define environment variable
         ENV NAME World

         # Run app.py when the container launches
         CMD ["python", "app.py"]

3. Create two new files by calling ```atom requirements.txt app.py```. Make sure they are in the same folder as Dockerfile. 

4. Please copy the following code into ```requirements.txt```: 
    ```
    Flask
    Redis
    ```
 And this code into app.py: 
 
         from flask import Flask
         from redis import Redis, RedisError
         import os
         import socket

         # Connect to Redis
         redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

         app = Flask(__name__)

         @app.route("/")
         def hello():
             try:
                 visits = redis.incr("counter")
             except RedisError:
                 visits = "<i>cannot connect to Redis, counter disabled</i>"

             html = "<h3>Hello {name}!</h3>" \
                    "<b>Hostname:</b> {hostname}<br/>" \
                    "<b>Visits:</b> {visits}"
             return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

         if __name__ == "__main__":
             app.run(host='0.0.0.0', port=80)

Great! Adding requirements.txt and app.py to the folder completes the app. When Dockerfile is called and is built 
into an *image*, the ```ADD``` command will make sure both files are present in the container. You have already
set up an environment in flask and Python, even if you didn't realize it :smile:

Now, lets go ahead and build the app. Please go to the top level directory and call  ```docker build -t friendlyhello .```

Now call the following to see the *image*: 

    ```docker image ls    ```
The output should look like this: 
    ```
   REPOSITORY            TAG                 IMAGE ID
   friendlyhello         latest              326387cea398
    ```
 



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
