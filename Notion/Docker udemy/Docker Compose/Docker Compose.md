# What is Docker Compose. Why?

Docker Compose is a tool that allows you to replace `docker build` and `docker run` commands, and there might be multiple `docker build` or `docker run` commands with just one configuration file and a set of Orchestration Commands to start all those services, all these Container at once, and build all neccessary images if it’s required, and we can use one command to stop everything

→ Docker compose shines the most if we have multiple Containers

![[/image 145.png|image 145.png]]

  

## Docker Compose will ==NOT==:

- Replace Dockerfiles for custom image. It works together with Docker Compose
- Replace Images or Containers. It just make working with them easier
- Suited for managing multiple Containers on different hosts (machines). It’s really great for managing multiple Containers on the same host.

![[/image 1 7.png|image 1 7.png]]

  

## To start Docker Compose

We always start by writing a Docker Compose file. In this file, we just define a couple of core things, components that make up our multi-container application.

The most hard and most important element or thing we can define in a Compose file and we MUST define in a Compose file are the ==**Services (Containers)**== that make up our multi-container application. And then, below every Service, we can configure that Service. We can do basically everything we can do with Docker Command in the terminal.

![[/image 2 6.png|image 2 6.png]]

# Creating a Compose File

Start by creating a file called docker-compose.==**yml**== or .==**yaml**== (a text format)

In yaml, we will use ==**indentation**== (thụt lề) to express dependencies between configuration options, and key: value pairs (need to be typed correctly).

We ==**used to**== define version, but now it’s ==**obsolete**==, therefore no need.

  

```Docker
services:
	mongodb:
		image: 
	backend:
	
	frontend:
```

==**services**== take nested values, and need at least 1 child element

Children of services key are ==**Containers**==. And since our application got 3 Containers, we will have 3 children below services

  

Now let’s translate the commands

```Docker
docker run --name mongodb \
-e MONGO_INITDB_ROOT_USERNAME=max \
-e MONGO_INITDB_ROOT_PASSWORD=secret \
-v data:/data/db \
--rm \
-d \
--network goals-net \
mongo
```

  

```Docker
services:
	mongodb:
		image: 'mongo'
		volumes: 
			- data:data/db \#We could add option like :ro
		environment:
			MONGO_INITDB_ROOT_USERNAME: max
			- MONGO_INITDB_ROOT_PASSWORD=secret
		# networks:
			- my-network
	backend:
	
	frontend:
	
volumes:
 data:
```

We don’t need to specify ==**--rm and detached mode**==, bydefault, when we bring our services down, they will be remove, and we can specify detached mode when we start our services together

  

There are 2 ways to specify ==**environment**== variables

We can also add ==**environment variables file**==

```Docker
services:
	mongodb:
		...
		# environment:
			# MONGO_INITDB_ROOT_USERNAME: max
			# - MONGO_INITDB_ROOT_PASSWORD=secret
			env_file:
				- ./env/mongo.env
	backend:
	
	frontend:
```

The relative path is at the directory where the yml file located

We will need a dash (-) whenever it’s a single value

==**- MONGO_INITDB_ROOT_PASSWORD=secret**==

==- ./env/mongo.env==

But if we have a key value pair, and a colon (:), white space, value, then it will create a yaml object → No need dash

  

For Named Volumes, we ==**should**== add the ‘volumes’ key next to ‘services’, at the same level of services (not indented). Any Named Volumes we used in our services have to be listed there. We simply add the name with the colon (:), but without value after it. If we use the same Volume name in different services, the Volume will be shared → Different Container can use the same Volume, the same folder on our hosting machine.

Anonymous Volume and Bind Mount don’t need to be specified there.

  

We can add ==**Network**== by adding ‘==**networks**==’ key and specify all the Networks the Container should belong to. But in many cases, we ==**don’t need to do that**==, because when we use Docker Compose, Docker will ==**automatically create a new Environment**== for all the ==**services specified in the Compose File**==, and it will add all the services to that Network out-of-the-box

→ All these services, which are defined in the same Compose File, will already be part of the same Network that was created for them by Docker

In case we add our own network, that service will not only be added to the default network that was created, but also to the network we specified

  

## backend service

```Docker
...
backend:
    build: ./backend
```

The build key is `docker build` . It takes the context (path) of the Dockerfile and build it into an image.

If the file’s name is not Dockerfile but something else like Dockerfile-dev, we can write it as the object:

```Docker
...
backend:
    build:
	    context: ./backend
	    dockerfile: Dockerfile-dev
```

Translate these commands:

```Plain
---------------------
Build Node API Image
---------------------

docker build -t goals-node .   //This is the 'build' key above

---------------------
Run Node API Container
---------------------

docker run --name goals-backend \
  -e MONGODB_USERNAME=max \
  -e MONGODB_PASSWORD=secret \
  -v logs:/app/logs \
  -v /Users/maximilianschwarzmuller/development/teaching/udemy/docker-complete/backend:/app \
  -v /app/node_modules \
  --rm \
  -d \
  --network goals-net \
  -p 80:80 \
  goals-node
```

  

```Plain
...
backend:
    build: ./backend
    ports: 
	    - '80:80'
		volumes: 
			- logs:/app/logs
			- ./backend:/app
			- /app/node_modules
		env_file:
			./env/backend.env
		depends_on: 
			- mongodb
			- ... 
volumes:
	data:
	logs:
```

==**ports:**== allow us to specify published ports. We specify it as a list → We need a dash  
./backend:/app : Bind Mounts the whole backend folder to /app. In Docker Compose, we can specify as relative path (./backend) (at docker-compose.yml), while in  
`docker run` command, we have to specify as an ==absolute path.== And Bind Mount don’t need to be specified at the volumes key at the last line, and CAN’T be specify in it.

For environment variables, we will create a file called backend.env

==**depends_on**== key is Docker Compose exclusive (not in docker run) because Docker Compose create and launch multiple services (Containers), and sometimes, one Container might ==**depends on**== another Container to run. As we can tell, backend depends on mongodb and mongodb needs to be already up and running → ==**depends on**== will ensure that the mongodb will be up and running before backend service is created.

  

## frontend service

Translate:

```Plain
Build React SPA Image
---------------------

docker build -t goals-react .

---------------------
Run React SPA Container
---------------------

docker run --name goals-frontend \
  -v /Users/maximilianschwarzmuller/development/teaching/udemy/docker-complete/frontend/src:/app/src \
  --rm \
  -d \
  -p 3000:3000 \
  -it \
  goals-react
```

```Plain
frontend:
    build: ./frontend
    volumes:
      - ./frontend:/app/src
    stdin_open: true
    tty: true
    depends_on:
      - backend
```

==**-it**== mode: interactive mode (-i: interactive, -t: tty)  
  

stdin_open: true : let Docker knows this service needs to open input connection  
tty: true : attaching terminal  

  

We can force rebuild the image using `build`, since Docker will not do it (unless there are some changes in the image)

```Docker
docker-compose build
```

  

We can change name of Container:

```Docker
mongodb:
	image: 'mongo'
	container_name: mongodb \#Old name would be folder-name_mongodb_1
													\#New name: mongodb
```

# Docker Compose Up & Down

To start services with Docker Compose, we come to the directory where yaml file located, and with command:

```Docker
docker-compose up
```

It will start all the services defined in the Compose File, and will not just start the Containers, it also pull and build all the Images that might be required

  

To stop all services and remove all Containers, we use:

```Docker
docker-compose down -v
```

It does not delete Volumes, to delete Volumes, we have to add ==**-v**== flag