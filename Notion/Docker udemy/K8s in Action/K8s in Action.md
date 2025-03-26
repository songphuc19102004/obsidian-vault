# Installation

![[/image 149.png|image 149.png]]

First we need to install (no matter where it’s our local machine or some cloud machine) Cluster. A Cluster with a Master Node and one or more Worker Nodes. After that, we also need to install softwares (services) which they needed (API Server, Scheduler, Kublet,…)

And then we need another tool locally on our machine: kubectl (Kube control):

- Create a new deployment
- Delete a deployment
- Change our running deployment

With kubectl, we as Dev can send instructions to Master Node, which then will do whatever it needs to do with the Worker Nodes. For example: we send instruction to create more Pods of a given Container (more instances of a given Container) → The Master Node will interact with the Worker Node, such that the extra Pods are created.

→ Kubectl is for talking with the Cluster

  

For setting up the Cluster locally, we can use a tool called **Minikube**

Minikube is a tool we can install locally for playing around with K8s and for testing it, and it will use a VM on our local machine to create a Cluster in there.

→ It simulates an another machine on our machine by using such a VM, and this VM hold the Cluster. It will create a single Node Cluster, which means there, the Worker and Master Node is combined into 1 single VM (not something we wouls do in Production).

→ Minikube is good for getting started with K8s and for testing thing locally

==Note: Minikube doesn’t replace the Kubectl tool, we always need to Kubectl command locally, even if we don’t use Minikube, since that’s the tool we use to communicate with our Cluster, no matter if it’s a Cloud Cluster or a Minikube Cluster==

## Steps

