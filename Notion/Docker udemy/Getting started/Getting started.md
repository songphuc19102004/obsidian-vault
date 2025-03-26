# What is Docker? And Why?

Docker is a ==**container**== technology: A tool for creating and managing containers

==**Container:**== ==A container technology is a lightweight, executable unit of software that packs up application code and dependencies such as binary code, libraries, and configuration files for easy deployment across different computing environments. It’s often referred to as the operating system virtualization as it can run a software, microservice, or an application.==

→ The ==**same container**== always yields the ==**exact same application and execution behavior!**== No matter where or by whom it might be executed.

→ Docker simplifies the creation and management of containers

## Why containers?

> Why would we want ==independent, standardized “applications packages”==?

- Main use case: We often have different Development & Production Environments.
    
    Ex: We have development code where we use a function only available on higher version of nodejs or .net core, and we want to test it first in the development environment, before deploy to production.
    
    ![[/image 141.png|image 141.png]]
    
    ![[/image 1 3.png|image 1 3.png]]
    

  

- Different Development Environments within a team/company
    
    Ex: We are in a big team, i work here, you work there. We work on the same project. For some reason, my team is still using the old version of nodejs, and your team is developing a new function, which requires the newer version.
    
    ![[/image 2 2.png|image 2 2.png]]
    
    ![[/image 3 2.png|image 3 2.png]]
    
      
    
- Clashing(xung đột) tools / versions between different projects
    
    ![[/image 4 2.png|image 4 2.png]]
    
    ![[/image 5 2.png|image 5 2.png]]
    

# Virtuals machines Vs. Docker Containers

## VMs

![[/image 6 2.png|image 6 2.png]]

Your operating system: our host OS, like Windows, Linux,…

We install virtual machines on top of our host OS, and install all the needs in them

→This ==**works**==, but there are a ==**couple of problems**==

The biggest problem is that the virtual OS, and in general, the overhead( the resources required to set up an operation) we have with multiple virtual machines. Every VMs is like a standalone(độc lập) computer, standalone machine running on top of our machine. Therefore, if we have such machines, we have a lot of ==**wasted spaces and resources**==

![[/image 7 2.png|image 7 2.png]]

![[/image 8 2.png|image 8 2.png]]

  

## Containers

![[/image 9 2.png|image 9 2.png]]

Another great thing about containers is that we can ==**configure and describe**== them with ==**configuration file**==, and then share that file with others so they can ==**recreate the container**==, or we can also ==**build the container into an image**==, and then we can share that image with others to ==**ensure that everyone is able launch that same container**== that we have on our system

  

## Summary

![[/image 10 2.png|image 10 2.png]]

# Docker setup

![[/image 11 2.png|image 11 2.png]]

Linux OS natively supports containers and technology that docker uses

  

## Docker Tools & Building blocks

In the end, we install Docker Engine, which is required to run Docker

![[/image 12 2.png|image 12 2.png]]

Daemon: a process that keeps on running and ensure that Docker works (The heart of Docker)

# Practice (Quick walkthrough)

Dockerfile

```Docker
FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "node", "app.mjs" ]
```

In this file, we ==**describe**== to docker ==**how our container**== in the end should ==**set up**==, by using ==**instructions**==.

  

FROM node:14 : means use Nodejs as a base image

WORKDIR /app: a certain directory in the container file system (==**every container has their own file system**==)

COPY package.json . : copy package.json file into our working directory

RUN npm install: then we run npm install command to install all the dependencies the application needs

COPY .. : copy the rest of the code

EXPOSE 3000: we expose port 3000 to the outside world(this nodejs app creates a web server listening for incoming requests on port 3000)

CMD [ "node", "app.mjs" ]: and then we execute the app.mjs with the node command, which is available because we are running in a node environment

  

After that we run this command on the VSC terminal

```Docker
docker build .
```

it will ==**find the Dockerfile by default**==, and build image based on it

after that we will run it

```Docker
docker run -p 3000:3000 141...(id)
```

Our container has a port(3000) to which we want to communicate, and we need to publish that port on the container which we want to run (by adding the ==**-p**== flag). Which means we can ==**use our**== ==**[localhost](http://localhost)**== ==**on our local system to reach the application running on port 3000 in the container**==, because ==**by default there is no connection between container and our host OS**==. For example, if we want to send HTTP request to the application running in the container, we need to open up the port on the container to which we want to communicate, otherwise it’s a ==**lock network in the container and we can’t reach it outside**==

![[localhost3000.png]]