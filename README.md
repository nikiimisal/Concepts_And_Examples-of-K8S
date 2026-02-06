
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
- [What is service in K8S & also Diff Between Service and lables](#example-25)
- [Namespace's](#example-26)
- [Volume's](#example-28)
- [Jobs & Cronjobs](#example-33)


---
[All Related Command's](#example-100)
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



<a id="example-100"></a>


###    Command's

  
```yaml
# ================== POD – ALL IMPORTANT COMMANDS ==================


# ---------- CREATE & ACCESS POD ----------

kubectl run mypod --image=nginx --port=80
# Create a Pod using command

kubectl exec -it <pod-name> -- /bin/bash
# Access the Pod shell


# ---------- LIST PODS ----------

kubectl get pods
kubectl get pod
kubectl get po
# Check whether Pods are created or not

kubectl get pods -n <namespace-name>
# View Pods in a specific namespace


# ---------- POD DETAILS ----------

kubectl describe pod <pod-name>
# See detailed information about a Pod

kubectl get all
# Show all resources in current namespace

kubectl get pods -o wide
# View Pod status with node and IP details


# ---------- POD YAML / JSON OUTPUT ----------
#  On our behalf, Kubernetes creates a file that contains all the required details. To view those details, run this command.

kubectl get pods -o yaml                   # View complete Pod configuration
kubectl get pods -o json


# ---------- POD LOGS & LABELS ----------

kubectl logs <pod-name>                 # Check Pod logs
kubectl get pods --show-labels          # Show labels with Pods
kubectl get all                         # shows all common Kubernetes resources (pods, services, deployments, replicasets, etc.) in the current namespace



# ---------- REPLICATION CONTROLLER (RC) ----------

kubectl get rc myrc                                    # This command is used to get detailed information    # about a specific ReplicationController named "myrc"
kubectl scale rc myrc --replicas=6                     # This command is used to scale the ReplicationController   # It changes the number of Pod replicas to 6
kubectl scale rc myrc --replicas=15 && kubectl get rc myrc --watch       # This command scales the ReplicationController "myrc" to 15 replicas and then continuously watches its status in real time
kubectl delete rc myrc                                 # Delete the rc



# ---------- DEPLOYMENT ROLLOUT COMMANDS ----------

kubectl rollout history deployment mydeployment                     # This command shows rollout history of a deployment
kubectl rollout history deployment mydeployment --revision=2        # shows details of revision 2 of the deployment
kubectl rollout undo deployment mydeployment                        # rolls back the deployment to the previous revision
kubectl rollout undo deployment mydeployment --to-revision=2        # rolls back the deployment to a specific revision (2)
kubectl set image deployment mydeployment nginx=nginx:1.25 --record   # updates the container image and records the change in history
kubectl rollout --help                                               # Rollout help commands
                    


# ---------- DELETE POD ----------

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




```yaml
# ================== SERVICE – ALL IMPORTANT COMMANDS ==================

kubectl get svc             # check the Service
kubectl get svc mysvc       # To see the NodePort number
<NodeIP>:<NodePort>         # You can access the application using

```
```yaml

kubectl get services                          # To view all Services
kubectl get svc                               # To view all Services

kubectl get svc -n <namespace-name>           #  To check Services in a specific namespace

kubectl describe service <service-name>       #  To see detailed information about a Service


kubectl delete service <service-name>                        # Delete a specific Service
kubectl delete svc <service-name>                            # Delete a specific Service
kubectl delete svc <service-name> -n <namespace-name>        # Delete Service from a specific namespace
```

>  Apply, Run, Execuite `.YAML` file

```yaml
kubectl apply -f filename.yml
kubectl delete -f filename.yml   # 🗑️ Delete using YAML file
```

###  Namespace 

```yaml
# ================== NAMESPACE – ALL IMPORTANT COMMANDS ==================


# ---------- LIST NAMESPACES ----------

kubectl get namespaces
# List all namespaces

kubectl get ns
# Short form to list namespaces


# ---------- CREATE & DELETE NAMESPACE ----------

kubectl create namespace dev
# Create a namespace named dev

kubectl delete namespace dev
# Delete the dev namespace


# ---------- WORK WITH PODS IN A NAMESPACE ----------

kubectl get pods -n dev
# List all Pods in dev namespace

kubectl get pod mypod -n dev
# Get a specific Pod from dev namespace

kubectl describe pod mypod -n dev
# Show detailed information about a Pod

kubectl delete pod mypod -n dev
# Delete a Pod from dev namespace


# ---------- CREATE RESOURCES IN A SPECIFIC NAMESPACE ----------

kubectl apply -f pod.yml -n dev
# Create a Pod in dev namespace

kubectl apply -f deployment.yml -n dev
# Create a Deployment in dev namespace

kubectl apply -f service.yml -n dev
# Create a Service in dev namespace


# ---------- SERVICE COMMANDS WITH NAMESPACE ----------

kubectl describe service <service-name> -n dev
# Show detailed info of a Service

kubectl delete service <service-name>
# Delete a Service from default namespace

kubectl delete svc <service-name>
# Short form to delete a Service

kubectl delete svc <service-name> -n dev
# Delete a Service from dev namespace


# ---------- SET & CHECK DEFAULT NAMESPACE ----------

kubectl config set-context --current --namespace=dev
# Set dev as default namespace

kubectl config view --minify
# Show current context details

kubectl config view --minify | grep namespace
# Verify the current default namespace


# ---------- GET ALL RESOURCES ----------

kubectl get all -n dev
# List all resources in dev namespace


# ---------- CLUSTER-SCOPED (NON-NAMESPACED) RESOURCES ----------

kubectl api-resources --namespaced=false
# List resources that are NOT bound to any namespace
# Examples: nodes, namespaces, persistentvolumes


```


###  Volume's

```yaml
# ================== VOLUMES – ONLY COMMANDS ==================


# ---------- POD / DEPLOYMENT WITH VOLUMES ----------

kubectl apply -f pod.yml
# Create Pod with volume

kubectl apply -f deployment.yml
# Create Deployment with volume

kubectl delete pod <pod-name>
# Delete Pod

kubectl delete deployment <deployment-name>
# Delete Deployment


# ---------- CHECK VOLUMES IN POD ----------

kubectl get pods
# List Pods

kubectl describe pod <pod-name>
# See volume & volumeMount details

kubectl get pod <pod-name> -o yaml
# View volume configuration in YAML


# ---------- EXEC INTO POD (VERIFY VOLUME) ----------

kubectl exec -it <pod-name> -- /bin/bash
# Enter container

df -h
# Check mounted storage

mount
# List mounted volumes

ls <mount-path>
# Verify data inside mounted volume


# ---------- CONFIGMAP (VOLUME SOURCE) ----------

kubectl create configmap mysqlconfig --from-literal=MYSQL_ROOT_PASSWORD=root
# Create ConfigMap from key-value

kubectl create configmap appconfig --from-file=config.txt
# Create ConfigMap from file

kubectl get configmap
# List ConfigMaps

kubectl describe configmap mysqlconfig
# View ConfigMap details

kubectl delete configmap mysqlconfig
# Delete ConfigMap


# ---------- SECRET (VOLUME SOURCE) ----------

kubectl create secret generic mysqlsecret --from-literal=mysql_root_password=root
# Create Secret from key-value

kubectl create secret generic tlssecret --from-file=tls.key --from-file=tls.crt
# Create Secret from files

kubectl get secrets
# List Secrets

kubectl describe secret mysqlsecret
# View Secret details

kubectl delete secret mysqlsecret
# Delete Secret


# ---------- EMPTYDIR VOLUME ----------

kubectl apply -f emptydir-pod.yml
# Create Pod using emptyDir volume

kubectl delete pod emptydir-pod
# Delete Pod (data removed automatically)


# ---------- HOSTPATH VOLUME ----------

kubectl apply -f hostpath-pod.yml
# Create Pod using hostPath

kubectl describe pod <pod-name>
# Verify hostPath mount

kubectl delete pod <pod-name>
# Delete Pod (node data remains)


# ---------- NFS / EFS VOLUME ----------

kubectl apply -f nfs.yml
# Create Deployment using NFS/EFS

kubectl describe pod <pod-name>
# Verify NFS mount

kubectl delete deployment mydeploy
# Delete Deployment using NFS


# ---------- PERSISTENT VOLUME (PV) ----------

kubectl apply -f pv.yml
# Create PersistentVolume

kubectl get pv
# List PVs

kubectl describe pv <pv-name>
# View PV details

kubectl delete pv <pv-name>
# Delete PV


# ---------- PERSISTENT VOLUME CLAIM (PVC) ----------

kubectl apply -f pvc.yml
# Create PVC

kubectl get pvc
# List PVCs

kubectl describe pvc <pvc-name>
# View PVC details

kubectl delete pvc <pvc-name>
# Delete PVC


# ---------- DEBUG / TROUBLESHOOT ----------

kubectl get events
# Check volume related errors

kubectl logs <pod-name>
# Check Pod logs

kubectl describe pod <pod-name> | grep -i volume
# Filter volume info

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

```yaml
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
```yaml
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

```yaml
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
```yaml
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

-  [Example or say practicess Schreenshots](#example-19)

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
```yaml
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

```yaml
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
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20144651.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20144733.png?raw=true) |


- After modifying the image from nginx to httpd in the same deployment file and reapplying it, the deployment was updated to use the httpd image.


| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20144859.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20144826.png?raw=true) |


-  If we want to check the rollout history
-  Additionally, to monitor how the deployment process is progressing

<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20144955.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


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

1️⃣ Liveness Probe `helthcheker.yml`

```yaml
# API version used for Deployment
apiVersion: apps/v1

# Defines the Kubernetes object type
kind: Deployment

# Metadata for the Deployment
metadata:
  # Name of the Deployment
  name: mydeploy

spec:
  # Number of Pod replicas to run
  replicas: 3

  # Selector to identify Pods managed by this Deployment
  selector:
    matchLabels:
      app: myapp

  # Pod template used to create Pods
  template:
    metadata:
      # Labels applied to Pods
      labels:
        app: myapp

    spec:
      # Container specification
      containers:
      - name: mycontainer
        # Name of the container

        image: nginx
        # Container image

        ports:
        - containerPort: 80
          # Port exposed by the container

        # Liveness probe checks if the container is alive
        livenessProbe:
          httpGet:
            # Kubernetes sends an HTTP GET request
            path: /index.html
            # URL path to check inside the container
            port: 80
            # Port on which the application is running

          initialDelaySeconds: 5
          # Waits 5 seconds after container starts before first check

          periodSeconds: 5
          # Performs the check every 5 seconds

          timeoutSeconds: 2
          # Probe fails if no response within 2 seconds

          failureThreshold: 3
          # After 3 consecutive failures, container is restarted
```

2️⃣ Readiness Probe  `helthcheker.yml`

```yaml
# API version used for Deployment
apiVersion: apps/v1

# Defines the Kubernetes object type
kind: Deployment

# Metadata for the Deployment
metadata:
  name: mydeploy
  # Name of the Deployment

spec:
  replicas: 3
  # Number of Pod replicas

  selector:
    matchLabels:
      app: myapp
      # Deployment will manage Pods with this label

  template:
    metadata:
      labels:
        app: myapp
        # Labels assigned to Pods

    spec:
      containers:
      - name: mycontainer
        # Container name

        image: nginx
        # Container image

        ports:
        - containerPort: 80
          # Port exposed by the container

        # ---------------- LIVENESS PROBE ----------------
        livenessProbe:
          httpGet:
            path: /index.html
            # Path used to check if container is alive
            port: 80
            # Port used for liveness check

          initialDelaySeconds: 5
          # Wait 5 seconds before starting liveness checks

          periodSeconds: 5
          # Check every 5 seconds

          timeoutSeconds: 2
          # Fail if no response in 2 seconds

          failureThreshold: 3
          # Restart container after 3 consecutive failures

        # ---------------- READINESS PROBE ----------------
        readinessProbe:
          httpGet:
            path: /login.html
            # Path used to check if container is ready to receive traffic
            port: 80
            # Port used for readiness check

          initialDelaySeconds: 5
          # Wait 5 seconds before starting readiness checks

          periodSeconds: 5
          # Check every 5 seconds

          timeoutSeconds: 2
          # Fail if no response in 2 seconds

          failureThreshold: 3
          # After 3 failures, Pod is removed from Service (no traffic)
```

3️⃣ Startup Probe `helthcheker.yml`

```yaml
# API version used for Deployment
apiVersion: apps/v1

# Defines the Kubernetes resource type
kind: Deployment

# Metadata contains information about the Deployment
metadata:
  # Name of the Deployment
  name: mydeploy

spec:
  # Number of Pod replicas to maintain
  replicas: 3

  # Selector defines which Pods are managed by this Deployment
  selector:
    matchLabels:
      app: myapp

  # Pod template used to create Pods
  template:
    metadata:
      # Labels applied to Pods
      labels:
        app: myapp

    spec:
      containers:
      - name: mycontainer
        # Name of the container

        image: nginx
        # Container image to run

        ports:
        - containerPort: 80
          # Port exposed by the container

        # Command delays nginx startup by 50 seconds
        # Used to simulate a slow-starting application
        command: ["sh", "-c", "sleep 50 && nginx -g 'daemon off;'"]

        # ---------------- STARTUP PROBE ----------------
        startupProbe:
          httpGet:
            path: /index.html
            # Path checked to confirm application has started
            port: 80
            # Port used for startup check

          initialDelaySeconds: 5
          # Wait 5 seconds before starting startup probe

          periodSeconds: 5
          # Check every 5 seconds

          timeoutSeconds: 2
          # Fail if no response within 2 seconds

          failureThreshold: 3
          # After 3 failures, container is restarted
          # Until startup probe succeeds,
          # liveness and readiness probes are disabled

        # ---------------- LIVENESS PROBE ----------------
        livenessProbe:
          httpGet:
            path: /index.html
            # Path used to check if container is alive
            port: 80

          initialDelaySeconds: 5
          # Wait 5 seconds after startup probe succeeds

          periodSeconds: 5
          # Check every 5 seconds

          timeoutSeconds: 2
          # Fail if no response within 2 seconds

          failureThreshold: 3
          # Restart container after 3 consecutive failures

        # ---------------- READINESS PROBE ----------------
        readinessProbe:
          httpGet:
            path: /login.html
            # Path used to check if application is ready for traffic
            port: 80

          initialDelaySeconds: 5
          # Wait 5 seconds before starting readiness checks

          periodSeconds: 5
          # Check every 5 seconds

          timeoutSeconds: 2
          # Fail if no response within 2 seconds

          failureThreshold: 3
          # After 3 failures, Pod is removed from Service
          # (traffic stops, container is NOT restarted)

```


---
---
<a id="example-24"></a>

#  Screenshots

1️⃣ Liveness Probe `helthcheker.yml`



| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20151255.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20151310.png?raw=true) |



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20152406.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---


2️⃣ Readiness Probe  `helthcheker.yml`


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20152526.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



---


3️⃣ Startup Probe `helthcheker.yml`


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20185618.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-01%20185509.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---
---



<a id="example-25"></a>



#  What is service in K8S  & also Diff Between Service and lables


###   🔹 What is a Service in Kubernetes (K8s)?

A Service in Kubernetes is a networking abstraction that provides a stable way to access Pods.


##  Why Service is needed?


- Pods are temporary (IP changes when Pods restart).
- A Service provides:
  - Stable IP
  - Stable DNS name
  - Load balancing across Pods


###   What Service does:

- Selects Pods using labels
- Routes traffic to matching Pods
- Distributes traffic evenly

👉 In short:

>Service = stable network endpoint for Pods

---


###   🔹 What are Labels?

- Labels are key-value pairs attached to Kubernetes objects.
- Used for:
  - Identifying objects
  - Grouping resources
  - Selecting Pods

Example:
```yaml
labels:
  app: myapp
  env: prod
```

👉 Labels do NOT provide networking<br>
They are only identifiers

---

###  🔹 Difference Between Service and Labels



| Feature             | Service             | Labels                    |
| ------------------- | ------------------- | ------------------------- |
| Purpose             | Networking & access | Identification & grouping |
| Provides IP/DNS     | ✅ Yes               | ❌ No                      |
| Load balancing      | ✅ Yes               | ❌ No                      |
| Used to select Pods | ✅ Yes (uses labels) | ✅ Yes                     |
| Standalone resource | ✅ Yes               | ❌ No                      |
| Traffic handling    | ✅ Yes               | ❌ No                      |


###  🔁 Relationship (Very Important)

- Labels tag Pods
- Service uses labels to find Pods
- Traffic flows:

```arduino
Client → Service → Pods (matched by labels)
```

🧠 Easy Memory Trick

- Label → “Who are you?”
- Service → “How can I reach you?”


---
---


<a id="example-26"></a>


#   NameSpace 

[Screenshot's](#example-27)

###  🔹 What is a Namespace in Kubernetes?


A Namespace in Kubernetes is a logical isolation mechanism used to organize and separate resources within the same cluster.

A Namespace is a logical partition inside a Kubernetes cluster.<br>
It is used to divide cluster resources among multiple teams, projects, or environments.

👉 Simple line:<br>
Namespace = virtual cluster inside a Kubernetes cluster

###   🔹 Why do we need Namespaces?

- A single cluster can host:
  - Multiple applications
  - Multiple teams
  - Multiple environments (dev / test / prod)
- Avoids resource name conflicts
- Helps in:
  - Resource isolation
  - Access control (RBAC)
  - Resource limits (CPU, memory)
    
---

###   🔹 Default Namespaces in Kubernetes

When a cluster is created, Kubernetes already has some namespaces:

| Namespace         | Purpose                                         |
| ----------------- | ----------------------------------------------- |
| `default`         | Where resources go if no namespace is specified<br>The four pods running on the master node belong to the `kube-system` namespace |
| `kube-system`     | Kubernetes internal components                  |
| `kube-public`     | Publicly readable data                          |
| `kube-node-lease` | Node heartbeat & health info                    |




>Create a namespace using command too see [click here](#example-100)

###  Second method using Yaml file(Recommended)

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```
Apply:

```
kubectl apply -f namespace.yml
```

###  🔹 Create Resources Inside a Namespace using file (Best Practice)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  namespace: dev
spec:
  containers:
  - name: nginx
    image: nginx
```


---

<a id="example-27"></a>


#   Screenshot's



- Checked existing Kubernetes namespaces using `kubectl get ns`
- Verified system pods running in `kube-system` namespace
- Created a new custom namespace `nikii`
- Deployed an application in `nikii` namespace using deployment YAML
- Verified deployment pods are running in `nikii` namespace
- Created another namespace `misal` for testing purpose
- Created a Pod inside `misal` namespace
- Changed default namespace using `kubectl config set-context` and verified pods




| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-02%20091151.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-02%20091202.png?raw=true) |



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-02%20091231.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---
---

<a id="example-28"></a>


#  Volumes

- [Example or say practicess Schreenshots](#example-29)<br>
- [Difference's](#example-30)

###   📌 What is a Volume?

A Volume in Kubernetes is a directory accessible to containers in a Pod, which persists data beyond the life of a container.

>Key idea: Containers are ephemeral (they die → data gone). Volumes solve this by storing data outside the container filesystem so it survives restarts or can be shared between containers.

---

###  🔹 Why Volumes are Needed

1. Containers are temporary
- If a container crashes or restarts, `/data` inside container disappears.
- Volume keeps your data safe.

2. Sharing data between containers
- Two containers in the same Pod can read/write the same Volume.

3. Persistent storage for apps
- Databases, logs, config files → should survive Pod restart.

---

###  🔹 Types of Volumes (Common ones)

| Volume Type                   | Description                                                                        |
| ----------------------------- | ---------------------------------------------------------------------------------- |
| `emptyDir`                    | Temporary storage; lives as long as Pod lives. Deleted when Pod dies.              |
| `hostPath`                    | Uses a path on the **host node**; Pod can read/write host files.                   |
| `persistentVolumeClaim` (PVC) | Connects to a Persistent Volume (PV) for long-term storage; survives Pod restarts. |
| `configMap`                   | Stores configuration data as files; containers can read them.                      |
| `secret`                      | Stores sensitive info (passwords, keys) securely.                                  |
| `nfs`                         | Network File System; shared storage across nodes.                                  |


###  1️⃣ emptyDir Volume

- What it is: Temporary storage inside a Pod.
- Lifetime: Exists only as long as the Pod is running. Once Pod is deleted, data is lost.
- Use case: Share temporary data between containers in the same Pod (like cache, logs, temp files).
- Example:
```yaml
volumes:
- name: temp-storage
  emptyDir: {}
```
- Mounted inside container at `/data.`

---

###  2️⃣ hostPath Volume

- What it is: Maps a directory from the host node to the Pod.
- Lifetime: Lives as long as Pod + host directory exists.
- Use case: Access node-level files, logs, or config.
- Warning: Tied to a specific node → not portable in multi-node clusters.
- Example:
```yaml
volumes:
- name: host-log
  hostPath:
    path: /var/log
    type: Directory
```

>Used EBS storage

---

###  3️⃣ persistentVolumeClaim (PVC) / PersistentVolume (PV)

- What it is: Persistent storage managed by Kubernetes.
- Lifetime: Survives Pod restarts and deletion.
- Use case: Databases, files, anything that must persist.
- PV and PVC act as an isolation layer between Pods and storage volumes.
- PV and PVC are used so that any backend storage (EBS, NFS, hostPath, etc.) can be changed without modifying the Pod YAML file. The Pod only talks to the PVC, not to the actual storage.
- Example:
```yaml
volumes:
- name: my-storage
  persistentVolumeClaim:
    claimName: my-pvc
```

---

PV & PVC – Access Modes and Capacity Binding

Access Modes define how a Persistent Volume can be mounted by Pods.

Common access modes are:

- ***ReadWriteOnce*** ***(RWO)*** <br>
The volume can be mounted as read-write by only one node at a time.

- ***ReadOnlyMany*** ***(ROX)*** <br>
The volume can be mounted as read-only by multiple nodes.

- ***ReadWriteMany*** ***(RWX)*** <br>
The volume can be mounted as read-write by multiple nodes at the same time.

---

Capacity Binding between PV and PVC


- A Persistent Volume (PV) has a fixed storage capacity (for example, 5Gi, 10Gi).
- A Persistent Volume Claim (PVC) requests a specific amount of storage.
- Kubernetes binds a PVC to a PV only if:
- The PV capacity is greater than or equal to the requested size.
- The PV supports the requested access mode.
- Storage class (if used) matches.

---

How Binding Happens


- When a PVC is created, Kubernetes searches for a matching PV.
- If a suitable PV is found, it is bound one-to-one with the PVC(i.e depends upon the capacity if capacity are equal or greater than equal `And` Accesss Mode ).
- Once bound:
  - That PV cannot be used by any other PVC.
  - The Pod accesses storage only through the PVC.

---



  
---

###  4️⃣ configMap Volume

- What it is: Stores config data as files or env variables or Secrets.
- Lifetime: Exists as long as Pod exists.
- Use case: Provide configuration to apps (like `.properties`, `.yaml` files) without rebuilding the image.
- Example:
```yaml
volumes:
- name: config-volume
  configMap:
    name: my-config
```

---

###  5️⃣ secret Volume

- What it is: Securely stores sensitive data like passwords, keys, tokens.
- Lifetime: Exists as long as Pod exists.
- Use case: Apps needing secure credentials.
- Example:
```yaml
volumes:
- name: secret-volume
  secret:
    secretName: my-secret
```

---
🔑 Types of Secrets

---

1️. Generic Secret


Definition

-  Generic Secret is used to store arbitrary sensitive data such as passwords, tokens, and keys.

Key Points

- Most commonly used Secret type.
- Can store multiple key–value pairs.
- Used by applications for:
  - Database credentials
  - API tokens
  - Application secrets 

---

2️. Docker Registry Secret

Definition

- Docker Registry Secret stores authentication information required to pull images from private container registries.

Key Points

- Used when images are not publicly accessible.
- Contains registry credentials securely.
- Referenced during image pull, not during runtime.

---

3️. TLS Secret

Definition

- TLS Secret stores cryptographic certificates and private keys.

Key Points

- Used for enabling secure (HTTPS) communication.
- Commonly associated with Ingress controllers.
- Contains certificate and private key data.



| Secret Type     | Purpose              | Typical Usage        |
| --------------- | -------------------- | -------------------- |
| Generic         | Store sensitive data | DB passwords, tokens |
| Docker Registry | Image authentication | Private image pull   |
| TLS             | Secure communication | HTTPS, SSL           |



---
---


###   6️⃣ nfs Volume

- What it is: Uses Network File System for shared storage across nodes.
- Lifetime: Independent of Pod; shared across cluster.
- Use case: Multi-node apps that need the same data.
- Example:
```yaml
volumes:
- name: nfs-volume
  nfs:
    server: 10.0.0.1
    path: "/shared-data"
```

>Used EFS storage


---


💡 Tip to remember:

- Temporary → emptyDir
- Host-specific → hostPath
- Persistent → PVC/PV
- Config → configMap
- Sensitive → secret
- Shared → nfs

---

<a id="example-30"></a>

##  Different Between's

| **ConfigMap**                               | **NFS Volume**                                |
| ------------------------------------------- | --------------------------------------------- |
| 1) Used for configuration file              | 1) Used for application data file             |
| eg: `/etc/nginx/conf.d/default.conf`        | eg: `/usr/share/nginx/html/index.html`        |
| 2) Easy to rollback <br> – creates versions | 2) No version history <br> – hard to rollback |
| 3) Read-only access                         | 3) Read & write                               |




