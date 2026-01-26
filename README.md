
<h1 align="center">Concepts And Examples of K8S</h1>

---

- [Pod](#example-0)
  
 Types of Pods  :
    - [Pod](#example-1) ,   - [Replication of Pod](#example-2) ,   - [ReplicaSet](#example-3) ,     - [Deployment](#example-4)  ,   - [Static Pod](#example-5)  ,   - [Job](#example-6)  , - [CronJob](#example-7) ,  - [DaemonSet](#example-8)  
 
- [Service](#example-9)
  
Types of Kubernetes services  :
     - [ClusterIP](#example-10) ,  - [NodePort](#example-11) ,    - [LoadBalancer](#example-12)  ,  - [ExternalName](#example-13)
  

-  [Ways to create kubernates](#example-14)


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



#   Example


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

kubectl get pods                                      #  To check whether a Pod has been created or not, use the following command
kubectl get pod
kubectl get po

kubectl get pods -n <namespace-name>                  #  To view Pods in a specific namespace


kubectl describe pod <pod-name>                       # To see detailed information about a Pod

kubectl get pods -o wide                              #  To view Pod status with node and IP details

kubectl logs <pod-name>                                #  To check Pod logs

# Delete Pod:
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






