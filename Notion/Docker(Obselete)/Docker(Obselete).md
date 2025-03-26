**Container** has revolutionized how software is packaged and shipped

Application works in one place but not others is a common problem in the world of software development. And the reasons are:

- Missing tools (the other machine doesn’t have)
- Different configuration
- Hardware dependencies: Maybe a block of source code in our app depends on files or hardware on our computer that other computers don’t have

> Docker is a software that allows Developers to ==**packaged**== their apps into ==**images**== that run on ==**containers**==

**Images** are created from lightweight configuration files that **describe everything your app needs to run**

**Containers** are **virtualized operating systems** that are configured with just enough to run your app

→ **Containers are created from images**, as long as the machine can run Docker, your app will run and behave the same, regardless of where it is.

  

# Containers vs. Virtual Machines

> Containers are not smaller VMs

The biggest difference is that, **VMs** virtualize hardware, where as **containers** virtualize operating system kernels.

  

![[/Untitled 5.png|Untitled 5.png]]

==This diagram describes how applications run on containers on the left, and VMs shown on the right==

  

![[/Untitled 1 3.png|Untitled 1 3.png]]

  

# CLI

On Windows, we will use Powershell

Every commands accept options(or flags) to provide additional information to your request (--help can be used by every command)

  

# Create a Docker container

Containers are created from **container images**. Container images are a **compressed and pre-packaged file system**, that contain your app along with its environment and configuration, with an instruction on how to start your application. That instruction is called ‘The entry point’

  

**First**, we need to tell Docker to create a container from an image. If an image does not exist on your computer, Docker will try to retrieve it from a container registry. By default, Docker always tries to pull from Docker Hub

![[/Untitled 2 3.png|Untitled 2 3.png]]

‘docker container create’ requires the name of the image(IMAGE), it also accepts additional information(flags). **Square brackets** means ==**optional**==

  

![[/Untitled 3 3.png|Untitled 3 3.png]]

‘:linux’ is to get a linux specific version of this ‘hello-world’ image. This annotation is called an ‘image tag’

Every container created by Docker has an ID associate with it, this make it easy to interact and inspect them.

The docker create command creates containers, but it does not actually start them. This means that it **does not execute the image’s entry point automatically**

  

![[/Untitled 4 2.png|Untitled 4 2.png]]

‘docker ps’ will only show us containers that are actively running. You will see this with containers running server or other long-lived application. Nginx is a great example. Since Nginx is a web server, it’s expected to run forever or until the system kill it

  

To see all the containers docker knows about, we add the flag(option): ‘--all’

![[/Untitled 5 2.png|Untitled 5 2.png]]

As we can see the container we just created is in ‘Created’ state, that means we just need to start it

![[Untitled 6.png]]

To start it, we need the id of the container. It will print out the id if it starts successfully

![[Untitled 7.png]]

The (0) (zero exit code) means that the entry point was executed successfully. ==**Any other exit code(not 0), likely means that there was a problem**==

  

To see the log messages, we will use: ‘docker logs ID’

At the id, we can either put first 3 characters of the ID or the whole ID, both are fine

![[Untitled 8.png]]

![[Untitled 9.png]]

Docker log is really helpful in troubleshooting containers that have failed

  

We can also get this output without using ‘docker log’ every time by using

==**--attach**== when start the container

![[Untitled 10.png]]

  

## Short way to create a container

It would be inconvenient if we had to **create a start containers explicitly** every time we want to run our app, it would also be inconvenient to use ‘docker ps’ to know whether our app ran successfully, and then use ‘docker logs’ to see its output → Docker creates a single command to combine all these steps into 1, which is ==**‘docker run’**==

![[Untitled 11.png]]

We can see all the steps Docker did

![[Untitled 12.png]]

![[Untitled 13.png]]

  

## Create a Docker container from our Dockerfiles