| Component / Storage | ConfigMap                     | Secret                        |
| ------------------- | ----------------------------- | ----------------------------- |
| **Stored at**       | Master / ETCD                 | Master / ETCD                 |
| **Type of data**    | Configuration file            | Passwords / sensitive data    |
| **Data format**     | Plain text                    | Base64 encoded                |
| **Purpose**         | Store application config      | Store secrets for apps        |
| **Usage in Pod**    | Environment variable / volume | Environment variable / volume |






| Feature           | ConfigMap                                                      | Secret                                                              |
| ----------------- | -------------------------------------------------------------- | ------------------------------------------------------------------- |
| **Purpose**       | Store non-sensitive configuration data                         | Store sensitive data (passwords, tokens, keys)                      |
| **Data Storage**  | Plain text                                                     | Base64 encoded (not encrypted by default)                           |
| **Use Case**      | Application configuration, environment variables, config files | Passwords, API keys, TLS certificates, Docker credentials           |
| **Type**          | Opaque by default                                              | Opaque, `kubernetes.io/dockerconfigjson`, `kubernetes.io/tls`, etc. |
| **Access Method** | Environment variables, volumes                                 | Environment variables, volumes                                      |
| **Security**      | Not secure; readable by anyone with access to the namespace    | More secure; intended for sensitive data; can be restricted by RBAC |
| **Example**       | DB_HOST, LOG_LEVEL, APP_MODE                                   | MYSQL_ROOT_PASSWORD, .dockerconfigjson, TLS cert/key                |
| **Lifetime**      | Exists as long as the Pod/namespace exists                     | Exists as long as the Pod/namespace exists                          |
| **Use in Pods**   | Mounted as files or environment variables                      | Mounted as files or environment variables                           |
|  master           

