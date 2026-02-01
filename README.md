
<h1 align="center">Concepts And Examples of K8S</h1>

---

- [Pod](#example-0)
  
 Types of Pods  :
    - [Pod](#example-1) ,   - [Replication of Pod](#example-2) ,   - [ReplicaSet](#example-3) ,     - [Deployment](#example-4)  ,   - [Static Pod](#example-5)  ,   - [Job](#example-6)  , - [CronJob](#example-7) ,  - [DaemonSet](#example-8)  
 
- [Service](#example-9)
  
Types of Kubernetes services  :
     - [ClusterIP](#example-10) ,  - [NodePort](#example-11) ,    - [LoadBalancer](#example-12)  ,  - [ExternalName](#example-13)
  

-  [Ways to create kubernates](#example-14)
-  [Pod & Service Commands , Demo Exammples](#example-15)
-  [Service-LoadBalancer](#example-18)
-  [Detail Replication controller & Replica set pod](#example-20)
-  [Detail Deployment set pod](#example-22)
-  [HelthChekers](#example-23)  :
     - [Liveness](#example-23)
     - [Readiness](#example-23)
     - [Startup](#example-23)


<br>


---


<a id="example-0"></a>

# POD 


### What is a Pod in Kubernetes? 🧩


A Pod is the smallest deployable unit in Kubernetes.<br>
It represents one or more containers that run together on the same node and share:


- 🧠 Network (same IP & port space)
- 💾 Storage (volumes)
- ⚙️ Configuration (environment, secrets)


👉 In simple words:<br>
Pod = Wrapper around container(s)



---


## There Are 8 Types of Pods


<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/types%20of%20pod.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



<a id="example-1"></a>

###  1️⃣ Pod 

Pod Creation – Manifest File 

When we create a Pod, we must write a YAML file.
From here onward, any YAML file used to create Kubernetes resources is called a Manifestation (Manifest) File.

4 Important Parts of a Manifest File :

1️⃣ apiVersion
➡️ Defines which Kubernetes API version is used to create the object.

2️⃣ kind
➡️ Specifies what type of object you are creating (Pod, Deployment, Service, etc.).

3️⃣ metadata
➡️ Stores basic information like Pod name, labels, and namespace.

4️⃣ spec
➡️ Contains the actual configuration of the Pod, such as container image, ports, and volumes.


Example :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: first-pod
spec:
  containers:
  - name: nginx
    image: nginx
```

```bash
kubectl apply -f pod.yml         # create a pod using yaml file with using this command
kubectl get pods                 # Check if Pod is created
```

key points :

- The smallest unit in Kubernetes
- A Pod can run one or more containers
- Containers in a Pod share network, storage, and configuration


Example:
Running a single Nginx container



>  👉 A Kubernetes manifest file always has apiVersion, kind, metadata, and spec, which define what to create and how to run it.


---



<a id="example-2"></a>


###  2️⃣ Replication of Pod


- Means running multiple copies of the same Pod
- Used for high availability and load balancing
- If one Pod fails, another one continues serving traffic

⚠️ Kubernetes does not do pod replication directly<br>
👉 It is handled by ReplicaSet / Deployment


---


<a id="example-3"></a>


###  3️⃣ ReplicaSet

- Ensures a fixed number of Pods are always running
- If a Pod crashes or is deleted, ReplicaSet creates a new Pod

Example:<br>
Maintain exactly 3 Pods at all times

---



<a id="example-4"></a>


###  4️⃣ Deployment


- A higher-level controller that manages ReplicaSets
- Supports:
   - Rolling updates
   - Rollbacks
   - Scaling

Example:<br>
Updating application from v1 to v2 without downtime

✅ Most commonly used in real projects


---



<a id="example-5"></a>


###   5️⃣ Static Pod


- Static Pods are created directly by kubelet, not by the Kubernetes API server.
- They are used for very critical components that must start first.
- Kubernetes control plane runs as Static Pods.
- Static Pods are defined as YAML files on the node (example path:` /etc/kubernetes/manifests`).
- If a Static Pod stops or crashes, kubelet restarts it automatically.
- These Pods are not created using `kubectl apply.`


Why Static Pods are needed :

- Cluster data must be stored in correct order (old → new).
- etcd (Static Pod) stores all data first (example: 2012 → 2015 → 2019 data).
- kube-apiserver (Static Pod) writes and reads data from etcd.
- kube-scheduler (Static Pod) uses this data to place Pods on nodes.
- If these don’t start first → cluster cannot work.

Examples of Static Pods :

- `etcd` → stores cluster data
- `kube-apiserver` → handles requests
- `kube-scheduler` → schedules Pods
- `kube-controller`-manager

> 👉 Static Pods run critical Kubernetes components that must start first and store data in proper order, so they are managed directly by kubelet.


---



<a id="example-6"></a>


###  6️⃣ Job 


-  Used for one-time tasks & then self destracted  itself
-  Pod runs, completes the task, and then stops

Example:

- Database migration
- One-time data processing  

---


<a id="example-7"></a>


###  7️⃣ CronJob


- Used for scheduled jobs (tasks)
- Runs Jobs at a specific time or interval

Example:

- Daily database backup at 12 AM
- Log cleanup every Sunday  

---



<a id="example-8"></a>



###   8️⃣ DaemonSet

- Ensures one Pod runs on every node ( monetering pod )
- When a new node joins, a Pod is automatically created


Example:

- Log collectors (Fluentd)
- Monitoring agents

---
---

<a id="example-15"></a>

#   Example

- [too see Screenshots](#example-16)
- [Script](#example-17)
  
A Pod can be created using a command like:

```
kubectl run mypod --image=nginx --port=80
```

###  Problem with this approach:

When you create Pods using commands, it is not reusable. You cannot easily replicate it or apply auto-scaling.

###   Why files (YAML) are better:

If you define the Pod in a YAML file, you can reuse it, replicate Pods easily, and enable auto-scaling, Version control.<br>

###   Limitation of commands:

When using commands, you have to run the same command again and again.
For example, if you want to create 1000 containers, you would need to execute the command repeatedly, which is not practical.

###  ⚠️ Important Note

- This command creates a single Pod only.
- It is not reusable and not recommended for production.
- Auto-scaling and replication are not possible with this method.
- To create multiple Pods, the command must be executed repeatedly.


Pod Commands

```
kubectl run mypod --image=nginx --port=80 
```

🔍 Explanation
- `kubectl run` → Used to create a Pod
- `mypod` → Name of the Pod
- `--image=nginx` → Container image used for the Pod
- `--port=80` → Port exposed by the container (informational)
  
```

kubectl run mypod --image=nginx --port=80             # Create A pod using command

kubectl exec -it <pod-name> -- /bin/bash             # Command to access the Pod shell.

kubectl get pods                                      #  To check whether a Pod has been created or not, use the following command
kubectl get pod
kubectl get po

kubectl get pods -n <namespace-name>                  #  To view Pods in a specific namespace


kubectl describe pod <pod-name>                       # To see detailed information about a Pod
kubectl get all

kubectl get pods -o wide                              #  To view Pod status with node and IP details

##  On our behalf, Kubernetes creates a file that contains all the required details. To view those details, run this command.

kubectl get pods -o yaml
kubectl get pods -o json


kubectl logs <pod-name>                                #  To check Pod logs
kubectl get pods --show-labels                         #  To show labels with pods
kubectl get all                                        # shows all common Kubernetes resources (pods, services, deployments, replicasets, etc.) in the current namespace

kubectl get rc myrc                                    # This command is used to get detailed information    # about a specific ReplicationController named "myrc"
kubectl scale rc myrc --replicas=6                     # This command is used to scale the ReplicationController   # It changes the number of Pod replicas to 6
kubectl scale rc myrc --replicas=15 && kubectl get rc myrc --watch       # This command scales the ReplicationController "myrc" to 15 replicas and then continuously watches its status in real time
kubectl delete rc myrc                                 # Delete the rc

kubectl rollout history deployment mydeployment                     # This command shows rollout history of a deployment
kubectl rollout history deployment mydeployment --revision=2        # shows details of revision 2 of the deployment
kubectl rollout undo deployment mydeployment                        # rolls back the deployment to the previous revision
kubectl rollout undo deployment mydeployment --to-revision=2        # rolls back the deployment to a specific revision (2)
kubectl set image deployment mydeployment nginx=nginx:1.25 --record   # updates the container image and records the change in history
kubectl rollout --help
                    


# Delete Pod:

rm -f pod.yml                                           # file is delete but pod  still running
kubectl delete -f pod.yml 

kubectl delete pod <pod-name>                           # Delete a specific Pod
kubectl delete pod <pod-name> -n <namespace-name>       # Delete Pod from a specific namespace
```











Services Commands:

```
kubectl expose pod mypod --name=mysvc --type=NodePort --port=80 --target-port=80
```
🔍 Explanation

- `kubectl expose` → Creates a Service
- `pod mypod` → Pod you want to expose
- `--name=mysvc` → Name of the Service
- `--type=NodePort` → Exposes the Service on a Node port
- `--port=80` → Service port
- `--target-port=80` → Container port inside the Pod

```
kubectl get svc             # check the Service
kubectl get svc mysvc       # To see the NodePort number
<NodeIP>:<NodePort>         # You can access the application using

```
```

kubectl get services                          # To view all Services
kubectl get svc                               # To view all Services

kubectl get svc -n <namespace-name>           #  To check Services in a specific namespace

kubectl describe service <service-name>       #  To see detailed information about a Service


kubectl delete service <service-name>                        # Delete a specific Service
kubectl delete svc <service-name>                            # Delete a specific Service
kubectl delete svc <service-name> -n <namespace-name>        # Delete Service from a specific namespace
```

>  Apply, Run, Execuite `.YAML` file

```
kubectl apply -f filename.yml
kubectl delete -f filename.yml   # 🗑️ Delete using YAML file
```

<a id="example-16"></a>


##  Screenshots

###   Run through commands




| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-26%20090542.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-26%20090636.png?raw=true) | 


---

-  To see Webpage Add port To node server

  <p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-26%20084841.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-26%20085208.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



###   Run through   .yml  files



| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-26%20090811.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-26%20090828.png?raw=true) | 



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-26%20090435.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

<a id="example-17"></a>

`Pod.yml`

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  labels:
    app: mynginx
spec:
   containers:
     - name: mycontainer
```
```
# apiVersion specifies the Kubernetes API version being used
apiVersion: v1

# kind defines the type of Kubernetes object (here, it is a Pod)
kind: Pod

# metadata contains information to identify the Pod
metadata:
  # name is the unique name of the Pod
  name: mypod

  # labels are key-value pairs used for grouping and selecting resources
  labels:
    # app label helps identify the application running in the Pod
    app: mynginx

# spec defines the desired state/configuration of the Pod
spec:
  # containers section defines the containers that run inside the Pod
  containers:
    # name is the name of the container inside the Pod
    - name: mycontainer
```

`service.yml`

```
apiVersion: v1
kind: Service
metadata:
  name: mysvc
spec:
  selector:
    app: mynginx
  type: NodePort
  ports:
    - protocol: TCP
      port: 80 # service port
      targetPort: 80 # pod port
```
```
# apiVersion specifies the Kubernetes API version
apiVersion: v1

# kind defines the Kubernetes resource type (Service)
kind: Service

# metadata contains identifying information about the Service
metadata:
  # name is the unique name of the Service
  name: mysvc

# spec defines the desired configuration of the Service
spec:
  # selector connects the Service to Pods with matching labels
  selector:
    # this Service will route traffic to Pods labeled app: mynginx
    app: mynginx

  # type defines how the Service is exposed
  # NodePort exposes the Service on a static port on each node
  type: NodePort

  # ports defines how traffic is mapped
  ports:
    # protocol used for communication
    - protocol: TCP

      # port is the port exposed by the Service inside the cluster
      port: 80 # service port

      # targetPort is the port on which the container listens
      targetPort: 80 # pod port
```

---
---

<a id="example-9"></a>


#  Service


A Service is a Kubernetes object that provides a stable IP address and DNS name to access a set of Pods, even when Pods are recreated or their IPs change.<br>
In Kubernetes, a Service distributes network traffic to Pods.

👉 Simple line:<br>
Service = Permanent access point for changing Pods


Why do we need a Service? 

- Pods are temporary (IP changes when Pod restarts)
- Users / apps need a fixed way to access Pods
- Service solves this problem


##  Types of Kubernetes Services (Main 4 Types) ✅



<p align="center">
  <img src="https://github.com/nikiimisal/Kubernetes_-K8s-_Info-and-Setup/blob/main/img/kub%20services.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



<a id="example-10"></a>


###  1️⃣ ClusterIP (Default)


- Communication between inside node pods
- Exposes Pods inside the cluster only
- Not accessible from outside
  
📌 Example:
- Backend app
- Database service

---



<a id="example-11"></a>


###  2️⃣ NodePort

- communaction between different node pods
- Load balance between pods 
- Exposes Service on a fixed port on every node
- Accessible using:
  - `NodeIP : NodePort`

📌 Example:
- Testing applications
- Dev environments

---


<a id="example-12"></a>


###   3️⃣ LoadBalancer

- Balance out the load between nodes
- Creates an external load balancer (Cloud-based)
- Gives a public IP

📌 Example:
- Production web applications (AWS, Azure, GCP)

---

<a id="example-13"></a>


###  4️⃣ ExternalName

- Maps a Service to an external DNS name
- No selector or Pods involved

📌 Example:
- Accessing external database or API


---
###  Quick Recap


| Service Type | Access         |
| ------------ | -------------- |
| ClusterIP    | Inside cluster |
| NodePort     | NodeIP : Port  |
| LoadBalancer | Public IP      |
| ExternalName | External DNS   |


---
---


<a id="example-14"></a>

#  Ways to create kubernates 

### 1️⃣ Minikube

- Contains only one node cluster... that node act as a master as well as worker node 
- Creates a single-node Kubernetes cluster
- Runs inside VM or Docker on your laptop
- Very easy to install and use

Used for:<br>
Learning, practice, testing YAML files


Limitation:<br>
Not suitable for production

---

###  2️⃣ Kind (Kubernetes in Docker)

- Runs Kubernetes inside Docker containers
- No VM required → very fast
- Can create multi-node clusters locally


Used for:<br>
CI/CD pipelines, fast testing


Limitation:<br>
Only for development and testing


---


###  3️⃣ kubeadm

- Master & worker node deployment but manually
- All the infrastructure managed by you .
- On premices , cloud vm --> deployment 
- Official Kubernetes tool
- Used to create real, production-like clusters
- Supports multi-node (master + workers)

Used for:<br>
Interviews, labs, on-prem clusters


Limitation:<br>
Manual setup and maintenance required

---

###  4️⃣ Managed Kubernetes (Cloud Services)


🔹 Amazon EKS<br>
🔹 Azure AKS<br>
🔹 Google GKE


- Cloud provider manages control plane
- High availability, auto scaling
- Very secure and reliable


Used for:<br>
Production applications

Limitation:<br>
Cost involved


---

###  5️⃣ On-Prem / Bare Metal Kubernetes

- Installed on physical servers
- Full control over cluster
- No cloud dependency


Used for:<br>
Enterprises, private data centers

Limitation:<br>
Very complex to manage


---


###  Quick Comparison Table 🧠



| Method          | Nodes         | Best For      |
| --------------- | ------------- | ------------- |
| Minikube        | Single        | Learning      |
| Kind            | Multi (local) | CI/CD         |
| kubeadm         | Multi         | Real practice |
| EKS / AKS / GKE | Multi         | Production    |
| On-Prem         | Multi         | Enterprise    |


---
---


<a id="example-18"></a>


#   Service - Loadbalancer

#  Quick Overview: Using Load Balancer Service (AWS)

- We are going to use a Load Balancer Service.
- Open the AWS Console and go to Load Balancers.
- Click on Application Load Balancer.
- Give the Load Balancer a name.
- Select Scheme: Internet-facing (because the application should be publicly accessible).
- Select the default VPC.
- Choose the same security group that is attached to the Kubernetes master and worker nodes.
- Click Next and create a Target Group.
- While creating the Target Group, you must provide a Node port number because traffic will be forwarded through this port to node  -> service -> pod.
- To find the port:
   - Access the Kubernetes master terminal,There you will see node port ip.
   - Run:
  ```
  kubectl get svc
  ```
  -  Note the NodePort shown there.


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20192705.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


- Use this NodePort as the Target Group port.
- Create the Target Group.
  
<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20192837.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

- Go back to the Load Balancer and attach/select this Target Group.
- Once completed, the Load Balancer for Kubernetes is ready.

Done ✅ Kubernetes Load Balancer is ready.

<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20193021.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

>>Before connecting the Load Balancer to the Pod (or Node), make sure the Pod and Service are running on your master server.


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20193901.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

-  To check the LoadBalancer is sucessfully run copy the LB DNS name and pest to the browser



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20194111.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---

- If we want to change the content inside a Pod, we need to enter the Pod.
- The file must be edited from inside the Pod.
- Package managers are usually not installed inside the Pod.
- Because of this, editing files directly inside the Pod is not easy.

An error is occurring. See this screenshot.


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20201141.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

- There are 2 temperery steps to follow, as shown in the screenshot.




| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20201716.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20201726.png?raw=true) |


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-29%20201044.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


>  note :<br>
> - This is temporary.<br>
> - Problem: If the Pod restarts, all changes are lost and it shows the default “Welcome to Nginx” page again.<br>
> - To make data permanent, use a `ConfigMap` instead of editing files inside the Pod.



---
---


<a id="example-20"></a>


#    Detail Replication controller  & Replica set pod 

  [Example or say practicess Schreenshots](#example-19)

###  Why we need ReplicationController / ReplicaSet (before comparing)

- A Pod is not reliable by itself.
- If a Pod crashes, gets deleted, or the node fails, Kubernetes will NOT recreate it automatically.
- To solve this problem, Kubernetes uses controllers.
- ReplicationController and ReplicaSet are controllers that:
   - Keep a fixed number of Pods running
   - Automatically create new Pods if existing ones fail

###  1️⃣ ReplicationController (RC)


What is it?<br>
- ReplicationController is an old Kubernetes controller.
- Its job is to maintain a specified number of Pod replicas.

- Older Kubernetes object (mostly legacy now).
- Ensures a fixed number of Pod replicas are always running.
- Uses equality-based selectors only (`key=value`).
- If a Pod crashes or is deleted, the ReplicationController creates a new Pod.
- Cannot handle complex label selection.
- Mostly replaced by ReplicaSet and Deployment.


How it works

- You define:
  - Number of replicas
  - Pod template
  - Label selector

- Kubernetes checks continuously:
  - If Pods < desired count → create new Pods
  - If Pods > desired count → delete extra Pods

Selector limitation

- Supports only equality-based selectors
- Example:
```
selector:
  app: web
```
- Cannot use advanced rules like `In`, `NotIn`, `Exists`


Pod Behavior

- Pods are recreated only based on exact label matches.
- Less flexible for scaling and updates.

- Drawbacks
   - Limited label selection
   - No rolling update support
   - Not flexible for modern workloads
   - Mostly deprecated


>> note : RC --> Dosen't support condation


###  2️⃣ ReplicaSet (RS)


What is it?

- ReplicaSet is the next-generation version of ReplicationController.
- Does the same basic job but in a more powerful way.


- Newer and improved version of ReplicationController.
- Also ensures the desired number of Pod replicas are running.
- Supports set-based and equality-based selectors.
- Commonly used with Deployments (not usually created directly).
- More powerful and flexible than ReplicationController.


How it works

- Maintains desired number of Pod replicas
- Uses advanced label selectors
- Works smoothly with Deployments

Selector support (big advantage)

- Supports:
  - Equality-based selectors
  - Set-based selectors
- Example:

```
selector:
  matchExpressions:
    - key: app
      operator: In
      values:
        - web
        - frontend
```

Integration with Deployment

- You usually don’t create ReplicaSet directly
- A Deployment creates and manages ReplicaSets for:
  - Rolling updates
  - Rollbacks
  - Version control

>> note : RS --> does support condation 

---

###  🔄 Rolling Updates (Major Difference)

ReplicationController

- ❌ No rolling update support
- Updating Pods causes downtime

ReplicaSet

- ✅ Rolling updates via Deployment
- Pods update gradually
- Zero-downtime deployments possible

---

###  📈 Scaling Comparison 

| Feature         | ReplicationController | ReplicaSet               |
| --------------- | --------------------- | ------------------------ |
| Auto-healing    | Yes                   | Yes                      |
| Manual scaling  | Yes                   | Yes                      |
| Auto-scaling    | Limited               | Works with HPA           |
| Update strategy | Recreate              | Rolling (via Deployment) |


---

###  🧠 Real-World Usage

- ReplicationController
  - Rarely used today
  - Only for legacy clusters
- ReplicaSet
  - Actively used
  - Always created by Deployment
    
---

###   🔑 Final Summary (Easy to Remember)

- Pod → Single instance (not reliable)
- ReplicationController → Old Pod manager
- ReplicaSet → Modern Pod manager
- Deployment → Best practice (uses ReplicaSet internally)

---
---



Replication controller file `myrc.yml`
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: myrc
spec:
  replicas: 3
  selector:
    app: myapp
  template:
    metadata:
      name: myapp
      labels:
        app: myapp
    spec:
      containers:
        - name: mycontainer
          image: nginx
          ports:
            - containerPort: 80
```


```yaml
# ReplicationController configuration file
# Note: ReplicationController does NOT support condition-based selectors

apiVersion: v1
# Specifies the Kubernetes API version

kind: ReplicationController
# Defines the resource type as ReplicationController

metadata:
  name: myrc
  # Name of the ReplicationController

spec:
  replicas: 3
  # Specifies how many Pod replicas should be running

  selector:
    app: myapp
    # The selector helps the ReplicationController
    # identify and manage Pods based on labels
    # (only equality-based selection is supported)

  template:
    # Pod template used to create new Pods
    metadata:
      name: myapp
      labels:
        app: myapp
        # Labels applied to Pods created by this controller

    spec:
      containers:
        - name: mycontainer
          # Name of the container inside the Pod

          image: nginx
          # Container image to be used

          ports:
            - containerPort: 80
            # Port exposed by the container
```

replica set file `myrc.yml`

```
## Replication set file -> It does supports the conditions
apiVersion: v1
kind: ReplicaSet
metadata:
  name: myrs
spec:
  replicas: 3
  selector: # adopt pods based on conditions
    matchExpressions:
      - key: env
        operator: In
        values: [dev, test]
  template: # create a new pod with label (env: test)
    metadata:
      labels:
        env: test
    spec:
      containers:
        - name: mycontainer
          image: nginx
```

>⚠️ Important: ReplicaSet uses apiVersion: apps/v1 (not v1)

```yaml
# ReplicaSet configuration file
# ReplicaSet supports condition-based selectors

apiVersion: apps/v1
# Specifies the API version for ReplicaSet

kind: ReplicaSet
# Defines the Kubernetes resource type as ReplicaSet

metadata:
  name: myrs
  # Name of the ReplicaSet

spec:
  replicas: 3
  # Number of Pod replicas to maintain

  selector:
    # Selector is used to adopt Pods based on conditions
    matchExpressions:
      - key: env
        # Label key to match

        operator: In
        # Condition operator (supports In, NotIn, Exists, DoesNotExist)

        values: [dev, test]
        # Pods with env=dev or env=test will be managed

  template:
    # Pod template used to create new Pods
    metadata:
      labels:
        env: test
        # Label applied to newly created Pods

    spec:
      containers:
        - name: mycontainer
          # Name of the container

          image: nginx
          # Container image
```

<a id="example-19"></a>

#  Screenshots

  [Too see Commands](#example-15)


###  Replication controller

| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-30%20153449.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-30%20153500.png?raw=true) |


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-30%20153526.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---

###   Replica set




<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-30%20153604.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-01-30%20153845.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>




---
---

<a id="example-22"></a>


#  Deployment Set

[Example or say practicess Schreenshots](#example-21)


### What is it?
- Deployment is a modern and most commonly used Kubernetes controller.
- Its main job is to manage Pods using ReplicaSets.
- It ensures the desired number of Pod replicas are always running.
- Provides rolling updates and rollback support.
- If a Pod crashes or is deleted, the Deployment automatically recreates it.
- Uses ReplicaSet internally to manage Pods.
- Recommended controller for production workloads.
  
---

### How it works
- You define:
  - Number of replicas
  - Pod template
  - Label selector
  - Update strategy(RollingUpdate / Recreate)

- Kubernetes continuously monitors the cluster:
  - Deployment creates a ReplicaSet
  - ReplicaSet creates and manages Pods
  - If a Pod fails → ReplicaSet recreates it
  - Deployment monitors ReplicaSet health and status

---

### Update behavior (Rolling Updates)

- Supports rolling updates by default
- Pods are updated gradually, not all at once
- Ensures zero or minimal downtime
- Old Pods are terminated only after new Pods become ready
  
---

###  Rollback support

- Deployment keeps revision history
- You can easily rollback to a previous version
- Useful when a new release causes issues
  
---

### Selector behavior

- Uses ReplicaSet selectors
- Supports:
   - Equality-based selectors
   - Condition-based selectors (`In`,` NotIn`, `Exists`)
- Much more flexible than ReplicationController

---

###  Pod behavior

- Pods are managed indirectly through ReplicaSets
- Scaling is smooth and safe
- Supports auto-scaling with HPA (Horizontal Pod Autoscaler)

---

###   Advantages

- Rolling updates support
- Easy rollback to previous versions
- High availability
- Best suited for modern, scalable applications

> note: Deployment is the recommended controller for production environments and internally uses ReplicaSet.


Deployment set script `deploy.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mydeploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: mycontainer
        image: nginx
        ports:
        - containerPort: 80
```
```yaml
# API version used for Deployment
apiVersion: apps/v1

# Defines the Kubernetes resource type
kind: Deployment

# Metadata contains basic information about the Deployment
metadata:
  # Name of the Deployment
  name: mydeploy

spec:
  # Number of Pod replicas to maintain
  replicas: 3

  # Selector tells Deployment which Pods it should manage
  selector:
    matchLabels:
      # Deployment will manage Pods with this label
      app: myapp

  # Template is used to create new Pods
  template:
    metadata:
      # Labels applied to Pods created by this Deployment
      labels:
        app: myapp

    spec:
      # List of containers inside the Pod
      containers:
      - name: mycontainer
        # Name of the container

        image: nginx
        # Container image to run

        ports:
        - containerPort: 80
          # Port exposed by the container

```








---
---


<a id="example-21"></a>

#  Screenshots


- Write Script and apply Deployment set pod

| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS]() | ![AWS]() |


- After modifying the image from nginx to httpd in the same deployment file and reapplying it, the deployment was updated to use the httpd image.


| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS]() | ![AWS]() |


-  If we want to check the rollout history
-  Additionally, to monitor how the deployment process is progressing

<p align="center">
  <img src="" width="500" alt="Initialize Repository Screenshot">
</p>


---
---




























---
---
---

<a id="example-23"></a>


<h1>HelthChekers</h1>

[Example or say practicess Schreenshots](#example-24)

###   🔍 What are Health Checkers in Kubernetes?

Health checkers are called Probes.<br>
They are used by Kubernetes to check the health of a container inside a Pod.

Kubernetes supports 3 types of probes:

1️⃣ Liveness Probe<br>
2️⃣ Readiness Probe<br>
3️⃣ Startup Probe


---

###  1️⃣ Liveness Probe

👉 What is it?

- Checks whether the container is alive or stuck.
- If the liveness probe fails:
   - Kubernetes restarts the container.

👉 Why needed?

- Sometimes an app is running but not responding (deadlock, infinite loop).
- Liveness probe helps Kubernetes recover automatically.

👉 Example

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5
```

👉 Behavior

- App not responding ❌
- Probe fails ❌
- Container restarted 🔄

---

###  2️⃣ Readiness Probe

👉 What is it?

- Checks whether the container is ready to receive traffic.
- If readiness probe fails:
   - Pod is removed from Service endpoints
   - Container is NOT restarted

👉 Why needed?

- App may be running but:
   - Database not connected
   - App still loading
- Prevents traffic from reaching an unready Pod

👉 Example

```yaml
readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 5
```

👉 Behavior

- App not ready ❌
- Traffic stopped 🚫
- No restart 🔕


---

###   3️⃣ Startup Probe

👉 What is it?

- Used for slow-starting applications.
- Tells Kubernetes:

>Wait until the app fully starts

👉 Why needed?

- Some apps take time (Java, Spring Boot, heavy apps)
- Without startup probe:
   - Liveness probe may fail early
   - Container may restart again and again

👉 Example
```yaml
startupProbe:
  httpGet:
    path: /
    port: 80
  failureThreshold: 30
  periodSeconds: 10
```
👉 Behavior

- Until startup probe succeeds:
- Liveness & Readiness are disabled
- Once startup completes
- Normal probes start working

---

###  Flow chart

```mathematica
            Pod Created
                 │
                 ▼
        ┌─────────────────┐
        │  Startup Probe  │
        └─────────────────┘
                 │
        ┌────────┴────────┐
        │                 │
   ❌ Fail            ✅ Success
        │                 │
 Restart Container     Startup Complete
                          │
                          ▼
        ┌──────────────────────────┐
        │  Liveness + Readiness    │
        │        Probes            │
        └──────────────────────────┘
             │               │
             │               │
     ┌───────┘               └────────┐
     ▼                                ▼
Liveness Fail                   Readiness Fail
(Container Dead)               (Not Ready for Traffic)
     │                                │
     ▼                                ▼
Restart Container            Remove Pod from Service
                                   (No traffic)
```

---

###  🧪 Types of Probe Checks

Kubernetes supports 3 probe methods:

1️⃣ HTTP GET
```yaml
httpGet:
  path: /health
  port: 8080
```

2️⃣ TCP Socket
```yaml
tcpSocket:
  port: 3306
```

3️⃣ Exec Command
```yaml
exec:
  command:
    - cat
    - /tmp/healthy
```

---

🔄 Comparison Table

| Probe Type | Purpose            | Restart Container | Traffic Allowed |
| ---------- | ------------------ | ----------------- | --------------- |
| Liveness   | Is app alive?      | ✅ Yes             | ✅ Yes           |
| Readiness  | Ready for traffic? | ❌ No              | ❌ No            |
| Startup    | Has app started?   | ❌ No              | ❌ No            |


---

🧠 Easy to Remember

- Liveness → Restart if app is dead  //  Can app start? //  App loading configs
- Readiness → Stop traffic if app is not ready  //  Should app be restarted?  //  DB connection ready
- Startup → Give time to slow apps  //  Should app receive traffic?  //  App health endpoint responding


---
---


<a id="example-24"></a>

#  Screenshots







































































































  
  
