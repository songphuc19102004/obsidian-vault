# General structure of the Project

![[/image 151.png|image 151.png]]

![[/image 1 12.png|image 1 12.png]]

Pod-internal communication: Auth API & Users API. Not reachable from outside the Cluster, only inside that Pod

Both Pods will be reachable from the outside  
Auth API can’t be reached and handle requests from the outside, only the Users API will.  

After demo Pod-internal Communication, separate AuthAPI and Users API into 2 Pods

Task API will be able to talk to Auth API (Pod-to-Pod Communication)

### Creating Users Deployment

==kubernetes/users-deployment.yaml==

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: users-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: users
  template:
    metadata: 
      labels: 
        app: users
    spec:
      containers:
        - name: users-container
          image: songphuc191004/kub-network-user:1
```

Apply:

```YAML
kubectl apply -f kubernetes/users-deployment.yaml
```

  

### Creating Users Service

```YAML
apiVersion: v1
kind: Service
metadata:
  name: users-service
spec:
  selector:
    app: users
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8080
```

# Multiple Containers in one Pod

```JavaScript
const response = await axios.get(
    'http://auth/token/' + hashedPassword + '/' + password
  );
```

  

```JavaScript
const hashedPW = await axios.get('http://auth/hashed-password/' + password);
```

==docker-compose.yaml==

```YAML
version: "3"
services:
  auth:
    build: ./auth-api
  users:
    build: ./users-api
    ports: 
      - "8080:8080"
  tasks:
    build: ./tasks-api
    ports: 
      - "8000:8000"
    environment:
      TASKS_FOLDER: tasks
    
```

  

As we can see, they are requesting the the ==**auth**== domain, this is knowledge in the Docker section (Container to Container communication). The service name (==**auth**==) can be used internally in that network.

That works for Docker Compose, but it ==**will not work for K8s.**== To stay flexible, we will replace it with Environment Variables, so the address can be changed depending on the environment on which we are running

  

Fix:

```JavaScript
const hashedPW = await axios.get(`http://${process.env.AUTH_ADDRESS}/hashed-password/` + password);
```

```JavaScript
const response = await axios.get(
    `http://${process.env.AUTH_ADDRESS}/token/` + hashedPassword + '/' + password
  );
```

```YAML
version: "3"
services:
  auth:
    build: ./auth-api
  users:
    build: ./users-api
    environment:
      - AUTH_ADDRESS: auth
    ports: 
      - "8080:8080"
  tasks:
    build: ./tasks-api
    ports: 
      - "8000:8000"
    environment:
      TASKS_FOLDER: tasks
```

  

Since we want Auth API and Users API in the same Pod, instead we create a new Deployment file for Auth API, we will add it into Users’ file

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: users-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: users
  template:
    metadata: 
      labels: 
        app: users
    spec:
      containers:
        - name: users-container
          image: songphuc191004/kub-network-user:1
        - name: auth-container
	        image: songphuc191004/kub-network-auth:1
```

Since we want Auth API to only works with Users API and not expose to the outside world, we don’t need to write it in the Service file.

# Pod-Internal Communication

Pod-Internal Commnucation: 2 ==**Containers**== (Or more) running in the ==**same Pod**==, only then K8s allows us to send the requests to the ==**localhost**== address and then using the ==**port**==, which is exposed by the other Container. → We use ==**localhost**== address inside of a Pod.

We will add the env var in the users-deployment into a Container:

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: users-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: users
  template:
    metadata: 
      labels: 
        app: users
    spec:
      containers:
        - name: users-container
          image: songphuc191004/kub-network-users:2
          env:
            - name: AUTH_ADDRESS
              value: localhost
        - name: auth-container
          image: songphuc191004/kub-network-auth:1
```

→ When we use Docker Compose, it provides AUTH_ADDRESS with the value: auth (since Docker Compose will use service name as Domain), and we will provide AUTH_ADDRESS with value:localhost in K8s world.

Test:

```JavaScript
res.status(201).json({ message: `User created!, env: ${process.env.AUTH_ADDRESS} ` });
```

![[/image 2 11.png|image 2 11.png]]

# Creating Multiple Deployments

### Separate Deployments

==auth-deployment.yaml==

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: auth-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: auth
  template:
    metadata: 
      labels: 
        app: auth
    spec:
      containers:
        - name: auth-container
          image: songphuc191004/kub-network-auth:1
```

  

==users-deployment.yaml==

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: users-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: users
  template:
    metadata: 
      labels: 
        app: users
    spec:
      containers:
        - name: users-container
          image: songphuc191004/kub-network-users:2
          env:
            - name: AUTH_ADDRESS
              value: localhost
        - name: auth-container
          image: songphuc191004/kub-network-auth:1
