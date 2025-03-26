> “Utility Containers” is not an official term

Up to this point, we have always work with Application Containers (primary point of Docker)

That doesn’t mean we can’t use Docker for other things

→ Utility Containers are Container which only have a certain Environment in them (nodejs, php,…). The idea is that they don’t start an application when you ==**run**== ==them, but instead we run them in== ==conjunction== ==with some commands specified by us to then execute a certain task.==

→ Utility Containers are Containers that are used to provide specific utility functions, rather than running application or services.

→ We can use Utility Containers by `docker run`, but it would be more convinient with

`docker-compose run`

![[/image 146.png|image 146.png]]

## Problem

We have an empty folder, and we want to create a Node application in there. We can create a Node Application without installing Node, but it’s cumbersome (because we typically create Node app by npm init, but to use npm, we have to install Node). But by using Docker, we don’t want to install any Environment but using the Environment within a Container itself. But to set up the application on the first place, we basically need to install Node, in order to run the `npm init` command, unless we want to type package.json manually.

  

## First approach

First, let run the node image

```Docker
docker run node
```

It will just stop, since we need to run it in interactive mode, and input mode

```Docker
docker run -it node
```

Now, we will run it with interactive mode and ==**detached mode**==**(it still run internaly and wait for input)**

```Docker
docker run -it -d node
```

  

```Docker
docker exec NAME_CONTAINER COMMAND
```

```Docker
docker exec example_name npm init
```

  
docker exec command allow us to execute certain commands inside a running Container ==besides the default command this Container execute== (in this case, besides the commands that might have been specified in Dockerfile of Node). That command still continue running, so the application still continues to run, but we can run ==additional== commands inside the Container.

By executing npm init, it will create a Node project in that running Node Container.

But when it start to ask package name, it will finished immediately, because `docker exec` is just like `docker run`, need to be started in ==interactive== mode if we want to stay connected to that process, or provide input

```Docker
docker exec -it example_name npm init
```

And that’s how we run `npm init` without Node and npm being installed on our system, because it runs inside the Node Container

With `docker exec` , we can execute commands in a running Container without interupting the default commands, which starts when the Container starts. We also can override the default command. When we start the Node Container, the default command that is executed in the Container is ==node executable== itself. We will override the default command by adding the new command after the image name

```Docker
docker exec -it node npm init
```

docker exec could be useful for read some log files which were written inside the Container, without interupting the main process  
  

But it still not too useful here, because if it’s in the Container (we create the project in there) we have no access to it. But it just a demo to show what we can do.

By combining with ==**Bind Mounts,**== we can mirror the file created by `npm init` to our file onto our host machine

```Docker
docker run -it -v /home/songphuc1910/Desktop/empty:/app npm init
```

→ We can create ==package.json== with `npm init` ==**without installing Node**== on our host machine

  

## ENTRYPOINT instruction

To understand ENTRYPOINT, we first have to understand its similar instructions:

### RUN

It runs when we are building an image → It’s a layer in the image

### CMD

It runs when the Container is created. We can override this default command in command line when run the Container

### ENTRYPOINT

It also run when the Container is created. But we can’t override this default command, instead we append it.

Example

```Docker
FROM node

WORKDIR /app

ENTRYPOINT [ "npm" ]

# CMD [ "--help" ]
```

Now, when we add a command in the command line when running the Container, it will actually append after the npm.

Example:

Without ENTRYPOINT

```Docker
docker run -it node-util npm install
```

With ENTRYPOINT + CMD [”help”]

```Docker
docker run -it node-util install 
```

We can also use CMD [”help”] to act as a default arguments passed to the ENTRYPOINT. So if no commands is specified when running the Container, it will default to running `npm --help`

→ But the downside is we have to write long command, let’s use Docker Compose!

  

```Docker
services:
  npm:
    build: ./
    volumes:
      - ./:/app
    stdin_open: true
    tty: true
```

stdin_open and tty represents for -it (interactive mode and input)

Now, let’s run Docker Compose as usual

![[/image 1 8.png|image 1 8.png]]

It’s like we run npm command in terminal, because the entrypoint is npm and we didn’t provide any addition option (like init or install)

So let’s add some command

![[/image 2 7.png|image 2 7.png]]

No such services. Because docker-compose up is just mean services defined in docker-compose.yml file.

To add command, we have to use another command, which is `docker-compose run` . It allows us to ==**run a single service**== in the yml file

```Docker
\#docker-compose run SERVICE_NAME COMMAND
docker-compose run --rm npm init
```

When we start services with `docker-compose up` , they are automatically deleted when we use `docker-compose down` . But for `docker-compose run` , there is no up and down, so we have to add

--rm flag to remove when the command finished.