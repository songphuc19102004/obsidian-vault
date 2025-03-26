# Data categories

There are different kind of data

![[/image 143.png|image 143.png]]

### Application

A package of code which set up and written by us

This code and the entire environment is added to the image during the ==build phase== → When we build an image, our code is copied into the image (that is what Dockerfile for: to set up the instructions) → When we create a container based on that image, it contains our code and environment

Since it’s copied into the image, that code is fixed, can’t be changed.

  

### Temporary App Data

Data generated while the application is ==running==.

For example: Talking about a website, a user might be entering something in a form, and when we fetch that data and handle it on the server, which might be running in the container, then the ==entered user data is temporary data==. We might store it in ==variables== in our code → stored in ==memory== → we are ==fine with losing that data== when the container ==shut down.==

So that would be data which is not read-only, but we also want to write it while application is running → since it’s read + write, it’s stored in ==Containers== (the ==extra layer== which is added by Docker on top of our image, which makes up our containers. This extra layer is ==aware of the image and the file system of the image==), because images are read-only.

→ The container ==stores data in the read - write extra layer==

![[/image 1 5.png|image 1 5.png]]

  

### Permanent App Data

For example: We got an application where users can sign up and log in, then these ==user accounts== we would want to ==store in files or typically in database== → That data needs to persist → ==Must not be lost if Containers stop / restart== (The data should actually even survive the removal(deletion) of a container)

It must be stored permanently → store in Containers, but with the help of ==**Volumes**==

# Volumes

## Problem

We have a node app uses to take feedback from customer by using a form, it will ==store in a file in the Container’s file system== → When the Container is stopped and restart, the data is still stored in the file in the ==Container’s file system==. But when we ==remove the Container, all the data is gone.==

## Volumes

![[/image 2 4.png|image 2 4.png]]

Volumes are folders on our host machine which we make Docker aware of, and which are then mapped to folders inside of the Docker Containers

→ We can connect a folder inside the container to a folder outside the container on our host machine, and ==changes on either folder will reflect to the other one==

  

To add a volume, we will add an instruction to our Dockerfile

```Docker
FROM node

WORKDIR /app

COPY . /app

RUN npm install

EXPOSE 80

VOLUME [ "/app/feedback" ]

CMD ["node", "server.js"]
```

But even after we add this instruction, if we create a new Container based on that image, it still doesn’t have the old data we saved on the old container

  

![[/image 3 4.png|image 3 4.png]]

## Anonymous Volumes

As the above approach (VOLUME []), we used Anonymous Volumes

Which we don’t give it a name, so Docker just give it some encryptic name like fe6167c…

And if we remove the container using this volume, it’s gone in `docker volume ls` →It only exists while our container exists

The path is manage by Docker, we don’t know where it is, and we should not know/access it.

  

## Named Volumes

Named volumes WILL survive Container’s removal (the folder on our hard drive will survive) →When we start a new Container, the data is still there

First we need to remove the VOLUME instruction in the Dockerfile

```Docker
FROM node

WORKDIR /app

COPY . /app

RUN npm install

EXPOSE 80

VOLUME [ "/app/feedback" ]

CMD ["node", "server.js"]
```

Then rebuild the image

```Docker
docker build -t node-app-image .
```

Then run the container with the ==-v== (stands for volume) flag

```Docker
docker run -d -p 3000:80 --name node-app-container --rm -v feedback:/app/feedback
node-app-image
```

We will specify the path inside the container file system which we wanna save: ==**/app/feedback**==

And infront of the path, we can provide any name of our choice ==**feedback**==

After we delete the Container, we can run the Container using the command above

```Docker
docker run -d -p 3000:80 --name node-app-container --rm -v feedback:/app/feedback
node-app-image
```

And it will have the data of the removed Containers

## Differences between Named and Anonymous Volumes

- The key difference to Anonymous Volume is: ==Named Volumes will not be deleted by Docker when the container removed== ==≠== ==Anonymous Volumes are deleted== because they are recreated whenever the container is created
    
    → Keep them around when container is removed make no sense.
    

  

- Anonymouse Volumes are closely attached to one specific Container ≠ Named Volumes are not atttached to a Container

## Bind Mounts

### Problem

Whenever we ==change anything in our source code== (server.js, feedback.html,…), those changes are ==not reflected in the running Containers==, unless we ==rebuild== the image

### Key difference

