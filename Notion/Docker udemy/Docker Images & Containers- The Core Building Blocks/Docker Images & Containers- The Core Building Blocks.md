# Images Vs. Containers

Containers: small packages that contains both our application and the entire environment

Images: templates/blueprints for containers. It contains code + required tools/runtime to execute the code

![[/image 142.png|image 142.png]]

We split this (images and containers) so we can create multiple containers from just one image

![[/image 1 4.png|image 1 4.png]]

→ Containers are the concrete running instances of images

# Using & Running External (Pre-built) Images

There are 2 ways of creating and getting images

- Use an existing image, pre-built image (If it can’t find the image locally, it will find on Docker hub)
- Create your own (based on other images)
    
    Typically, you would pull a new official base image, and then add our code on top of that to execute our code with that image, and we want to do all of that inside a container
    

![[/image 2 3.png|image 2 3.png]]

# Building our own image with a Dockerfile

Typically, we start with a FROM, this allow us to build our image up on another base image

```Docker
FROM node
```

  

Next step, we want to tell Docker which files, that live here in our local machine should go into the image

```Docker
FROM node

COPY . .
```

The first ==**.**== tells docker that all the folders, subfolders and files in the project should be copy in the image

The second ==**.**== **is** the path inside the image where those file should be stored. Every image and therefore every container created on based on the image has its own internal file system, which is totaly detach(tach roi) from our file system from our machine. It’s hidden away from the container.

![[/image 3 3.png|image 3 3.png]]

It’s a good idea to not use the root folder, root entry in our docker container, but some subfolder

```Docker
FROM node

COPY . /app
```

→ All the folders in our project will be copied into the app folder inside the container, and ‘app’ folder will simply be created in the image and container

  

In the next step, we need to run ‘npm install’ to install all the dependencies of our node application

```Docker
FROM node

COPY . /app

RUN npm install
```

However, by default all those commands will be executed in the working directory of our docker container and image, and by default the working directory is the root folder. Since we copy our folder into the ‘app’ folder → We want to run ‘npm install’ in the ‘app’ folder

To tell docker that all commands should be executed in a specific folder folder:

```Docker
FROM node

WORKDIR /app

\#COPY . ./

COPY . /app --this is better since it's more clear

RUN npm install
```

Since we change the current working directory to /app, which means we are now in /app folder, so instead of COPY all to /app, we will copy to ==**./ (./ means at here, relative path)**== or we can put absolute path which is /app

  

After all of that, we will want to start our server

```Docker
FROM node

WORKDIR /app

COPY . /app

RUN npm install

RUN node server.js
```

This is wrong, since this would be executed whenever this image is being built. All these here are instructions to Docker for setting up the image → The image should be the template for the container. The image is not what we run in the end, it’s the container. Therefore, we only want to start the server when we start the container based on this image

  

```Docker
FROM node

WORKDIR /app

COPY . /app

RUN npm install

CMD ["node", "server.js"]
```

The difference to RUN is that CMD will not be executed when the image is created, but when the container is started. The syntax of CMD is an array, inside the array is the command we want to run but split it

==If we don’t specify a CMD, the CMD of the base image will be executed. With no base image and no CMD, we’d get an error==

  

Because the docker container is isolated from our local environment → it has its internal network. So when we listen to the port specified in the code, the container doesnt expose that port to our local machine → we wont be able to listen on the port, because something listening inside of the container

```Docker
FROM node

WORKDIR /app

COPY . /app

RUN npm install

EXPOSE 80

CMD ["node", "server.js"]
```

→ EXPOSE means that when this container is started, we want to expose a certain port to our local system

==CMD should always be the last instruction==

# Running a Container based on our own Image

> After completing the Dockerfile, how do we turn it into an image and turn it into a container?

First, we have to create the image based on the instruction of Dockerfile

```Docker
docker build .
```

It tells docker to build a custom image, based on a Dockerfile (. represents **here**)

After that, we will run the container from the image

```Docker
docker run id
```

After we ran the container, it will not work, since we didn’t specify the -p flag

