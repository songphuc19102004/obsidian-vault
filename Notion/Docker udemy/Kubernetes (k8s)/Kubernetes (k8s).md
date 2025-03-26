```Docker
Kubernetes is more like a framework, a collect of concepts,
a standard, which will help us with container orchestration, and
large scale deployment independent of the cloud provider we use.
```

Kubernetes is an open-source ==**system**== for automating deployment, scaling, and management of containerized applications

  

# Problem

When we think about deploying Containers, we might have a problem (manually deploy containers, by manually managing servers for them)

Lets think about manually deploying containers by manually managing servers for them, like Create our own EC2 instance, download docker on it,…

![[/image 148.png|image 148.png]]

We have to ==**manually**== monitor whenver something crash, and we have to ==**manually**== restart the container → Not good for serious or big app, because we ==**can’t**== monitor it all the time.

If we have ==**too many traffic**== incoming, we might want to ==**increase**== the number of container, and we also might want to ==**remove**== containers if less traffic incoming.

In the deploy section, we only just have ==**one**== running instance of a container, but in reality, that might not be enough. If we only have one instance, there is nothing to be distributed.

In reality, we might need multiple container instances based on ==**same image**==, so the work can be splitted, so we ensure we have enough containers available to handle high traffic load

# Why Kubernetes

Service like ECS (AWS) can help us with the above problems

![[/image 1 9.png|image 1 9.png]]

It can:

Restart the containers

Autoscaling: dynamically increase/decrease number of container instances which are up and running to help us handle incoming traffic spike for example

Load Balancer: It ensures that if we got incoming traffic, that traffic is splitted evenly accross all running instances, in case we have more than 1 running instances.

→ ECS helps us with the problems we stated, but it has one downside: We are locked in that service (AWS). Which means, if we want to switch to another service (Azure, Google,…), like the config file we wrote for AWS ECS won’t work there, because it’s AWS specific.

![[/image 2 8.png|image 2 8.png]]

  

![[/image 3 6.png|image 3 6.png]]

→ That’s where ==**Kubernetes**== can help us

  

# What Kubernetes exactly is?

![[/image 4 6.png|image 4 6.png]]

  

![[/image 5 6.png|image 5 6.png]]

Docker Compose helps us manage multi-container project easily on our local machine.

K8s is the same for multi-machine setups. Because when we deploy our application, we do that by running the application accross multiple computers, multiple machines, not just one machine. K8s makes deploying our Containers, monitoring, restarting automatically accross multiple machine very easy.

  

It allows us to:

![[/image 6 6.png|image 6 6.png]]

The config file will work with any cloud provider, as long as it support Kubernetes, or even if it doesn’t support, we can manually install Kubernetes softwares in any machine we own, and then that software will be able to utilize the config file

We can also merge cerntain cloud provider specific option into this config file

![[/image 7 6.png|image 7 6.png]]

→ The idea of Kubernetes is having the ==**standardized way of describing deployments**==

  

## What Kubernetes ==IS== and ==IS NOT==

![[/image 8 6.png|image 8 6.png]]

1. It’s not a cloud service provider
    
    → It’s not an alternative for AWS or Azure, it’s an open-source project, a collection of concepts, a collection of softwares (we could say), which together can be used with any Cloud Provider
    
      
    
2. It’s not a service by a Cloud Service Provider
    
    → While AWS indeed which has a service which helps us with using K8s, K8s itself is an open-source project, can be used with any cloud provider, machine.
    
      
    
3. It’s not just a software which we run on some machine
    
    → It’s a collection of concepts and tools, which can help us with deployment on any provider of our choice
    

  

1. It’s not an alternative for Docker
    
    → It works together with Docker Containers to deploy Containers anywhere
    

  

1. It’s not a paid service
    
    → Because it’s a free open-source project. But we might need to pay if we use a K8s specific cloud provider service, or if we use K8s to deploy to some cloud provider, we would of course pay the cloud provider
    

  

# K8s Architecture & Core concepts

![[/image 9 6.png|image 9 6.png]]

  