Volumes are managed by Docker, and we don’t really know where they are on our machine file system, but for bind mounts, we ==do know== it → we could put our source code into bind mount → we can ensure that the Container is aware of that → The source code is not used in the COPY . . snapshot, but instead from the bind mount → The ==Container always have access to latest code==

  

Since it’s specific to the Container that we run, we cannot do that in image (Dockerfile)

→ We have to set up the bind mount in the terminal when we run

```Docker
docker run -d -p 3000:80 --name node-container -v feedback:/app/feedback 
-v "/home/songphuc1910/Desktop/docker-learn:/app" node-image:latest
```

==**/home/songphuc1910/Desktop/docker-learn:**== The ==path to the folder on our host machine== where we have all the code, all the content that should go into the /app folder (and it must be an absolute path, and also since we are binding the ‘==**app’**== folder, we have to bind to a folder path. If we bind to a file in the container, we can bind to a file path)

==**/app:**== path of content we want to bind (folder, file,..)

We can “ “ the path if it includes special chars or whitespace

![[/image 4 4.png|image 4 4.png]]

But when we run, it immediately stopped and we can see the log error said “Cannot find modul express”. That simply means our node code doesn’t even start executing, since it didn’t even run npm install. So what’s wrong?

  

→The problem is that, we are OVERRIDING all the files in /app

```Docker
docker run -d -p 3000:80 --name node-container -v feedback:/app/feedback 
-v "/home/songphuc1910/Desktop/docker-learn:/app" node-image:latest
```

  

So all of these steps, including the command ==**RUN npm install**==, which download node modules, are basically gone. So 3 of those instructions are worthless because we are going to override everything in the app folder with our local folder anyway (the local folder doesn’t have node modules folder since we don’t npm install there).

```Docker
FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "node", "app.mjs" ]
```

  

## Container / Volume Interaction

![[/image 5 4.png|image 5 4.png]]

If we have a Container, and we have a Volume and a Bind Mount, we can mount both into the Container with the ==-v flag.== That means that some folders inside the container are mounted or connected to folders on the host machine. Let say we already have files inside the container, in that case, they all now exist in the outside volume, and if we write a new file, it’s also added in the folder on the host machine. ==If the container start up, and it finds files in the volume, and the container doesn’t have any internal files yet, it loads the files from the volume==. That actually what we utilize with the Bind Mount. Here we don’t have any files inside the container, but we have files on the local host machine → these files are basically usable inside the container.

  

The problem is local host folders and the content in it overrides what in the Docker container → we got node_modules removed. To solve this, we need to tell Docker that there are certain parts in its’ internal file system, which should not be overriden from outside in case we have a ==clash (node_modules downloaded by Dockerfile instruction got overriden and removed==). That canbe achieved by adding another Anonymous Volume to the Container → Use case of Anonymous Volume

```Docker
docker run -d -p 3000:80 --name node-container -v feedback:/app/feedback 
-v "/home/songphuc1910/Desktop/docker-learn:/app" -v /app/node_modules
node-image:latest
```

  

==**-v /app/node_modules:**== This is equivalent to ==VOLUME [].== And also it’s an Anonymous Volume because it has no name. We can add ‘ : ‘ before and give it some name.

==We write flag in== ==`docker run`== ==because we don’t have to rebuild image, so if we can we should write the VOLUME in the first place==

```Docker
...

VOLUME [ "/app/node_modules" ]

...
```

==The rule is: If there are clashes, the longer internal path wins==

Here we have a clash

```Docker
docker run -d -p 3000:80 --name node-container -v feedback:/app/feedback 
-v "/home/songphuc1910/Desktop/docker-learn:/app" -v /app/node_modules
node-image:latest
```

==**/home/songphuc1910/Desktop/docker-learn:/app**==

==**/app/node_modules**== (even for anonymous volume, they are managed by Docker, and there is some mapped folder somewhere on our local machine)

Here Docker sees that some volume mapped to ==**/app**== folder, and some volume mapped to the ==**/app/node_modules**== → By the rule ==**“If there are clashes, the longer internal path (more specific path) wins”.**== That means, we can still bind to the /app folder, but the node_modules folder is inside of the /app folder. And the node_modules folder (created by the RUN npm install in Dockerfile) will survive. It will override the coming in from outside of the node_modules folder. And it will pass and override the non-existant node_modules folder.

→ node_modules folder, which were created during the image creation, will survive, and co-exist together with the bind mount

  

After all these steps, now if we change the source code, it will immediately changed in the Container too.

## Read-Only Volumes