```Docker
docker run -p <host-port>:<exposed-port> imageId
docker run -p 3000:80 7d...
```

  

# Images are Read-Only

If we make changes to the code in our project after we have built the image and ran the container, even when we restart the container, it won’t change the code.

→ Basically, we took a ==**snapshot**== of our source code (==COPY . /app==) at the point of time the image build into the container. So any changes after the container is built won’t be changed in the container → In order to update the changed source code to the image, we have to ==**rebuild the image into a new image**==

## Summary

Images are basically locked and finished once we built them, everything in the image is Read-Only then, and we can’t edit it from the outside → If we changed something, like the source code, it has no impact

# Images Layers

Notice when we ==**rebuild an image,**== it is super fast, unlike the first time we build, because we can see it using cache, some of it it was same as before → Docker can infer(suy ra) that it doesn’t need to go through that instruction(COPY, WORKDIR,…) again.

Whenever we build an image, Docker ==**caches every instruction result (layer).**== And then when we rebuild an image, it willl use these cache results if there is no need to run the instruction again

→This is called the layer based architecture. ==**Every instruction represents a layer**== in you Dockerfile, and an ==**image is built up from multiple layers**== based on different instructions. In addition, in ==**image is read-only**==, which means ==**once an instruction has been executed, and once an image is built, the image is locked in, and code in there can’t change**==, unless we rebuild the image, which technically we create a new image.

If we then run a container based on an image, that container basically add a new extra layer on top of the image, which is the running application or running code

  

==**All layers AFTER the changed layer are rebuilt**==

So when we changed the source code, since we put npm install under the COPY . /app, it will run npm install again even though there’s no package changed. We can change the Dockerfile a little bit:

```Docker
\#old
FROM node

WORKDIR /app

COPY . /app

RUN npm install

EXPOSE 80

CMD ["node", "server.js"]
```

```Docker
\#changed
FROM node

WORKDIR /app

COPY package.json /app

RUN npm install

COPY . /app

EXPOSE 80

CMD ["node", "server.js"]
```

As we can see, we add a new instruction ==COPY package.json /app== and ==RUN npm install==  
before the COPY ==.== /app. Because we only want to run npm install when we add some packages → package.json will changed → when we rebuild it will run again all the layers below. But when we just change some source code, it will just rerun the COPY ==.== /app, not the npm install → ==**better performance**==  
  

  

# Summary

A container is just the image with an extra top layer with command (CMD)

![[/image 4 3.png|image 4 3.png]]

# Managing Images & Containers

![[/image 5 3.png|image 5 3.png]]

## Stopping & Restarting Containers

To start a stopped container:

```Docker
docker start <id> or <name>
```

  

## Attached and Detached

==Attached mode==: Container running in ==foreground==, can’t do anything current terminal

==`docker run`== is by default attached mode

In attached mode, we are ==listening to the output of that container,== like we can see the result of console.log(”…”);

  

==Detached mode==: Container running in ==background==

==`docker start`== is by default detached mode

If we want to use ==`docker run`== in detatched mode, we can add the ==**-d**== flag

```Docker
docker run -p 8080:80 -d 294d
```

If we want to use docker start in attached mode, we can add the ==**-a**== flag

```Docker
docker start -a 294d
```

  

If we want to attach to a ==running container==, there are 2 ways:

```Docker
docker attach <id> or <name>
```

  

```Docker
docker logs <id> or <name>
```

We can also add the ==-f== flag, which means follow to follow the log output, basically like attach

```Docker
docker logs -f <id> or <name>
```

## Interactive mode

rng.py

```Python
from random import randint
min_number = int(input('Please enter the min number: '))
max_number = int(input('Please enter the max number: '))

if (max_number < min_number):
print('Invalid input - shutting down...')
else:
rnd_number = randint(min_number, max_number)
print(rnd_number)
```

We have a python file, to run it, let’s create a container for it

First we gotta write the Dockerfile to create the image

```Docker
FROM python

WORKDIR /app

COPY . /app

CMD ["python", "rng.py"] 
```

  

As we can see, the program asks us to input 2 number min and max and generate a random number from that range. In attached mode, we can only listen to the output, but we can’t input anything. So in order to input, we gotta turn on interactive mode