---


<a id="example-31"></a>


## Scripts

 2️⃣ hostPath Volume  `hostpath.yml`

```yaml
apiVersion: apps/v1                     # Specifies the API version used for Deployment
kind: Deployment                        # Defines this resource as a Deployment
metadata:                               # Metadata section for the Deployment
  name: mydeploy                        # Name of the Deployment object
spec:                                   # Specification of desired state
  replicas: 3                           # Number of Pod replicas to run
  selector:                             # Selector used to identify Pods managed by this Deployment
    matchLabels:                        # Uses label matching to select Pods
      app: myapp                        # Deployment will manage Pods with label app=myapp
  template:                             # Pod template used to create new Pods
    metadata:                           # Metadata for the Pod
      labels:                           # Labels assigned to the Pod
        app: myapp                      # Label used by Deployment and Service to identify Pod
    spec:                               # Specification of the Pod
      containers:                       # List of containers inside the Pod
      - name: mycontainer               # Name of the container
        image: nginx                    # Docker image used for the container
        ports:                          # Ports exposed by the container
        - containerPort: 80             # Application listens on port 80 inside the container
        volumeMounts:                   # Defines where volumes are mounted inside the container
        - name: myvolume                # Name of the volume to mount  # For Use
          mountPath: /usr/share/nginx/html # Path inside container where volume is attached
      volumes:                          # Declares volumes available to the Pod
      - name: myvolume                  # Name of the volume      # For Create
        hostPath:                       # Uses a directory from the worker node filesystem
          path: /home/ubuntu/bkp        # Directory path on worker node (stored on EBS)
          type: DirectoryOrCreate       # Creates directory if it does not already exist
```
  


  6️⃣ nfs Volume `nfs.yml`