Let’s have a closer look at the Bind Mounts volume. The idea behind bind mounts is that we can edit our source code, and changes will be available inside Container → The Container shouldn’t be able to WRITE the /app folder. The Container shouldn’t be able to change the files, only we can change it via hostfs.

→ We want to enforce the bind mount volume into read-only (by default, volumes are read + write) by adding :==**ro (stands for read-only)**== after the Container’s internal path. This ensure that Docker will now not be able to write into this folder or any of it’s subfolder, but we can change it on our host machine.

  

```Docker
docker run -d -p 3000:80 --name node-container -v feedback:/app/feedback 
-v "/home/songphuc1910/Desktop/docker-learn:/app:ro" node-image:latest
```

Here we should ensure that writing is possible for ==**temp**== and ==**feedback**== folder (since we store data in there). The logic is the same as before (using Anonymous Volumes)

  

```Docker
docker run -d -p 3000:80 --name node-container -v feedback:/app/feedback 
-v "/home/songphuc1910/Desktop/docker-learn:/app:ro" node-image:latest
```

==**/app/feedback:**== Here ==**feedback**== folder is already good, because it has a longer internal path than ==**/app**== → It will not be overridden by the bind mount → It’s writable

But the ==**temp**== folder is not specifid, we need to bind it to an Anonymous Volume

```Docker
docker run -d -p 3000:80 --name node-container -v feedback:/app/feedback 
-v "/home/songphuc1910/Desktop/docker-learn:/app:ro" -v /app/temp
node-image:latest 
```

  

If we had the VOLUME the instruction in the Dockerfile, we should remove it, as we only override our bind mount if we specify the Anonymous Volume in the CLI

```Docker
...
VOLUME [ "/app/temp" ]
...
```

# Volumes and Bind Mounts Overview

![[/image 6 4.png|image 6 4.png]]

  

## Comparison

![[/image 7 4.png|image 7 4.png]]

### Anonymous Volume

- Can be useful for locking in certain data, which already exists in the Container. They can be useful for avoiding a specific data to be overridden by another module (like the case of node_modules)
- Create a counterpart(bản sao, bản đối chiếu) of folder ==**on your host machine**== → Docker doesn’t have to store all the data inside the Container, and doesn’t have to managed all the data inside that Container’s read write layer, but it can outsource certain data to our host machine file system → better performance
    
    EX:
    
    ```Docker
    ...
    VOLUME [ "/app/temp" ]
    ...
    ```
    

  

### Named Volume

- Cannot be created in Dockerfile, but by using ==**-v flag**==
- Data in named volumes can be shared between Containers

  

### Bind Mounts

- fs stands for ==file system==
- Data in bind mounts can be shared between Containers

  

# Managing Docker Volumes

To see all Volumes:

```Docker
docker volume ls
```

It will show us Volumes that are managed by Docker, which means Bind Mounts won’t show up

  

We can create a Docker volume by our own

```Docker
docker volume create [OPTIONS] [VOLUME]
\#docker volume create feedback-files
```

If we haven’t created yet when we -v in `docker run`, Docker will create for us

  

To remove a Volume

```Docker
docker volume rm [VOLUME]
```

  

To inspect a Volume

```Docker
docker volume inspect [VOLUME]
```

If it’s Read-Only, we can see it at “Options”

  

To rm unused Volumes all at once:

```Docker
docker volume prune
```

# COPY vs Bind Mounts

Question:

> Why do we `COPY . .` even when later we will override it anyway (node_modules is created by npm install). So can we remove the `COPY . .` step?

→ Well basically yes, ==**we can remove**== `COPY ..` in Dockerfile, and it would ==**still run fine**== when we run the Container, ==**if we use a Bind Mount**==. But there is one important thing about Bind Mount:

The docker run command with Bind Mount is ==**used during development**== to ==**reflect changes in our code instantly**==, but once we ==**done with developing**== and put the Container to the server and we want to run it there, ==**we will not run with the command with Bind Mount**== (we can still use other Volumes to ensure that data survives), because if a Container is running in production on server, there is no connected source code which updates while it’s running → ==**In production**==, we ==**always want to have a snapshot of our code**==

  

  

# Don’t COPY everything using .dockerignore files

We can restrict (specify which folders and files should not be copied by COPY instruction) what to COPY using .dockerignore file

.dockerignore

```Docker
node_modules
```

We can add node_modules in the .dockerignore files to avoid overriding, outdated packages (since we always run npm install), and faster COPY process

# ARGuments & EVNironment Variables

![[/image 8 4.png|image 8 4.png]]