```Docker
docker run -it  317
```

-i (--interactive) Keep STDIN(standard in) open even if not attached: allow us to input

-t (--tty): Allocate a pseudo-TTY (like a terminal)

==docker run is by default attached mode==

  

After we ran the program, the container will automatically stopped. To restart the program with the same interactive mode and input:

```Docker
docker start -ai 841d
```

-a (attach)

-i : interactive

The containers still remember the -t flag when we first build the container

## Delete images & containers

To ==remove stopped== containers:

```Docker
docker rm <id> or <name>
```

  

To remove running containers, we first need to stop it

```Docker
docker stop <ids> <ids> <ids> <names> <names>…
```

```Docker
docker rm <ids> <ids> <ids> <names> <names>…
docker rm 231 452 852 523... 
```

  

To ==remove all stopped containers== at once

```Docker
docker container prune
```

  

To ==remove== containers ==automatically== when it exit

```Docker
docker run -p 3000:80 -d --rm <imageId>
```

  

To remove ==i==mages

```Docker
docker rmi <id> <id> <id> <name> <name>...
```

We ==can only remove images not being used== by any containers anymore → stop containers

  

To remove ==unused== images at once

```Docker
docker image prune
```

If we want to remove ALL images, including tagged images, we need to run

```Docker
docker image prune -a
```

## Inspecting images

```Docker
docker image inspect <id>
```

It will contains some information about the image

Layers may have more than instructions in the Dockerfile, since the base image(node, nginx) could have some more layers

## Naming & Tagging Containers and Images

To give name to the containers

```Docker
docker run -p 3000:80 -d --rm --name goalsapp 23d
```

![[/image 6 3.png|image 6 3.png]]

  

Image tags consist of 2 parts: ==**name**==:==**tag**==

![[/image 7 3.png|image 7 3.png]]

```Docker
\#docker build -t goalsapp:10 .
docker build -t goalsapp:latest .
```

  

# Sharing Images & Containers

We got 2 main ways of sharing images:

- Sharing the ==source code with Dockerfile==
- Share a ==complete built image==

![[/image 8 3.png|image 8 3.png]]

  

## Pushing Images to DockerHub

Docker has built-in command for sharing images

There are 2 main places where we can push our images to:

- Docker Hub (Official Docker Images Registry)
- Any private registry

![[/image 9 3.png|image 9 3.png]]

If we just push and pull with a regular image repository name, then it will automatically go to Docker Hub. If we want to push/pull to a private registry, we have to include the host, the url.

![[/image 10 3.png|image 10 3.png]]

To push to this repository, we gotta have an image with the same name songphuc191004/node-app

We will have to log in before pushing

```Docker
docker login
\#docker logout
```

We could either create a new image with that name:

```Docker
docker build -t songphuc191004/node-app .
```

  

or rename an exisiting image (it actually create a clone image instead of get rid of the old one):

```Docker
docker tag oldName songphuc191004/node-app
```

  

![[/image 11 3.png|image 11 3.png]]

We can see it mounted from library/node because that is already on Docker Hub, so it’s establish the connection the node image, and only pushes the extra code, information it needs

  

Result

![[/image 12 3.png|image 12 3.png]]

  

## Pulling and using shared Images

To pull a public image, you don’t need to login:

```Docker
docker pull songphuc191004/node-app 
```

It by default will pull the image with ‘latest’ tag

  

We can also simply use

```Docker
docker run songphuc191004/node-app
```

As it will find on Docker Hub if it could’t find locally

What `docker run` will not do is automatically check for updates. If we have an image locally, because we pulled or ran it before, then if we run the container again based on an image, `docker run` ==will not== check if the image you have locally in your system is the latest version of that image. So if you update the image, and push it again to docker hub, just using `docker run` will not give us the latest update image, we instead manually need to run `docker pull imageName` first to ensure that we have the latest version, and we can execute `docker run` again

# Summary

![[/image 13 2.png|image 13 2.png]]

![[/image 14 2.png|image 14 2.png]]