[https://docs.docker.com/reference/dockerfile/](https://docs.docker.com/reference/dockerfile/)

Use when we try to create a container for our own app

This is a docker file

![[Untitled 14.png]]

‘FROM tells Docker which existing Docker image to base your Docker image off of. This can be any existing image, either local or from the internet. By default, Docker will try to get this image from Docker Hub if it’s not already on your machine.

Some images will contain a label(’LABEL’) adding additional data, like the maintainer of this image

‘USER’ tells Docker which user to use for any Docker file commands underneath it. By default, Docker will use the “root” user to execute commands. The ‘USER’ keyword is useful in changing a user that your app runs as to one that is less powerful, lilke “nobody”.

‘COPY’ copies files from a directory provided to the Docker build command to the container image. The directory provided to Docker build is called the “context”. The “context” is usually your working directory, but it does not have to be

‘RUN’ statements are commands that customize our image. This is a place to install additional software, or configure files needed by your application

Here, we’are using the ‘USER’ command to set the default users for containers created from this image to the powerless “nobody” user. This ensures that we can’t break out of the container, and potentially change important files on our host

‘ENTRYPOINT’ tells Docker what command containers created from this image should run

  

**Now, let’s turn the Docker file into a Docker image and start our container from it**

We are going to use ‘docker build’ command to do this

![[Untitled 15.png]]

using -t to give it a name so we don’t have to remember the ID

![[Untitled 16.png]]

Docker files look for a file called Dockerfile by default, so we don’t need to change anything since our file is named “Dockerfile”. But if the file is named something else, we have to add -f (or --file), follows by the file name

![[Untitled 17.png]]

Now we need to tell Docker where its context is. Context is simply the ==**folder containing files that Docker will include in your image**==. Since the entry point script is in our working directory already, we can simply put a period here. If it’s in on another path, add the path instead of the period(path/app/…)

![[Untitled 18.png]]

Now build it

![[Untitled 19.png]]

Then we will ‘docker run’ the image with the tagged name(our-first-image)

![[Untitled 20.png]]

  

**Now we will create a server container from image(03_06 of exercise)**

First build it

![[Untitled 21.png]]

Then run it

![[Untitled 22.png]]

Now actually, since it a server, it will run until we force it to stop. But pressing CTRL-C doesn’t work. To solve the problem, we will have to open the second terminal, use ‘docker ps’ to get its ID, and ‘kill’ it

![[Untitled 23.png]]

Now we will see that it’s stopped

![[Untitled 24.png]]

  

We will fix this with a flag: docker run ==**-d**==

This will going to create and starts our server, but does not attach our terminal to it

![[Untitled 25.png]]

  

If we want to interact with a running Docker container, we can use:

![[Untitled 26.png]]

- `docker exec`: This is the main command used to execute a command inside a running container.
- `-interactive` or `i`: Keeps STDIN open even if not attached, allowing for interactive input.
- `-tty` or `t`: Allocates a pseudo-TTY, which essentially provides a terminal interface.
- `f72`: This is the first few characters of the container ID or the container name. Docker allows you to use just the first few characters of the container ID as long as they uniquely identify the container.
- `bash`: This is the command that will be executed inside the container. In this case, it's launching a Bash shell.

And now, we can run additional commands. To exit, press CTRL-D

  

# Stopping and Removing containers

By default, Docker does not stop or remove containers for you

To stop a running container, use ‘docker stop ID’

![[Untitled 27.png]]

It could take some time to stop a running container, because Docker attempts to gracefully stop the program the container is running when you run this command. To immediately stop it, we can use:

![[Untitled 28.png]]

But be careful with this, since it can cause data loss on the application that you are stopping

  

To remove containers, we will use ‘docker rm ID’

![[Untitled 29.png]]

‘docker rm’ will not stop container that are running, if we want to do that, we need to add ‘-f’ to the end of the command

  

To remove all the containers, we will use :

![[Untitled 30.png]]

docker ps -aq means that it will only list out the id of container

  

To remove image, we will use

![[Untitled 31.png]]

Sometimes it will failed to remove images, to solve this, we will stop the container that are running and uses that image first, then we will stop it

  

# Binding ports to your container

Up to this point, all the containers we’ve created use data imports within themselves. This is somewhat useful, but not nearly as useful as being able to access data imports from your own computer into those containers.

First, let’s exporing accessing container network services from our host.

Port binding: provides the ability to access network ports within the container. This feature allow Docker to take a port on our machine, and map it to a port within the container.

==Quản lý port trong Docker bao gồm việc thiết lập và cấu hình các port để các container có thể giao tiếp với nhau và với mạng bên ngoài. Các khái niệm chính bao gồm:==

  

## -p flag

[https://www.youtube.com/watch?v=6by0pCRQdsI&list=WL&index=1&t=68s](https://www.youtube.com/watch?v=6by0pCRQdsI&list=WL&index=1&t=68s)

> ==docker run== **-p 8080:80**

8080:80 is port configuration

### Explaination

![[Untitled 32.png]]

Host machine: our OS(Windows, Linux, MacOS,…)

Docker Container is isolated from the host machine. Only people inside the Container 1 can access the this particular Apache web server, it is not exposed to the outside world because it’s isolated from the host machine

→ No one can access these 2 docker containers without going inside to the docker container. So to go inside the container, we have to do the ‘exec’ command and we can interact with the container, but that is not a good scenario

![[Untitled 33.png]]

Let say our host machine has port 8080, and it will be linked to the apache http server port 80, which is running inside the docker container using the -p

→ That means, we will access port 8080 on our host machine, and that will be **forward all the requests** to the docker container on port 80

Syntax

```HTML
docker -p <host-port>:<docker-port> imageName
```

host-port can be anything by ur preference

  

  

# Saving data from containers

Containers are meant to be disposable. When they are deleted, they’re deleted for good. This includes any data you’ve saved within them

```HTML
docker run --rm --entrypoint sh ubuntu -c "echo 'Hello there.' > /tmp/file && cat /tmp/file”
```

---

- `docker run`: Starts a new Docker container.
- `-rm`: Automatically removes the container when it exits.
- `-entrypoint sh`: Overrides the default entrypoint of the container with the `sh` shell.
- `ubuntu`: Specifies the Docker image to use (Ubuntu in this case).
- `c`: Tells the shell to execute the following string as a command.
- `"echo 'Hello there.' > /tmp/file && cat /tmp/file"`: This is the command to be executed inside the container. It does two things:
    - `echo 'Hello there.' > /tmp/file`: Writes the text "Hello there." to a file named "file" in the /tmp directory.
    - `&& cat /tmp/file`: After the echo command succeeds, it uses the `cat` command to display the contents of the file.

In summary, this command starts a temporary Ubuntu container, creates a file with the text "Hello there.", and then displays the contents of that file. After execution, the container is automatically removed

==In the Docker command you provided, specifying== ==`sh`== ==as the entrypoint serves a specific purpose:==

1. ==Overriding the default entrypoint:==
    - ==Many Docker images have a default entrypoint or command that runs when the container starts.==
    - ==By using== ==`-entrypoint sh`====, we're overriding this default behavior.==
2. ==Providing a shell environment:==
    - ==The== ==`sh`== ==shell allows us to execute complex commands or scripts within the container.==
    - ==It gives us a flexible way to run multiple commands in sequence.==
3. ==Command execution:==
    - ==The== ==`c`== ==flag that follows allows us to pass a string of commands to the shell.==
    - ==This is useful for running a series of commands in a single Docker run command.==
4. ==Temporary execution:==
    - ==Combined with== ==`-rm`====, this approach lets us run commands in a container without leaving it running or needing to attach to it interactively.==

==In this specific case, using== ==`sh`== ==allows us to:==

1. ==Echo text into a file==
2. ==Cat the contents of that file==
3. ==Do this all in one command, within a temporary container==

==If we didn't specify== ==`sh`== ==(or another shell), we would be limited to running a single command directly, without the ability to chain commands or use shell features like output redirection (====`>`====).==

When we show the content of the file:

```HTML
cat /tmp/file
```

It will show “No such file or directory”.

To save stuff after the containers exist, we will use

-v or --volume, which map a folder on our computer to a folder in the container. And it also uses **outside:inside**

  

```HTML
docker run --rm --entrypoint sh -v /tmp/container:/tmp ubuntu -c "echo 'Hello there.' > /tmp/file && cat /tmp/file”
```

We are mapping /tmp/container in out computer directory to /tmp of the docker container

So when we show the file content:

```HTML
cat /tmp/container/file
```

It will show the file content, even after it stop existing

  

# Docker Hub

A container image registry is a place for storing and tracking container images. Container images are tracked by their tags, a string combining the name of the image, and optionally its version with a semicolon(:)

  

Docker is the default registry used by the Docker client. This is a publicly accessible registry that anyone can push images to. Whenever you push images or whenevery you pull images from the ‘FROM’ line of a Docker file, it will fetch an image from Docker Hub

To login on CLI, use ‘docker login’

To create and push the image:

```HTML
docker tag our-web-server songphuc191004/our-web-server:0.0.1
```

First we need to tell Docker that this image is going to be pushed into a registry. To do that, we need to rename the image so that it contains our username: ==**docker tag**== **nameOfAnImageNeedRenaming newNameOfImage**

0.0.1: is the version, if we don’t put version, it will be marked as latest(could be confused as every image is mard as latest)

To push, use docker push

```HTML
docker push songphuc191004/our-web-server:0.0.1
```

![[Untitled 34.png]]

If we push the same image with different tag:

![[Untitled 35.png]]

![[Untitled 36.png]]

We can see different tags here, in the same image

To delete the repository, we can only delete it on the browser