```yaml
apiVersion: apps/v1   # Kubernetes API version for Deployment
kind: Deployment      # Resource type: Deployment (manages pods)
metadata:
  name: mydeploy      # Name of the deployment
spec:
  replicas: 3          # Number of pod replicas to run
  selector:
    matchLabels:
      app: myapp       # Selects pods with label app=myapp
  template:
    metadata:
      labels:
        app: myapp     # Label assigned to pods created by this Deployment
    spec:
      containers:
      - name: mycontainer     # Container name inside pod
        image: nginx          # Container image to use (nginx)
        ports:
        - containerPort: 80   # Port exposed by container
        volumeMounts:         # Mount inside pod
        - name: myefsvolume   # Reference name of the volume
          mountPath: /usr/share/nginx/html   # Path inside container
      volumes:                 # Volume source (outside pod)
      - name: myefsvolume       # Volume name (matches volumeMounts)
        nfs: 
          path: /data           # Path in NFS (EFS) share
          server: fs-0f58d373e2e9cf075.efs.eu-north-1.amazonaws.com  # EFS DNS from AWS
```

4️⃣ configMap Volume `configmap.yml`

```yaml
apiVersion: apps/v1                     # Kubernetes API version for Deployment object
kind: Deployment                        # Resource type is Deployment
metadata:                               # Metadata section (name, labels, etc.)
  name: mydeploy                        # Name of the Deployment

spec:                                   # Specification of the Deployment
  replicas: 3                           # Number of pod replicas to run
  selector:                             # Selector to identify which Pods belong to this Deployment
    matchLabels:                        # Match Pods having these labels
      app: myapp                        # Label key-value used to select Pods

  template:                             # Pod template (how Pods should be created)
    metadata:                           # Metadata for Pods
      labels:                           # Labels applied to Pods
        app: myapp                      # Label used for selection and Service mapping

    spec:                               # Pod specification
      containers:                       # List of containers inside the Pod
      - name: mycontainer               # Name of the container
        image: mysql                    # Docker image used (MySQL image)

        env:                            # Environment variables for the container
        - name: MYSQL_ROOT_PASSWORD     # Environment variable name used by MySQL
          valueFrom:                    # Value will be taken from an external source
            configMapKeyRef:            # Reference to a ConfigMap
              name: mysqlconfig         # Name of the ConfigMap
              key: MYSQL_ROOT_PASSWORD  # Key inside ConfigMap whose value will be injected
```