- First install chocolatey package manager
- Then follow this site instruction
    
    [https://kubernetes.io/vi/docs/tasks/tools/install-minikube/](https://kubernetes.io/vi/docs/tasks/tools/install-minikube/)
    
    ==Installing minikube will automatically install kublet-cli  
      
    ==To check if it works
    
    `minikube version --client`
    
- Next, create a .kube directory in our %USERPROFILE%

![[/image 1 10.png|image 1 10.png]]

Then create a config file without extension in the .kube directory

  

- To create a Cluster:
    
    ```Docker
     minikube start --driver=<driver-name>
     minikube start --driver=docker
     minikube start --driver=hyperv
    ```
    
    We will use docker here, for simplicity
    
    To check if it sucessfully created:
    
    ```Docker
    docker ps
    ```
    
    and
    
    ```Plain
    minikube status
    ```
    
    ![[/image 2 9.png|image 2 9.png]]
    
    We can also see its’ dashboard by typing
    
    ```Plain
    minikube dashboard
    ```
    
    ![[/image 3 7.png|image 3 7.png]]
    

# Understanding K8s Objects

After creating the minikube setup and having the VM with K8s Cluster up and running, we can start interacting with that Cluster and start sending commands to that Cluster to get K8s to do something. To do that, we need to understand how K8s works internally

  

K8s works with **Objects**

![[/image 4 7.png|image 4 7.png]]

The idea behinds these Objects is that we can create an object by executing certain commands, and K8s will take the object created by us and do something based on the instruction encoded in that object.

Objects can be created in 2 different ways:

### Imperatively (in a way that is extremely important or urgent)

  

### Declaratively ( in a way that involves making an announcement)

  

  

## Key objects

### The “Pod” Object

![[/image 5 7.png|image 5 7.png]]

Pod not just only run one or multiple Containers, it can also hold shared resources (mostly Volumes)

Pods of course are also part of the Cluster, therefore can communicate with other Pods or the outside world. But by default, a Pod has a Cluster internal IP Address, which can be used to send requests to that Pod, therefore to the Container running in that Pod. If there are multiple Containers running inside that Pod, these Containers will be able to communicate with each other by using [localhost](http://localhost) address

- Important things to understand about Pods:
    
    - Pods are ephemeral (they don’t last long, they don’t persist). If a Pod is replaced or removed by K8s, all the resources in the Pod (data stored and created by Container) is lost (but we can use volumes).
    - We can create Pods on our own and start them with on some Worker Nodes on our Cluster, but that’s typically not what we would do, instead we want K8s to manage these Pods for us. We want K8s to automatically create, remove, replace,… Because that’s why we use K8s
    
    → That’s why we typically don’t just create Pod objects and send them to the Cluster, but creating Controller objects (specifically “Deployment” object) which then create Pods for us.
    

### The “Deployment” Object

One of the key object we work with, when working with K8s. Because we don’t typically create Pod objects on our own and manually move it to some Worker Node, but instead we create a Deployment object, which should then give instructions about the number of Pods and Containers it should create and manage for us

![[/image 6 7.png|image 6 7.png]]

  

The “Deployment” object is able control 1 or multiple Pods.

Core ideas:

- We set a desired state we wanted, for example, we want 2 Pods with this Container up and running, and then K8s will do everything it needs to do to reach the target.
- We can pause or delete Deployments, and also roll them back. So if we messed something up, and our Application fails, we don’t have to rush to change our code and fix the error, and replace the failing part with a new one, instead we can roll back the failing deployment, and go back to the previous deployment which worked. Then we can fix the bug or do anything and update.
- Deployments can be scaled: We can tell K8s that we now want to have more Pods or less Pods, and there’s a feature called Auto scalling, where we can set certain metrics (incoming traffic, cpu utilization,…), and when these metrics exceeded, K8s will automatically create more Pods, therefore more running Container instances to handle the requests. Once the traffic go down and less, uneccessary Pods will be removed again

We can create multiple Deployments to have multiple Pods being managed by K8s, and therefore we typically don’t create Pods, we don’t typically manage them, instead we let K8s do that by using and create Deploymenet objects and send them to the K8s Cluster

  

### The “Service” Object

To reach a Pod and the Container running in the Pod, we need a “Service”

![[/image 7 7.png|image 7 7.png]]

Pod already has an internal IP Address, but there are 2 problems with this internal IP Address:

- We can’t use that to access the Pod from outside the Cluster
- It’ll change whenever a Pod is really (really often)

→IP Address is not a really great tool for communication to the Pod, even if it would be clustered internal (there we can use it because it’s reachable, but it will be changed)

  

Services group Pods together and give them a shared IP Address, and this Address won’t change → We can use a “Service” to move multiple Pods into that “Service” and then make them reachable on that “Service” ‘s unchangable IP Address  
  
  
  

# First Deployment

Even with K8s, we still need to use DOcker to create the Image based on the Application, since K8s is all about Containers → To run Containers, it needs an Image. The only difference is, we don’t need to run Containers on our own anymore

First we will build the image

```Docker
docker build -t kub-fd .
```

After building the Image, we need to send it to the K8s Cluster, but not as an Image file, but as part of the Pod or as part of a Deployment object, which will then create the Pod and therefore the Container running in the Pod, and it will also manage it for us.

To send the instruction to the Cluster, we have to ensure the Cluster is up and running

```Docker
minukube status
```

  
If it’s stopped, we can restart it  

```Docker

minikube start --driver=docker (any driver’s name)
```

  

Now we can send the instructions to create a **Deployment** to the Cluster

```Docker
kubectl create deployment <deployment-name> --image=songphuc191004/kub-fd
```

  

By using this `create` command, we are using the Imperative approach of creating Objects

`--image` option is passing the image, specify which image should be used for the Container of the Pod created by this Deployment

Now if we pass the image we just built (kub-fd), it will not work. With the pulling image error. Because the Cluster is on a VM, which is still on our machine, but acts as a separated machine, so it couldn’t find the local image we built. So instead specify a local image, we should specify an image which is on the registry (docker hub)

  

To get deployments:

```Docker
kubectl get deployments
```

  

To see Pods created:

```Docker
kubectl get pods
```

![[/image 8 7.png|image 8 7.png]]

  

Now we can see the Dashboard:

```Docker
minikube dashboard
```

![[/image 9 7.png|image 9 7.png]]

  

### Behind the scenes

![[/image 10 6.png|image 10 6.png]]

### Exposing Deployment with a Service

We can create a Service by using

```Docker
kubectl create service
```

But that’s not what we are doing here

Instead there is an even more convienient command for exposing the Pod created by a Deployment

```Docker
kubectl expose deployment
```

This command exposes a Pod created by a Deployment by creating a Service

```Docker
kubectl expose deployment <name-of-deployment> --type=LoadBalancer--port=8080
```

We gotta provide 2 important options

- port
- type: type of service (or type of exposing), and there are different built-in types. **ClusterIP** is the default type, which means it will only be reachable from inside the Cluster (we still get the IP Address that doesn’t change). But we don’t want that, instead we will use **NodePort**, which means this Deployment should be expose with help of the IP Address of the Worker Node on which it’s running. This will be accessible from the outside.
    
    But even better than that, there is a type **LoadBalancer.** This utilize the Load Balancer which has to exist in the infrastructure on which our Cluster runs, and then this Load Balancer will generate a unique Address for the Service, and not just that, it will also evenly distribute incoming traffic accross all Pods which are part of this Service. But the LoadBalancer type will only available if our Cluster and the Infrastructure on which the Cluster runs supports it (AWS,..), and minikube does support.
    

Now, after executing the command, we can see the services by using

```Docker
kubectl get services
```

![[/image 11 5.png|image 11 5.png]]

We will see 2 services:

- The bottom one (kubernetes): this has the type **ClusterIP** and it’s a default service which was created automatically, not by us
- first-deployment: type is LoadBalancer, Cluster Internal IP (which won’t help us). If this is a Cloud Provider, we would see an external IP here, but for minikube, this will always stay in ‘pending’ status, because minikube is still a VM running on our local machine, so it doesn’t have too many IP available

minikube has a command, which will give us access to this service by basically mapping a special port to an IP, which we can reach from our local machine, and which identifies this VM running on our local machine

```Docker
 minikube service <service-name>
```

If we are on a Cloud Provider, this is not needed since we already got the Internal IP, and this wouldn’t even work, because it’s minikube specific.

![[/image 12 5.png|image 12 5.png]]

![[/image 13 4.png|image 13 4.png]]

  

This programs is designed to be crashed when we get to the /error page, but K8s will try to restart the Pod for us

![[/image 14 3.png|image 14 3.png]]

That’s the behavior we get because we create the Deployment here. Because of that, this Pod and the Container in the Pod is being monitored, and if it fails, it will be restarted

![[/image 15 2.png|image 15 2.png]]

  

### Scaling

Kinda related to restarting, there’s another useful command. Let’s say we don’t have auto scalling in place, so K8s cannot create more or less Pods, but we know we will have more traffic incoming, so we want to have 3 Containers up and running instead of 1

```Docker
kubectl scale deployment/<deployment-name> --replicas=3 
```

replicas option: simply an instance of a Pod/Container. 3 replicas means that the same Pod/Container is running 3 times

![[/image 16 2.png|image 16 2.png]]

Now because we have a Load Balancer, traffic will also be distributed evenly accross these different Pods and the Containers running in there. Now let’s test it out with the /error path, which will crash a Container.

![[/image 17 2.png|image 17 2.png]]

As we can see, when we get to the /error path, one Container will be crashed, as before , we will have to wait for the Container to be restarted. But now we can still access the main page without a problem thanks to the replicas by redirect us to another Pod. And also, we might get different crashed Pods whenever we get to /error, this means the traffic is distributed by the Load Balancer

We can always scale it down by setting --replicas=1

### Updating Deployment

At this section, we will get to know how to change our code, and update our Deployment, and then even roll back to another Deployment, if we wanted to

If we change our source code, we still need to rebuild our image

After rebuilding our image, we will update our image into our Deployment

```Docker
kubectl set image deployment/<deployment-name> <current-image-name>=<new-image-name>
```

We will have to provide which current Container image to replace with which new image (for better flexibility, since we can change any image to any image)

![[/image 18 2.png|image 18 2.png]]

```Docker
kubectl set image deployment/first-deployment kub-fd=songphuc191004/kub-fd
```

This will pull the image from Docker Hub, so we need to push the new image first

But it won’t work (no differences), because new image will only be downloaded if they have a ==**different tag**== (image tag)

Now let’s tag it and rerun the command

```Docker
docker build -t songphuc191004/kub-fd:2 .
docker push songphuc191004/kub-fd:2
```

```Docker
kubectl set image deployment/first-deployment kub-fd=songphuc191004/kub-fd
```

![[/image 19 2.png|image 19 2.png]]

Now, we have to roll out (_**to make a new product, service)**_ for it to actually update

```Docker
kubectl rollout status deployment/<deployment-name>
```

![[/image 20 2.png|image 20 2.png]]

Now run

```Docker
minikube service first-deployment
```

![[/image 21 2.png|image 21 2.png]]

  

### Deployments Rollbacks & History

Now let’s try something which will fail: set image of a deployment to a non-exsiting image

```Docker
kubectl set image deployment/first-deployment current-image=new-image:non-existing tag
```

![[/image 22 2.png|image 22 2.png]]

After that we will rollout so it will actually update

![[/image 23 2.png|image 23 2.png]]

This tell us what is going on for this deployment (here, it said “waiting…”)

We can see some problem, as it waiting for the old replica is pending termination, and it will not be termintated, since the new Pod does not start successfully, with the ImagePull error

![[/image 24 2.png|image 24 2.png]]

→ Thanks to the rolling update strategy K8s uses, it doesn’t shutdown the old part before the new part is up and running.

Here, our Update will never be finished because the new Pod never start

→ We want to roll back this update (we don’t want K8s to continue with it)

  

First, let’s see the Pod which is stuck

![[/image 25 2.png|image 25 2.png]]

Now, to roll back this update, we run:

```Docker
kubectl rollout undo deployment/first-deployment
```

This will undo the latest deployment

![[/image 26 2.png|image 26 2.png]]

As we can see, the failed Pod is gone

![[/image 27 2.png|image 27 2.png]]

And now, when we rollout (previous update of the latest update), it succeeded

  

Now if we want to see and get to older deployments, not just the previous one, we can run:

```Docker
kubectl rollout history deployment/first-deployment
```

![[/image 28 2.png|image 28 2.png]]

We can specify the revision option to see a specific deployment

```Docker
kubectl rollout history deployment/first-deployment --revision=<number>
```

![[/image 29 2.png|image 29 2.png]]

  

To go back to the a specific revision, we will add an option the the rollout undo:

```Docker
kubectl rollout undo deployment/first-deployment --to-revision=<number>
```

  

Latest one:

![[/image 30 2.png|image 30 2.png]]

  

After rollout:

![[/image 31 2.png|image 31 2.png]]

# The imperative vs The Declarative Approach

It’s like `docker run`comand, the way we used in the previous sections are repetive → The same problem of `docker run`goes for this approach: If we have a project with multiple Containers, we will have to run multiple `docker run` commands with multiple options in each `docker run`  
→ docker-compose is here to solve  

→ K8s has a thing called Resource Definition, which is like docker-compose

We can write down configuration options in yaml file, and do that for ==**different kind of objects**== K8s understands (deployment, service,…)

→ Alternative to the Imperative approach, called ==**Declaritve.**==

![[/image 32 2.png|image 32 2.png]]

  

## Creating a Deployment Configuration File in Declarative approach

First, let’s create a yaml file

![[/image 33 2.png|image 33 2.png]]

First of all, no matter which kind of resource we are configuring (deployment, service,…), we need to specify the ==**apiVersion**== by adding it

```YAML
apiVersion: apps/v1
```

We can get the latest apiVersion on K8s website

![[/image 34 2.png|image 34 2.png]]

  

Next, we have to let K8s know what we want to create by adding kind:

```YAML
apiVersion: apps/v1
kind: Deployment
```

With this, we tell K8s that we want to create a Deployment object

  

Then, we typically need some metada (crucial things), like the name of the object we are creating

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
	name: second-deployment
```

We can add more metadata (look up on K8s Docs)

  

After that, we have to add ‘spec’ (specification of this object).

Here, we will define how this Deployment be configured

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
	name: second-deployment
spec:
	replicas: 1
	template: 
		metadata:
			labels: 
				app: second-app
		spec:
			containers: 
				- name: cursus.webapi
					image: songphuc191004/webapi
				- name: cursus.db
					image: postgres
				- name: cursus.cache
					image: redis
```

- **replicas** is for setting number of Pod instances, default is 1. Here we set it to 3, so it will start with 3 running Pods, 3 equal Pods containing the same Container right from the start. But for now, we will set to 1.
- **template (important)**: Here we will define the Pods that should be created as part of this Deployment. In the **Imperative Approach,** we use the --image= to define which image should be used for the Container used in the Pods attached to this Deployment  
    We create it below template because a Pod is a new Object → We need new **metadata.**
    
    We can add a name if we wanted, but in this case, we will add a label, using **labels** key
    
    Below **labels,** we can give it any labels we want (key name and value’s value is up to us).
    
    Notice that we didn’t use `**kind**` to specify it as Object, because the template of a Deployment always describe a Pod, so we don’t need it here (We can’t even write `kind` because the `template`only takes `metadata`and `spec` )
    
    - spec: here we will define how this individual Pod should be configured
        - **containers(important):** allows us to define Containers, or the single Container which should be part of this Pod
            
            We can define multiple containers, give them name. Images must be on registry (can contains tag)
            
              
            
    
    Before applying the configuration file, we are missing an important element: ==**selector**==
    
    We will add it in the Deployment’s spec (not the Pod’s spec)
    
    ```YAML
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    	name: second-deployment
    spec:
    	replicas: 1
    	selector: 
    		matchLabels: 
    			app: second-app
    			tier: backend
    	template: 
    		metadata:
    			labels: 
    				app: second-app
    				tier: backend
    		spec:
    			containers: 
    				- name: cursus.webapi
    					image: songphuc191004/webapi
    				- name: cursus.db
    					image: postgres
    				- name: cursus.cache
    					image: redis
    ```
    
    Below `matchLables` key, we have to specify which Pods do this Deployment should control by writing key value pairs of the Pods’ labels.
    
    Explain: We got a `template` for the Deployment here, so it’s obvious that which Pods are created by this Deployment (The Pod defined in the `template`). But Deployments are a dynamic objects in K8s. For example: if we scale up a number of Pods, after a Deployment has been created, these new Pods which are created will still automatically being managed by the already existing Deployment → Deployment continuously watches all the Pods which are out there, and see if there any Pods it should control → It decide which Pods to be controlled by using the `selector` key (we will see `selector` on pretty much all resources K8s works with, like Services)
    
    There are different kind of `selectors` , on Deployment, we can use `matchLabels` and `matchExpressions` (the latter is complex so we will not have a look at that rn). Here we specify ==app==: ==second-app== ==&&== ==tier==: ==backend==. So only Pods with both conditions met are being controlled by this Deployment → It tells the Deployment which Pods belong itself.
    

  

→ Now, let’s apply this configuration using the CLI

```YAML
kubectl apply -f <path-to-file>
kubectl apply -f deployment.yaml
```

-f stands for file. We can have multiple -f if we want to apply multiple files at once

![[/image 35 2.png|image 35 2.png]]

  

Now we will also define the Service resource, since we need it to access the Deployment

Because it’s still a K8s config file, we still need to define the apiVersion, but it just v1 instead of apps/v1 like deployment config (see on [docs](https://kubernetes.io/docs/reference/kubernetes-api/service-resources/service-v1/))

![[/image 36 2.png|image 36 2.png]]

  

```YAML
apiVersion: v1
kind: Service
metadata:
	name: backend
spec: 
	selector: 
		app: second-app
```

- selector: identifies which other resources should be controlled or connected to this resource. In this case, it defines which Pods should be part of this Service.
    
    Service’s **selector** is different from Deployment’s **selector,** because the Deployment’s **selector** is release recently, so it has different syntax (newer than Service’s)
    
    We don’t need **matchLabels** like in Deployment’s **selector,** because we can only match labels here.
    
    Here we only select ==app==:second-app instead of both app and tier:backend. We can actually do that, but we do this to show the case when we have an another Deployment which create Pods with label app:second-app which doesn’t have tier:backend. Since we want to group those Pods together, even though they are in different Deployments → only select app:second-app
    

Now we will configure how it will expose (port,…)

```YAML
apiVersion: v1
kind: Service
metadata:
	name: backend
spec: 
	selector: 
		app: second-app
	ports:
		- protocol: 'TCP'
			port: 80 (outside world port)
			targetPort: 8080 (container port)
		- protocol: 'TCP'
			port: 443
			targetPort: 1234
```

We can define multiple exposed ports (thats why its plurals)

Then we will have a list of ports that can be exposed

There, we will typically define the **protocol** being used, by default protocol is **TCP**

Then on the same list item, we define the ==**port**== we want to expose on, then the ==**targetPort**== inside of the ==**Container**== (our node app listen on port 8080)

  

Now for the **type** option

```YAML
apiVersion: v1
kind: Service
metadata:
	name: backend
spec: 
	selector: 
		app: second-app
	ports:
		- protocol: 'TCP'
			port: 80 (outside world port)
			targetPort: 8080 (container port)
	type: LoadBalancer
```

By default, type is ClusterIP, which is an internally exposed IP → Only accessible from inside the Cluster.

NodePort: to expose on the IP and Port of the Worker Node

LoadBalancer (most commonly used): outside the world access. This utilize the Load Balancer of our Cluster Infrastructure (minikube load balancer in this case). We will get the outside world reachable address, allows us to reach this Service, therefore reach the Pods, and incoming traffic is distributed equally

  
Apply it using:  

```Plain
kubectl apply -f service.yaml
```

![[/image 37 2.png|image 37 2.png]]

  

# Making changes

How do we make changes to our Deployment or Service?

→ We can simply change in the yaml files, and then reapply the files → neccessary changes will be made in our Cluster

For example: we can change replicas number, save file, and then reapply the file. We can also change the image by changing image or image tag, then save and reapply it

  

### Deletion

We can also delete by using the imperative approach, which is type out the command

```Plain
kubectl delete deployment <deployment-name
```

Or we can use:

```Plain
kubectl delete -f <file-name>
```

This will delete all the resources created by this file, not deleting this file. We can also delete multiple resources

![[/image 38 2.png|image 38 2.png]]

# Multiple vs Single Config files

We can merge multiple files into one. Having multiple files can embrace clearness, but we can also merge related file together, using three dashes to separate (---)

master-deployment.yaml

```YAML
apiVersion: v1
kind: Service
metadata:
    name: backend
spec:
    selector:
        app: second-app
    ports:
        - protocol: 'TCP'
          port: 80
          targetPort: 8080
    type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: second-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: second-app
      tier: backend
  template:
    metadata:
      labels:
        app: second-app
        tier: backend
    spec:
      containers: 
        - name: second-node
          image: songphuc191004/kub-fd:2 
```

If we put Deployment and Service into a single file, it’s a good practice to put Service first, so that the Service resource are created first (resources are created from top-bottom), because since it has the selector, it will then dynamically add any Pods with are created there after. It could be in other order, but not a good practice.

![[/image 39 2.png|image 39 2.png]]

  

# More on Labels and Selectors

When we have an object that ==**support modern syntax**== (like Deployment), we have an alternative to match labels, which is matchExpression

matchExpressions is a more powerful way of selecting things, where we simply have more configuration options, where we can do more

Unlike matchLabels, we don’t have multiple nested key value pairs, instead we have a list of expression, which all has to be satisfied in order to have a matching object. It defines in curly braces {}

```YAML
replicas: 1
selector:
	matchExpressions:
		- {key: app, operator: In, values: [second-app, first-app]}
...
```

- key: which key we are targeting
- operator: the condition: ‘In’ will take all the values of app which is in the values []. While NotIn will target to which app key’s value not in values []

  

We can also use selectors in ==**Delete**== command. We’ve learnt deleting targeting file (-f), targeting a specific object (`kubectl delete deployment`, `kubectl delete pod` ,…). We can also delete by selectors:

```YAML
kubectl delete deployments,services -l user-defined-key=user-defined-value
```

-l: labels

We can specify which resources will be delete to prevent mistakes

  

We will have to add labels key in metadata before using this

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
		name: something
		labels:
			user-defined-key: user-defined-value
```

```YAML
apiVersion: v1
kind: Service
metadata:
		name: something
		labels:
			user-defined-key: user-defined-value
```

  

# Liveness Probes

How K8s check wherever our Pods and Containers in our Pods are healthy or not. Up to this point, it always works by default, but we can of course can configure how it should do that.

We can change how the Container is checked by the Pod to see whether it’s up and running. We will change it in the Container spec, by adding **livenessProbe** key

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: second-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: second-app
      tier: backend
  template:
    metadata:
      labels:
        app: second-app
        tier: backend
    spec:
      containers: 
        - name: second-node
          image: songphuc191004/kub-fd:2
          livenessProbe:
	          httpGet:  
		          path: /
		          port: 8080 
		         periodSeconds: 3
		         initialDelaySeconds: 5
```

This define how K8s should verify whether this Container is up and running

# Close Look at Configuration Options

See more on docs

Previously, we can only pull an image by using a tag different from the old tag, but turns out, we can change that policy by putting :latest (see on docs for more)