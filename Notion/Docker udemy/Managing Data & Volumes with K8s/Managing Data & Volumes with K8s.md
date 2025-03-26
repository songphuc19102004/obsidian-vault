# K8s and Volumes - More than Docker Volumes

## Understanding “State”

State is ==**data**== created and used by your application which must not be lost

![[/image 150.png|image 150.png]]

![[/image 1 11.png|image 1 11.png]]

For intermediate, temporary results, Pod specific Volumes might be enough. We migh not need a PV for that, but we can use it if we need it.

## K8s and Volumes

K8s can mount Volumes into Containers. We can add instructions to out Pods template when we set up a Deployment, that a Volume should be mounted into the Container which will be launched as part of the Pod. K8s has quite powerful Volume support

![[/image 2 10.png|image 2 10.png]]

→ Ideas are the same as Docker, but K8s Volumes are a bit more powerful

  

## K8s Volumes vs Docker Volumes

![[/image 3 8.png|image 3 8.png]]

K8s Volumes: We have different drivers and types → Give us great control of where the data is stored

Docker Volumes: We run it locally on 1 machine anyway → We don’t need much support for different storage system. While K8s running our app on Cluster with multpile Nodes, on different hosting environment (AWS, your own data center,…) → K8s Volumes need to be flexible regarding how data should be stored

  

To add volumes to Container, we add:

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: story-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: story
  template:
    metadata:
      labels:
        app: story
    spec:
      containers:
        - name: story-container
          image: songphuc191004/story-image:2
      volumes: 
	      - name: story-volume
		      emptyDir: {}
```

Put volumes as same level as containers

Here we can define all the volumes which should be part of this Pod, and all Containers in that Pod will then be able to use that Volume

We have to give it a name and also a **type**

Here we set volume type as emptyDir, and give it an empty object as value, which means we don’t have any specific configuration for it, we just want to use this type with all its’ default settings

emptyDir simply creates a new empty directory whenever the Pod starts, and it keeps this directory alive, and fill with data, as long as the Pod is alive. Containers then can write to this directory, and if Containers restart or of removed, the data survived, but if the Pod is removed, this directory is removed, also when a Pod is re-created, a new empty directory is created.

Now we have set up the volume, we also need to bind it into a Container, so we make it available inside of a Container → We do that in the Container configuration

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: story-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: story
  template:
    metadata:
      labels:
        app: story
    spec:
      containers:
        - name: story-container
          image: songphuc191004/story-image:2
          volumeMounts: 
	          - mountPath: /app/story
		          name: story-volume
      volumes: 
	      - name: story-volume
		      emptyDir: {}
```

**volumeMounts** define where, which volume should be mounted

==**/app**====/story: this is the Dockerfile defined directory==

![[/image 4 8.png|image 4 8.png]]

![[/image 5 8.png|image 5 8.png]]

emptyDir volume is a great basic Volume, but it has 1 downside: What if we have multiple replicas, what if we have 2 replicas? Which means we have 2 Pods. And Volumes are ==**attached**== to Pods. So if a Pod failed or crash, the traffic will be redirected to the another Pod, and that Pod’s volume doesn’t contain the failed Pod’s data

One way to work with this is that we will replace the **emptyDir,** ==**which creates an empty directory per Pod**==, to a different driver: ==**hostPath**  
**hostPath**  
==allows us to set a path on the host machine (in this case, Node), so the data from that path will be exposed to different Pods → Multiple Pods can now ==**share**== the same path on the host machine, instead of Pod specific path, which is only useful on the same Pod, which handles all request.

==**Note**==: If we just work with 1 replica, and we just developing and trying out things, emptyDir is a great simple volume and does the job

  

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: story-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: story
  template:
    metadata:
      labels:
        app: story
    spec:
      containers:
        - name: story-container
          image: songphuc191004/story-image:2
          volumeMounts:
            - mountPath: /app/story
              name: story-volume
      volumes:
        - name: story-volume
          hostPath: 
	          path: /data
	          type: DirectoryOrCreate
