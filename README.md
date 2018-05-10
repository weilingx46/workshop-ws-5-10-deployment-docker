# CS52 Workshop, 5/10/18:  Learning to use Docker

![](https://giphy.com/gifs/rCQTCy4rvuxR6/html5)

Similar to a Java application, which will run exactly the same on any device capable of running a Java Virtual Machine, a Docker container is guaranteed to be identical on any system that can run Docker. The exact specifications of a container are stored in a Dockerfile. By distributing this file among team members, an organization can guarantee that all images built from the same Dockerfile will function identically. In addition, having an environment that is constant and well-documented makes it easier to keep track of your application and identify problems.

## Overview

We will build a simple image and upload it to a Github-like repository so that we can download and run it on any other computer.

## Setup

To set up for this workshop, please follow the steps below (the same steps that were slacked out last night).
### If you have already downloaded Docker and completed the set-up, please skip to Setup - Step 2

*1. Download the Docker desktop app from the links below:*

   MAC :apple:: https://docs.docker.com/docker-for-mac/install/

   WINDOWS:microsoft: (Pro + Hyper-V): https://docs.docker.com/docker-for-windows/install/
   
   WINDOWS:microsoft: (Home + Community): https://docs.docker.com/toolbox/toolbox_install_windows/

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

```
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 17.12.0-ce
 Storage Driver: overlay2
 ...
 ```

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

>An image is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables, and configuration files.

>A container is a runtime instance of an image--what the image becomes in memory when executed (that is, an image with state, or a user process). You can see a list of your running containers with the command, docker ps, just as you would in Linux.

Next, run ```docker image ls``` to confirm that the image was downloaded correctly, and then
```docker container ls --all```  to confirm that the container was built properly.

You will see a response on terminal that looks like:

```
CONTAINER ID     IMAGE           COMMAND      CREATED            STATUS
54f4984ed6a8     hello-world     "/hello"     20 seconds ago     Exited (0) 19 seconds ago
```
Congratulations! Docker is working correctly. You can delete the test directory now.

## Step 2

Now that you're set up, we're going to start the first step towards building our app! We'll start with the bottom of a heirarchy, building a new *container*. In the interest of time, we will stop at this, but if you are interested, the next steps towards building a complete app consits in building a *service*, the second level of the app that defines relationships
between multiple containers, and then deploy the app using a *stack*, which defines interactios between all of the services.

We'll first begin by writing an *image*, which explains how to build a container. *Images* are defined by something called a
*Dockerfile*.

1. Please fork the *ws-5-10-deployment-docker* directory here: https://github.com/dartmouth-cs52-18S/workshop-ws-5-10-deployment-docker. ```cd``` into the top level of this directory. Create a new dockerfile by typing ```atom Dockerfile``` into the command line.

2. Copy and paste the following code into Dockerfile.


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

3. Create two new files by running ```atom requirements.txt app.py```. Make sure they are in the same folder as Dockerfile.

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

             html = "<h3>Congratulations, {name}! You've built your first container! We hope you have a great day :)</h3>" \
                    "<b>Hostname:</b> {hostname}<br/>" \
                    "<b>Visits:</b> {visits}"          
             return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

         if __name__ == "__main__":
             app.run(host='0.0.0.0', port=80)

Great! Adding requirements.txt and app.py to the folder completes the app. When Dockerfile is called and is built
into an *image*, the ```ADD``` command will make sure both files are present in the container. You have already
set up an environment in flask and Python, even if you didn't realize it :smile:

Now, let's go ahead and build the app. Please go to the top level directory and call  ```docker build -t friendlyhello .```

Now run ```docker image ls``` to see the *image*:

The output should look like this:
    ```
   REPOSITORY            TAG                 IMAGE ID
   friendlyhello         latest              326387cea398
    ```. It is okay if you also see ```hello-world              latest              e38bc07ac18e```.

We're finally ready to start our app. Run ```docker run -p 8080:80 friendlyhello``` to start the app and map your computer's port 8080 to the container’s port 80. Note we specify the app should be run at the container's port 80 in app.py.

You should see a message that the app is running on ```http://0.0.0.0:80```. Since port 80 has been mapped to port 8080 on your computer, check ```http://localhost:8080``` to verify the app is running.

Your browser should look like this:
![screen shots are helpful](img/app-in-browser.png)

So what makes our app special? It is packaged with dependencies and can be run on any other machine. To demonstrate how portable our app is, let's try running it somewhere else.

First we will push our app to a *registry*. A registry is just a collection of repositories, like on Github.

Log into Docker's public registry by running ```docker login```.

Next, tag your application. This is how we assign a version to our application so that later we can retrieve this specific version. To do so, run ```docker tag friendlyhello USERNAME/getting-started:tag-youre-it```. Remember ```friendlyhello``` is the name of our *image*. ```USERNAME``` is the username of your docker account. ```getting-started``` is the name of the repository we will upload to, and ```tag-youre-it``` is the version we're assigning to our app.

Now run ```docker push USERNAME/getting-started:tag-youre-it``` to upload your app to the registry.

From this point on, you can run your app on any computer by simply running ```docker run -p 8080:80 USERNAME/getting-started:tag-youre-it```.

No matter where ```docker run``` executes, it pulls your image, along with Python and all the dependencies from ```requirements.txt```, and runs your code. It all travels together in a neat little package, and you don’t need to install anything on the host machine for Docker to run it.

## Summary / What you Learned

* [X] Set up Docker

* [ ] Create an *image*

* [ ] Upload an *image* to a *registry*

* [ ] Download and run an *image* on another machine.

## Resources

docker.com
