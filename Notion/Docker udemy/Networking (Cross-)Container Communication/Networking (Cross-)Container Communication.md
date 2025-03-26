# Containers & Networks: Connecting Containers

Example:

We have an app to send HTTP GET request to a film api using NodeJS

# Case 1: Containers to WWW Communication

## Containers & Network Requests

> What do we mean by saying network & requests?

Let assume that we have a Container without application inside (node app, python,…). Now let say that this application as part of this application logic, want to talk to an API, to some website out there in the WWW, for example, to send data there, or to fetch data from there.

![[/image 144.png|image 144.png]]

Out of the box (no need to configure), Container can request, communicate to WWW

# Case 2: Containers to Local Host Machine Communication

Sending HTTP GET request to a web API is not the only kind of communication we could have in Dockerized app. Maybe we also have something on our hosting machine (some web services, or a database,…) and we want to talk to that, from inside our Dockerized app → We still want to communicate with something outside the Container, something not a website, but instead it’s a service running on our host machine (not a service in another Container), like database.

![[/image 1 6.png|image 1 6.png]]

For example:

We have a mongodb at localhost:3000. In order to communicate, instead using localhost, we have to specify it as host.docker.internal, it will translate to our host machine ip address in our Docker Container.

![[/image 2 5.png|image 2 5.png]]

# Case 3: Containers to Containers Communication

The case when our application running in our Container, wants to talk to another Container

![[/image 3 5.png|image 3 5.png]]

Example: 1 Container for the Web API, 1 for Database (mongodb)

First we gotta run the mongodb Container, simply by using the existing image on Docker hub

```Docker
docker run -d --name mongodb mongo
```

## Basic solution

In this solution, we gotta inspect the mongodb Container and take it IP Address, and pass it to mongoose

![[/image 4 5.png|image 4 5.png]]

![[/image 5 5.png|image 5 5.png]]

![[/image 6 5.png|image 6 5.png]]

After that, run the move-app Container and we are good to go.

→ But this solution is not convinient, since we have to look up of other Containers’s IP Addresses to use it, and we also always have to build a new Image, whenever the mongodb Container’s IP Address changed, because we hardcoded it in the app

  

## Docker Networks Elegant

> With Docker, we can create Container Networks. So what are Networks?

The idea is we might have multiple Containers, and we want to allow communication between these Containers, so essentially this scenario we have, with the node Web API Container and the mongodb Container.

In Docker, we can put all these Containers into the same Network, by adding --network my_network option on `docker run` command. This will create a Network, in which all Containers are able to talk to each others. Docker will automatically doing the IP look up and resolving, which we do manually on the Basic Solution.

![[/image 7 5.png|image 7 5.png]]

Unlike Volumes, Docker will ==**not automatically create**== Network for you if it doesn’t exist. We have to ==**create it on our own**==

  

To create Docker Network:

```Docker
\#docker network create <name>
docker network create my-network
```

  

To list out all Networks:

```Docker
docker network ls
```

  

Now to use the Network we just created, we will add the ==--network== flag in `docker run`

```Docker
docker run -d --network my-network --name mongodb mongo
```

==As we can see, we don’t have the -p (publish port) flag in the command, since -p only is only required if we plan on== ==connecting== ==to something in that Container from out== ==[localhost](http://localhost)== ==machine, or from== ==outside the Container Network.== ==And since the only thing that connects to the mongodb Container is the favorites-container (name of the Container), which is part of the same Docker Network → If such cases, when we have a Container to Container connection, then we don’t need to publish the port, since in the same Docker Network, all the Containers of that Network can already communicate with each others.==

  

Containers in the same network can talk to each other. But how?

→ We can put the other Containers’ ==**names**== in it

```TypeScript
mongoose.connect(
'mongodb://mongodb:27017/swfavorites',
...
)
```

  

  
  

# How Docker Resolves IP Addresses

We can use ‘host.docker.internal’ and Container’s name as the domain, or address, and Docker will automatically resolve the IP Address

![[/image 8 5.png|image 8 5.png]]

Docker owns the Environment, in which the Containers run. If our application sends an HTTP Request or a mongodb request, or any kind of request that leaves the Container, then Docker is aware of that, and that point of time, where Docker is able to resolve the Address, the Container’s name, or ‘host.docker.internal’, and replace with the actual IP Address, since it is aware of the surrounding Containers and the host machine

→ If the request doesn’t leave the Container, or the request is generated, for example, in the browser, if the user visiting our web app and JS code is running in their browser, and the request is sent from there, then Docker is not doing anything, because it’s not replacing the source code.

# Summary

![[/image 9 5.png|image 9 5.png]]