```

Instead of passing an empty object{}, we will define 2 keys. hostPath is a bit like ==**bind mounts**==

- path: the ==**path on the host machine**== where the data should be stored (not the path in the Container). We could bind a path which contains some data which we wanna share with Container. Unlike emptyDir, hostPath will not always create a new empty path. (we shouldn’t use any path that are reserved in Linux)
- type: ‘**DirectoryOrCreate**’ means that **/data** should be an **exsiting** directory, else **create** a new one. We could put type as ‘**Directory**’, but it will fail if /data doesn’t exist

→ Now, even when a Pod failed, the other Pod can still get the data

  

### Downsides:

Here, in our local machine (minikube), we only have 1 Node, but if we have a bigger Cluster, with multiple Worker Nodes, which we typically do, the hostPath would still be ==**Node specific**== → Multiple Pods, multiple replicas running on different Nodes, ==**would not have access to the same data**==, because only the Pods on the same Node have access to this data

### CSI Volume

CSI stands for ==**Container Storage Interface**==

It’s a very ==**flexible**==, special Volume type. It’s a relatively new volume type which was added by K8s team. And it’s added to make sure that they don’t have to add more and more built-in types for different cloud provider, and different use cases. They expose a clearly defined interface and then anyone can build driver solution that utilize this interface

For example: We want to use Amazon Elastic File System Storage for storing our data. Then we could make it work with NFS type because EFS service is a network file system, but that might take some extra work, and there is no AWS EFS built-in type. Even better than that, thanks to the ==**csi**== type, the AWS team built a csi integration for the AWS EFS service

→ Thanks to the ==**csi**== feature, it would be super easy to add EFS as a storage solution for our K8s Volume

⇒ ==**csi**== allows us to attach any storage solution out there, as long as it exists an integration for this csi type (like AWS EFS CSI Driver)

  

## From Volumes to Persistent Volumes

We have covered some Volumes on the above section. But these Volumes has 1 disadvantage

![[/image 6 8.png|image 6 8.png]]

If we scale our Pods, and we go from one Pod to 2 Pods, then, depending on the type of the Volume we ae using, the new Pod might not have access to the data written by the first Pod (emptyDir). When we use hostPath, this problem was kind of solved, but it’s important to keep in mind that this solution only works here with Minikube because it’s a ==**One-Node environment**==

→ We don’t want to lose data because the Pod scales up or something like that

⇒ Persistent Volumes (Pod- and Node-independent)

> But AWS or Azure also has storage service, which stores data on their Servers, so why do we need Persistent Volumes?

→ Persistent Volumes contempt is more than just independent storage. The key idea is that the Volume will be detached from the Pod, and that include a total detach from a Pod life cycle. As a Cluster Administrator, we will have full power over how this Volume is configured, we don’t need to configure it multiple times for different Pods, and in different Deployment yaml files, or anything like that. Instead we will be able to ==**define it once and use it in multiple Pods**== if we want to

  

> How do they differ from regular Volumes?

![[/image 7 8.png|image 7 8.png]]

If we have a Cluster with multiple Nodes and different Pods, we’ve learnt that Volumes would be inside of these Pods.

With persistent Volumes, the idea is that we have new resources, new entities, which are ==**detached from our Nodes and from our Pods.**== Instead, we can create ==**PV Claims,**== this will be ==**part of Pods**== (yes part of Pods, picture is just to showcase), and these Claims could reach out to the standalone Pod- and Node-independent entities (persistent Volumes), to request access to them (for example: Containers running in the Pod are able to write into this Volume). We can also have Claims to multiple different PV

![[/image 8 8.png|image 8 8.png]]

  

### Defining Persistent Volumes

==host-pv.yaml==

```YAML
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-pv
spec:
  capacity: 
    storage: 1Gi
    volumeMode: Filesystem
    accessModes:
      - ReadWriteOnce
  hostPath: 
    path: /data
    type: DirectoryOrCreate
```

capacity is how much data can the Volume holds. Make sure it doesn’t exceed the cloud service’s Volume.

volumeMode: we have 2 Volume modes which is ==**Filesystem**== and ==**Block**==

accessModes: Here we just generally define which access modes are allowed, not defining the real access mode of the Volume. We only define the Volume accessMode when we claim a Volume, then we have to define how we want this Volume for a given Pod.

We have 3 accessModes:

- ReadWrite==**Once**==: means this Volume can be mounted as a ReadWrite Volume by a ==**single Node**== → Multiple Pods but they all have to be on the same Node
- ReadOnly==**Many**==: means this Volume is ==**read-only**==, but can be claimed by ==**multiple Nodes**==. But for the ==**hostPath**== type, this mode is not an available option because ==**hostPath**== by definition is defined on ==**one Node**==
- ReadWrite==**Many**==: means this Volume can be read and write from ==**multiple Nodes**==

→ For ==**hostPath**==, only ReadWriteOnce is available since it’s one Node

⇒ We only need to define this Volume once, because it’s independent. But in order to use it, we need a ==**PV claim.**==

  

### Creating PV Claim

To use the PV, first we need to create the Claim, then we will use it on the Pods that we want it to use.

==host-pvc.yaml==

```YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-pvc
spec:
  volumeName: host-pv
  accessModes:
    - ReadWriteOnce
  resources: 
    requests:
      storage: 1Gi