`nginxconfig.yml`

```yaml
apiVersion: apps/v1                     # Kubernetes API version for Deployment object
kind: Deployment                        # Resource type is Deployment

metadata:                               # Metadata section (name, labels, etc.)
  name: mydeploy                        # Name of the Deployment

spec:                                   # Specification of the Deployment
  replicas: 3                           # Number of pod replicas to run

  selector:                             # Selector to identify which Pods belong to this Deployment
    matchLabels:                        # Match Pods having these labels
      app: myapp                        # Label key-value used to select Pods

  template:                             # Pod template (defines how Pods are created)
    metadata:                           # Metadata for Pods
      labels:                           # Labels applied to Pods
        app: myapp                      # Label used for selection and Service mapping

    spec:                               # Pod specification
      containers:                       # List of containers inside the Pod
      - name: mycontainer               # Name of the container
        image: mysql                    # Docker image used (MySQL image)

        ports:                          # Ports exposed by the container
        - containerPort: 80             # Container listens on port 80

        volumeMounts:                   # Mount volumes inside the container
        - name: myvolume                # Volume name (must match volumes section)
          mountPath: /etc/nginx/conf.d/default.conf  
                                        # Path inside the container where file is mounted
          subPath: default.conf          # Mount only this file, not the entire ConfigMap

      volumes:                          # Volumes available to the Pod
      - name: myvolume                  # Name of the volume
        configMap:                      # Volume type is ConfigMap
          name: nginxconfig             # ConfigMap name containing default.conf
```