```

Since we move Auth API to a new Pod, it’s no longer have a Service → It’s not reachable and its’ IP will change all the time (restart, scale,…) ⇒ Create auth Service

```YAML
apiVersion: v1
kind: Service
metadata:
  name: auth-service
spec:
  selector:
    app: auth
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 81
      targetPort: 80
```

Here, we change to type ClusterIP. ClusterIP still do some Load Balancing, but it will not exposed to the outside world.

==AuthAPI Dockerfile==

```Docker
FROM node:14-alpine

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 80

CMD [ "node", "auth-app.js" ]
```

Dockerfile exposes port 80, so targetPort is 80

> Now, how can we reach the Auth API Pod? What should be the AUTH_ADDRESS, since it’s not in the same Pod anymore?

# Pod-to-Pod Communication

To communicate with Auth API, we need to get its Service IP Address, since the IP Address of a Service is stable

![[/image 3 9.png|image 3 9.png]]

Now, we will give it to the AUTH_ADDRESS of users-container and reapply it

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: users-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: users
  \#storageclass
  template:
    metadata: 
      labels: 
        app: users
    spec:
      containers:
        - name: users-container
          image: songphuc191004/kub-network-users:2
          env:
            - name: AUTH_ADDRESS
              value: '10.102.205.70:81'
        - name: auth-container
          image: songphuc191004/kub-network-auth:1
```

Here we put extra :81 because we set the port to 81 in the auth-service.yaml. If we set port to 80, we don’t need to put it there, since the default port of http is 80

But using the IP Address is a bit annoying, there is a more convenient way:

K8s automatically create an environment variable for Service. For example, we auth-service. To get the IP: AUTH_SERVICE_SERVICE_HOST. By default, it will return port 80, but we set port 81 in the Service, so we should change it back to 80 until we research it.

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: users-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: users
  template:
    metadata: 
      labels: 
        app: users
    spec:
      containers:
        - name: users-container
          image: songphuc191004/kub-network-users:3
          env:
            - name: AUTH_ADDRESS
              value: '10.96.69.72'
```

```YAML
apiVersion: v1
kind: Service
metadata:
  name: auth-service
spec:
  selector:
    app: auth
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

```JavaScript
//login
const response = await axios.get(
    `http://${process.env.AUTH_SERVICE_SERVICE_HOST}/token/` + hashedPassword + '/' + password
  );
```

```JavaScript
//signup
const hashedPW = await axios.get(http://${process.env.AUTH_ADDRESS}/hashed-password/ + password);
```

![[/image 4 9.png|image 4 9.png]]

⇒ Get the IP Process is now dynamic

# Using DNS for Pod-to-Pod Communication

There is a more convenient approach than using automatically created Env Var.

K8s’s Cluster by default, when using a modern version of K8s, it comes with a built-in service called Core DNS, which helps creating Domain Name (Cluster Internal Domain Name)

In order to use that, we need to get the target Service name, here is Auth API, so auth-service, combine with a dot and the name of the namespace the service is part of. Namespace is a concept that allows us to assign Services, Deployments,… to different namespaces within a Cluster → A way of differentiating and grouping resources.

```JavaScript
kubectl get namespaces
```

![[/image 5 9.png|image 5 9.png]]

By default, Services and Deployments are assigned to ‘default’ namespace if we don’t tell K8s to do it.

⇒ DNS: <service-name>.<namespace-name>

⇒ auth-service.default

```YAML
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: users-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: users
  \#storageclass
  template:
    metadata: 
      labels: 
        app: users
    spec:
      containers:
        - name: users-container
          image: songphuc191004/kub-network-users:3
          env:
            - name: AUTH_ADDRESS
              # value: '10.96.69.72'
              value: 'auth-service.default'
```

Then we apply the deployment yaml file

![[/image 6 9.png|image 6 9.png]]

# Best Approach

To determine the best approach, it depends on wherever we want to have 2 Containers in the same Pod or not. In most cases, we ==**don’t want to have multiple Containers per Pod.**== We should really do that if 2 Containers are tightly coupled with each other. If some Container also interact with another Container in another Pod, then we will want that Container to be a in a separated Pod.

About communicating, we have 3 approaches:

- Using the Service’s stable IP Address
- Using the automatically generated Environment Variable
- Using automatically generated Domain Name, which are generated for every Service launches in the Cluster

→ The most common way of connecting Pods is ==**Domain Name**==: Easy to remember, easy to add