- It’s all start with a Container, which we want to deploy, which in K8s world is actually managed by a Pod. We can think of a Pod as a smallest possible unit in the K8s world, which we can defined in some configuration file, for K8s to create, and a Pod simply hold a Container. A Pod can also hold multiple Containers which might need to work together, but the smallest possible unit is simply one pod which responsible for executing the Container.
- The Pod with the Container inside of it then itself runs on a Worker Node. Worker Node is a thing in K8s which runs your Containers in the end. Think of Worker Node as our machines, our virtual instances (For example: EC2 instance of AWS could be a Worker Node).
- Worker Node is simply a machine, a computer somewhere with a certain amount of CPU and Memory, and on that machine, we can run our Pod, and we can have more than one Pod running on the same Worker Node.
- Beside Pod, on Worker Node, K8s also need a Proxy, which is just an another tool K8s sets up for us on a Worker Node, to control the network traffic of the Pods on that Worker Node → To basically control wherever these Pods can reach the Internet, and how these Pods, and Containers in the Pods, can be reached from the outside world. For example, if we are running a Web Application in a Container inside a Pod, the Proxy need to be configured such that traffic from the outside world, from the users, can be able to reach these Containers
- We might need more than 1 Worker Node on bigger application, because we might need more compute powers.
- Now all these Worker Nodes, Pods, Containers, need to be controlled. Some one need to create a start these Containers and Pods, or some one need to replace or shut them down if they failed or not needed anymore
    
    → That’s done by Master Node, specifically by the Control Plane.
    
- Master Node is basically the Control Center, which then interacts with the Worker Nodes to control them. → When we work woth K8s, we typically don’t directly interact with our Worker Nodes or Pods (we can do that), instead we let K8s and the Control Plane do the heavy lifting, and we as the Dev, just define the desired end state K8s should take into account.
- The Master Node is simply an another Server, an another remote machine, which has the Control Plane running on it, which then is responsible for interacting with the Worker Nodes and the Pods running on them. Thereotically, we can have 1 machine which acts as both the Master and Worker Node, but for bigger deployment, of course we will have a Master Node which itself might be splitted accross multiple machines, to ensure high availablity
- The Control Plane on the Master Node is actually a collection of different tools, services, which are running on the Master Node

→ All together, this forms a ==**Cluster**==. This forms a Cluster of the Master and Worker Nodes → One network in which all the different parts are connected → Master Node is able to send instructions to a Cloud Provider API to tell that Cloud Provider to create that its’ Cloud Provider specific resources to replicate the desired big picture, the end state on that Cloud Provider

For example: We interact with AWS such that it creates all the required EC2 instances, a Load Balancer, and everything else which is required to have this Network, and then have K8s and some K8s tools running on the Master Node EC2 instance, which then control the other EC2 instances which belongs to this Network, to then run Containers in these Pods on them

  

## Our work and K8s work

![[/image 10 5.png|image 10 5.png]]

  

# A closer look at the Worker Nodes

![[/image 11 4.png|image 11 4.png]]

To be precised: What’s happening on the Worker Node (e.g. creating a Pod) is managed by the Master Node

- Worker Node is not task-specific, it can contains multiple Pods with the same Content (Copy of a Pod), or even multiple Pod with different Content, since Worker Node is just a virtual machine
- On the Worker Node, we don’t just have these Pods, but some additional softwares, for example:
    - Docker: of course required by the Pods to create and run Containers
    - kubelet: Communication device between the Worker Node and the Master Node → Basically a software, service running on that the Worker Node Machine which does the actual Communication with the Master Node, so the Master Node is able to control the Pods on this Worker Node
    - kube-proxy: a service responsible for handling incoming and outgoing traffic to ensure that everything is working as desired, and only allow traffic is able to reach the Pods, and only allows traffic is able to leave the Worker Node

  

# Closer look at the Master Node

![[/image 12 4.png|image 12 4.png]]

- Inside of the Master Node, the most important thing, service, software is the API Server, which is the counterpart (đối tác) for the communication between Worker and Master Node.
- Another service which is installed and running on the Master Node is Scheduler, which responsible for watching our Pods and choosing Worker Nodes on which new Pods should be created on, in case a new Pods should be added if it got unhealthy and went down, or because of scaling, we need to create a new Pod. → This is responsible for telling the API Server what to tell the Worker Node
- Kube-Controller-Manager: watches and controls the Worker Nodes overall, and is responsible for ensuring we got the correct number of Pods up and running → Work closely together with Scheduler and API Server
- Cloud-Controller-Manager: doing the same thing but which is Cloud Provider specific, and knows how tell that Cloud Provider what to do → This service translate instructions to AWS or Azure

  

# Summary (Terms and Concepts)

![[/image 13 3.png|image 13 3.png]]