5️⃣ Secret Environment Variable `mygenericsecretpod.yml`



```yaml
apiVersion: apps/v1                     # Kubernetes API version for Deployment object
kind: Deployment                        # Resource type is Deployment

metadata:                               # Metadata section
  name: mydeploy                        # Name of the Deployment

spec:                                   # Deployment specification
  replicas: 3                           # Number of Pod replicas to run

  selector:                             # Selector to match Pods with Deployment
    matchLabels:                        # Matching labels
      app: myapp                        # Label used to identify Pods

  template:                             # Pod template
    metadata:                           # Pod metadata
      labels:                           # Labels applied to Pods
        app: myapp                      # Same label as selector

    spec:                               # Pod specification
      containers:                       # List of containers inside the Pod
      - name: mycontainer               # Name of the container
        image: mysql                    # MySQL Docker image

        env:                            # Environment variables for the container
        - name: MYSQL_ROOT_PASSWORD     # Environment variable required by MySQL
          valueFrom:                    # Value is fetched from an external source
            secretKeyRef:               # Reference to a Kubernetes Secret
              name: mysecret            # Name of the Secret
              key: MYSQL_ROOT_PASSWORD  # Key inside the Secret whose value is injected
```

`Docker-regestry-secrete`

```yaml
kubectl create secret docker-registry mydockersecret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<your-username> \
  --docker-password=<token> \
  --docker-email=<your-email>
 ```


---


<a id="example-29"></a>



#  Screenshot's

- [For Script's](#example-31)
  
## Create a hostPath Volume  `hostpath.yml`



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20190357.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


📌 HostPath Volume Verification 

- Created a folder named `bkp` on the worker node.
- Added/modified content inside `bkp/index.html` on the worker node.
- The folder `bkp` is mounted into all Pods using a hostPath volume.
- Executed `kubectl exec` to enter different Pods.
- Ran `curl http://localhost` inside each Pod.
- Same updated content was visible in all Pods, confirming shared storage.
- This proves Pods are reading data directly from the worker node filesystem.
- Any change on the node is immediately reflected inside all Pods.


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20194750.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


🌐 Accessing Application in Browser using Service (NodePort)

- Verified the application output inside the Pod using `curl http://localhost`.
- To access the application externally, a Kubernetes Service is required.
- Created a NodePort Service to expose the Pods outside the cluster.
- The Service selects Pods using matching labels.
- Kubernetes assigned a NodePort automatically.
- The application became accessible using Worker Node Public IP + NodePort.
- Opened a browser and accessed the application via the NodePort URL.
```
http://<WORKER_NODE_PUBLIC_IP>:31488
```
- The same content displayed in the browser confirms Service is working correctly.



| **Terminal**    | **Browser**          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20195426.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20193739.png?raw=true) |