```

- name: important because we need it in order to use it
- spec: here, we specify which PV we want to claim. Here we only have 1, but we can have ==**multiple PVs**==, which also could use different types
    - volumeName: name of the PV
        
        When it comes to claiming PVs, we cannot just claim by names, there are also ways to claim by resource. Keyword: Dynamic Volume Provisioning. (In this course, we stick to Static Provisioning)
        
    - accessMode: define the modes which we wanna use for claiming this Volume. We could again list multiple modes, but we will use the ReadWriteOnce, since it’s the only mode we allowed.
    - resources (like ==**capacity**== in host-pv.yaml): here we specify which resources we want to get for this claim. The storage ==**must be ≤ the capacity**==.(For example, we have multiple claims, but each claim adds up must be ≤ 1G)

  

### Establish connection

==deployment.yaml==

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: story-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: story
  template:
    metadata:
      labels:
        app: story
    spec:
      containers:
        - name: story-container
          image: songphuc191004/story-image:2
          volumeMounts:
            - mountPath: /app/story
              name: story-volume
      volumes:
        - name: story-volume
          persistentVolumeClaim: 
            claimName: host-pvc
```

We can keep the name (story-volume), but the type is different

claimName: metadata:name of the host-pvc

  

### Using a Claim in a Pod

Storage class defines to K8s how exactly a specific storage we wanna use should be provided

Storage class will provide important information to the PV configuration we set up. It works together with the PV and our standard storage class which we got on minikube is already configured for the hostpath type

→ We have to define the storage class in the PV and PVC yaml file, somewhere after capacity and volumeMode

host-pv.yaml

```YAML
apiVersion: v1
kind: PersistentVolume
metadata:
  name: host-pv
spec:
  capacity: 
    storage: 1Gi
  volumeMode: Filesystem
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  hostPath: 
    path: /data
    type: DirectoryOrCreate
```

  

host-pvc.yaml

```YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: host-pvc
spec:
  volumeName: host-pv
  accessModes:
    - ReadWriteOnce
  storageClassName: standard
  resources: 
    requests:
      storage: 1Gi
```

  

![[/image 9 8.png|image 9 8.png]]

  

Then we apply all 3 files: deployment.yaml, host-pv.yaml, and host-pvc.yaml

![[/image 10 7.png|image 10 7.png]]

  

⇒ We have achieved Pod- and Node-independent!

## Normal Volumes vs Persistent Volumes

![[/image 11 6.png|image 11 6.png]]

On a small project, we can use normal volumes, we can use PV, but it might be overkilled

On large project, PV is to-go Volume

## Environment Variables

![[/image 12 6.png|image 12 6.png]]

  

We set it in the Container like we use Docker and Docker Compose → deployment.yaml

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: story-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: story
  template:
    metadata:
      labels:
        app: story
    spec:
      containers:
        - name: story-container
          image: songphuc191004/story-image:2
          env:
            - name: STORY_FOLDER
              value: 'story'
          volumeMounts:
            - mountPath: /app/story
              name: story-volume
      volumes:
        - name: story-volume
          persistentVolumeClaim: 
            claimName: host-pvc
```

We can set many envs, after that, build the image, and apply the file

For [C#](https://learn.microsoft.com/en-us/visualstudio/bridge/kubernetes-environment-variables)

  

If we want Env Variables to not be Container specific, and different Containers spec from different Pods can utilize the same Env Vars → Create an object (resource) called ConfigMap

==environment.yaml==

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: data-store-env
data:
  folder: 'story'
  key: 'value'
  key1: 'value1'
```

Then, we will apply the file

![[/image 13 5.png|image 13 5.png]]

After that, we will modify the deployment.yaml

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: story-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: story
  template:
    metadata:
      labels:
        app: story
    spec:
      containers:
        - name: story-container
          image: songphuc191004/story-image:2
          env:
            - name: STORY_FOLDER
              valueFrom: 
	              configMapKeyRef:
		              name: data-store-env
			            key: folder
          volumeMounts:
            - mountPath: /app/story
              name: story-volume
      volumes:
        - name: story-volume
          persistentVolumeClaim: 
            claimName: host-pvc
```

We will still need the name of the env var

Instead of value, use ==**valueFrom**==

configMapKeyRef:name: name of the config map

configMapKeyRef:key: key of the value we want to get in the config map

After that, apply the deployment.yaml file

==Note: we can only use one either way, we can’t use both value and valueFrom==