Docker supports build-time ==**ARG**==uments and runtime ==**ENV**==**ironments** variables

  

## ENVironment

Are available inside of the Dockerfile like ==ARG==, but also available in our entire application code in our running application. And we can set them with the ==**--env**== option on `docker run` or ==**ENV**== inside the Dockerfile

In Nodejs, we can access EVN variables by using process

For example, let change the listening port to ENV variable

```JavaScript
app.listen(process.env.PORT);
```

PORT is the ENV var name (they are typically uppercase)

  

3 Ways of declaring ENV variables

### Dockerfile

We can set ENV vars in the Dockerfile using ==**ENV**==

And we can call the ==**ENV**== var using ==**dollar sign**== (==**$**==**)**

Syntax:

```JavaScript
ENV VARIABLE_NAME DEFAULT_VALUE
```

  

```JavaScript
FROM node

WORKDIR /app

COPY package.json .

RUN npm install

ENV PORT 80

COPY . . 

EXPOSE $PORT

CMD ["node", "server.js"]
```

→ The default value of PORT is 80

  

### CLI (docker run)

Using the CLI, we can configure the ENV variables when we run a Container, we’re not limited to the hard-coded value, it’s just a default value (ENV PORT 80)

It will override the default value we specified in Dockerfile, using (multiple) ==**--env (or -e)**== flag

```Docker
docker run -p 3000:8080 --env PORT=8080 -e VAR_NAME=VALUE...
```

Now we don’t have to rebuild the image whenever we need to change ==**ENV**== vars

### File

We can create a file to contain ==**ENV**== vars. It oftens named ==**.env**==

.env

```Plain
PORT=8080
VAR_NAME=VALUE
...
```

Then, we can specify the file in the CLI command, using ==**--env-file**== flag

```Docker
--env-file FILE_PATH
```

```Docker
docker run -p 3000:8080 --env-file ./.env ...
```

We use ./ since it’s in the same folder as the terminal location

→ We can rerun the same command, and just adjust the file ==**.env**==

## ARGument

Allows you to set flexible bits of data (variables) in our Dockerfile, which we can use in there, to ==**plug different values into certain Dockerfile instructions,**== without having to hardcoded these values into the Dockerfile based on ==**ARG**==uments that are provided with ==**--build-arg**== option when we run `docker build`

```Docker
FROM node

WORKDIR /app

COPY package.json .

RUN npm install

ENV PORT 80

COPY . . 

EXPOSE $PORT

CMD ["node", "server.js"]
```

As the PORT default value being hardcoded to 80, it’s might make sense and might not be a problem at all, but maybe we want to make the default value flexible, so that when we build the image, we can actually build the Image based on one or the same, unchanged Dockerfile multiple times with different default values → We can achieve that with build-time ==**ARG**==**uments**

  

```Docker
FROM node

ARG DEFAULT_PORT=8080

WORKDIR /app

COPY package.json .

RUN npm install

ENV PORT 80

COPY . . 

EXPOSE $PORT

CMD ["node", "server.js"]
```

The ==**ARG**==**uments** can’t be used in our code, we can only use it in our Dockerfile, and even there, we can’t use it on every instruction. For example, we can’t use it on CMD, since it is a run-time command (execute when the Container start). We can even use it on ==**ENV**== instruction

```Docker
...

ARG DEFAULT_PORT=8080

...

ENV PORT $DEFAULT_PORT

...
```

→ We can set a dynamic argument which then set as a default value for the dynamic environment variable

  

Now we can build our Image

```Docker
\#docker build -t node-app:web-app .
docker build -t node-app:development --build-arg DEFAULT_PORT=8000 .
```

And now we again building an Image on a different tag with a different ARG value

→ It can be useful to be able to lock certain value in when we build an image, and we can build image different images in a flexible way, without having to change the Dockerfile

We should ==**declare ARG where we are going to use it right after we declared**==, not at the beginning of the file, because it just like other instructions, it adds layers to your Dockerfile, which mean ==**when something changed above, all subsequent layers are rebuild**==

```Docker
FROM node

...

ARG DEFAULT_PORT=8080

ENV PORT $DEFAULT_PORT

...
```

  

→ ==**Args**== and ==**ENVs**== variables allow us to create more flexible Images and Containers because we don’t have to hard-code everything into these Containers and Images, instead we can set it dynamically when we build an Image or even only when we run a Container

# Summary

![[/image 9 4.png|image 9 4.png]]

![[/image 10 4.png|image 10 4.png]]