`Service.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysvc
spec:
  selector:
    app: myapp
  type: NodePort
  ports:
    - protocol: TCP
      port: 80 # service port
      targetPort: 80 # pod port
```


---

## Create a NFS Volume `nfs.yml`
  
📦 NFS (EFS) Practical Setup 

- Opened AWS Console and searched for EFS.
- Created a new EFS named `K8S-EFS`.
- Went to Network settings of EFS.
- Allowed required NFS ports (2049) in the Security Group.
- Copied Security Group ID and updated inbound rules in EC2 Security Groups.
- Logged in to the Kubernetes worker node.
- Created a directory for EFS mounting on the node.
- Bypassed local EBS (hostPath) storage to use shared storage.
- Checked whether NFS client was installed.
- Installed NFS utilities if not present.
- Selected K8S-EFS → Attach option in AWS Console.
- Chose Mount via DNS name method.
- Copied the mount command provided by AWS.
- Executed the mount command on the worker node.
- Verified that EFS is successfully mounted and ready for Kubernetes use.



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20200832.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20203448.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20201504.png?raw=true) |



| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20203740.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20203813.png?raw=true) |


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-03%20203754.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>




---

##  Create a configMap Volume `configmap.yml`

  - Created ConfigMap using kubectl command
    - `kubectl create configmap mysqlconfig --from-literal=MYSQL_ROOT_PASSWORD=root`
    - `mysqlconfig`→ Name of the ConfigMap  , `--from-literal`→ Used to directly define key-value data from the command line  ,<br>
       `MYSQL_ROOT_PASSWORD`→ Key name (environment variable required by MySQL container)<br>
       `root`→ Value assigned to the key
   - This ConfigMap stores the MySQL root password securely outside the Pod
   - The ConfigMap value is later injected into the Pod as an environment variable


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20095146.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20095236.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



---

📌 Nginx ConfigMap Creation & Verification

- Created a basic Nginx Pod using `pod.yml`.
- Entered the running Pod using kubectl exec.
- Navigated to /etc/nginx/conf.d/ inside the container.
- Verified the default default.conf used by Nginx.
- Exited the Pod and copied / recreated default.conf on the master node.
- Created a ConfigMap named `nginxconfig` using:
- `kubectl create configmap nginxconfig --from-file=default.conf`
   - `nginxconfig` → Name of the ConfigMap
   - `--from-file` → Used to create ConfigMap data from a file
   - `default.conf` → File name becomes the key in the ConfigMap
   - `File content` → Stored as the value of that key
- Verified ConfigMap creation using kubectl get configmap.
- Checked ConfigMap content using kubectl describe configmap nginxconfig.
- Confirmed that Nginx configuration file is successfully stored inside the ConfigMap.
- This ConfigMap can now be mounted into Pods to override Nginx configuration without rebuilding the image.


>Now that we have created the ConfigMap, the next step is to use it by attaching it to the Pod.  or say<br>
After creating the ConfigMap, the next step is to consume it inside the Pod.


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20105402.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



📌 ConfigMap Usage Verification

- Created a Deployment using nginxconfig.yml.
- Deployment mydeploy was created successfully.
- Kubernetes created 3 Pods automatically via ReplicaSet.
- All Pods are in Running state.
- ConfigMap nginxconfig is attached to the Pods as a volume.
- The file default.conf from ConfigMap is mounted inside the container.
- Mount path used:
`/etc/nginx/conf.d/default.conf`
- Verified using:
  ```
  kubectl describe pod <pod-name>
  ```

- Volume type shown as ConfigMap in Pod details.
- This confirms:
  - Nginx configuration is coming from ConfigMap.
  - Same configuration is shared across all Pods.
- Any change in ConfigMap will update configuration inside Pods (after reload/restart).



<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20105421.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20105529.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



---
---

##  Create a Secret Volume  

###   generic secret 

 📌 Kubernetes Secret Creation

-  Created a Secret named mysecret using kubectl command.
- Used `--from-literal` to store sensitive data directly as key-value.
- Stored ***MYSQL_ROOT_PASSWORD=root*** inside the Secret.
- Verified Secret creation using `kubectl get secret`.
- Viewed Secret details using `kubectl describe secret mysecret`.
- Confirmed Secret type as ***Opaque*** (generic Secret).
- Checked Secret content in YAML format using `kubectl get secret -o yaml`.
- Observed that the value is Base64 encoded (`cm9vdA==`).
- This Secret securely stores sensitive data outside the Pod.
- Too see the encodedcode go to browser and encoded there

>Drawback: At present, even though the password is stored in encrypted (Base64) format, it becomes<br>
>easily readable once accessed. To improve security, Secret volumes will be used in future<br>
>implementations so that sensitive data can be handled more securely during application development.

<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20120802.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20110508.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>

`Concept`: From this point onward, Pods must be created using a Pod definition file. If the root password is<br>
updated (for example, changed in the Secret), the change should automatically reflect in all Pods without recreating them manually.

Therefore, we need to write a Pod definition file to use the Secret properly and ensure that any password change is reflected across all Pods automatically.

📌 Secret Usage Verification (Environment Variable)

- Created a Kubernetes Secret named `mysecret` containing `MYSQL_ROOT_PASSWORD`.
- Wrote a Pod/Deployment YAML file that references the Secret.
- Applied the file using `kubectl apply -f mygenericsecretpod.yml`.
- Kubernetes created a Deployment with 3 Pods automatically.
- Verified Pods using `kubectl get pods`.
- Used `kubectl describe pod <pod-name>` to inspect Pod details.
- Confirmed that:
  - `MYSQL_ROOT_PASSWORD` is injected into the container
  - Value is sourced from Secret `mysecret`
  - Password is not hardcoded in the Pod YAML
- All Pods receive the same password from the Secret.
- If the Secret value is updated and Pods are restarted, all Pods get the new password automatically.

✅ Conclusion:<br>
Secrets provide a secure and centralized way to manage sensitive data like passwords, ensuring consistency across all Pods.




<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20120938.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>


---

###  Docker Regestry-Secret


📌 Docker Registry Secret Verification

- Created a Docker Registry secret to allow Kubernetes to pull private Docker images.
  
Command used:
```yaml
kubectl create secret docker-registry mydockersecret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<your-username> \
  --docker-password=<your-docker-pat> \
  --docker-email=<your-email>
```

- Verified secret creation using:
- `kubectl get secret` → Lists all secrets.
- `kubectl get secret -o yaml` → Shows detailed YAML of all secrets.
- `kubectl get secret mydockersecret -o yaml` → Shows detailed info for the specific secret.

- Secret type: `kubernetes.io/dockerconfigjson` → Used specifically for Docker Registry authentication.
- Data stored inside: Encoded `.dockerconfigjson` containing username, password/token, email, and auth info.
- Use case: Allows Pods to pull images from private Docker registries without exposing credentials in plain text.




| **Browser**    | ****          | ****          | ****          |
|--------------------------------|------------------------------------|------------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20124438.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20124455.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20124554.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20124816.png?raw=true) |




| **Terminal**    | ****          |
|--------------------------------|------------------------------------|
| ![VS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20125518.png?raw=true) | ![AWS](https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20125330.png?raw=true) | 


<p align="center">
  <img src="https://github.com/nikiimisal/Concepts_And_Examples-of-K8S/blob/main/img/Screenshot%202026-02-06%20125400.png?raw=true" width="500" alt="Initialize Repository Screenshot">
</p>



---












##   TLS Secret


📌 TLS Secret

- What it is: Stores SSL/TLS certificates and keys securely for Kubernetes workloads.
- Use case: Used by applications needing HTTPS/secure communication (like Ingress or web servers).
- Current status: Practical implementation not done yet because certificates are not available.
- Future use: Can be added later when the certificates/keys are ready.
- Key point: Exists as long as the Pod that consumes it exists.

---
---

##    create a volume persistentVolumeClaim (PVC) / PersistentVolume (PV) 


















---
---
---


<a id="example-33"></a>


#  Jobs & Cronjobs  

 - [Example or say practicess Schreenshots](#example-32)



###  🔹 Kubernetes Job


What is a Job?

A Job in Kubernetes is used to run a task once or a fixed number of times and ensure it successfully completes.

- It creates one or more Pods
- Retries automatically if a Pod fails
- Stops once the task is completed

Use cases

- Database migration
- Backup task
- Report generation
- One-time scripts

---

###   🔹 Kubernetes CronJob

What is a CronJob?

A CronJob runs Jobs on a schedule, just like Linux cron.
- Executes Jobs at a specific time or interval
- Useful for recurring tasks


Use cases

- Daily backups
- Log cleanup
- Periodic health checks
- Scheduled reports

---

###   🔹 Difference (Conceptual)

- Job → Runs once or until completion
- CronJob → Runs Jobs repeatedly on a schedule

---

##  🔹 Important Job Terms


###  1️⃣ Completions


What it means:<br>
Number of successful Pod executions required for the Job to be considered complete.
- If `completions: 1` → Job finishes after 1 successful Pod
- If `completions: 5` → Job finishes after 5 successful Pods
Example meaning:<br>
“Run this task successfully 5 times.”


###    2️⃣ Parallelism

What it means:<br>
Number of Pods that can run at the same time.
- Controls concurrency
- Helps finish Jobs faster
Example meaning:<br>
“Run 2 Pods at the same time.”

---

###  🔹 How Completions & Parallelism Work Together

- ***Completions*** = How many times the task must succeed
- ***Parallelism*** = How many Pods can run simultaneously
Simple Example
- `completions: 6`
- `parallelism: 2`

➡️ Kubernetes will run 2 Pods at a time<br>
➡️ Total 6 successful runs needed<br>
➡️ Job completes after all succeed

---

🧩 Kubernetes Job & CronJob  Flowchart

```code
┌───────────────────────────┐        ┌────────────────────────────────┐
│           JOB             │        │            CRONJOB             │
├───────────────────────────┤        ├────────────────────────────────┤
│                           │        │                                │
│   Job Created             │        │   Cron Schedule (Time)         │
│        ↓                  │        │        ↓                       │
│   Pod Created             │        │   Job Created                  │
│        ↓                  │        │        ↓                       │
│   Task Executed           │        │   Pod Created                  │
│        ↓                  │        │        ↓                       │
│   Task Completed ?        │        │   Task Executed                │
│     ├─ No → Retry Pod     │        │        ↓                       │
│     └─ Yes                │        │   Task Completed               │
│        ↓                  │        │        ↓                       │
│   Job Completed           │        │   Pod Deleted                  │
│        ↓                  │        │        ↓                       │
│   Pod Deleted             │        │   Wait for Next Schedule       │
│        ↓                  │        │        ↓                       │
│       END                 │        │   Repeat Cycle                 │
│                           │        │                                │
└───────────────────────────┘        └────────────────────────────────┘

```

🔁 Job vs CronJob (One-Line Visual Logic)

```code
Job      : Create → Run Task → Delete (ONE TIME)

CronJob  : Create → Run → Delete
           ↓
         Wait
           ↓
         Create → Run → Delete (REPEATED)
```

🔑 Key Idea (Job)

- A Job runs only once in Kubernetes.
- It creates a Pod to perform a specific task.
- After the task is completed, the Pod is terminated.
- If the task fails, the Job retries by creating a new Pod until it succeeds (based on restart policy).


⏰ Key Idea (CronJob)

- A CronJob is a Job with a time schedule.
- It creates a new Job at every scheduled time.
- Each Job creates its own new Pod to run the task.
- Old Pods finish and exit, and new Pods are created for the next schedule.



---
---


<a id="example-32"></a>


#  Screenshot's




