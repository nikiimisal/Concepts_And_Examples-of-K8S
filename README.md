
<h1 align="center">ConceptsAnd Examples of K8S</h1>

---

- [Pod](#example-0)
  -  




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


###  2️⃣ Replication of Pod


- Means running multiple copies of the same Pod
- Used for high availability and load balancing
- If one Pod fails, another one continues serving traffic

⚠️ Kubernetes does not do pod replication directly<br>
👉 It is handled by ReplicaSet / Deployment


---

###  3️⃣ ReplicaSet

- Ensures a fixed number of Pods are always running
- If a Pod crashes or is deleted, ReplicaSet creates a new Pod

Example:<br>
Maintain exactly 3 Pods at all times

---

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

###  6️⃣ Job 


-  Used for one-time tasks & then self destracted  itself
-  Pod runs, completes the task, and then stops

Example:

- Database migration
- One-time data processing  

---

###  7️⃣ CronJob


- Used for scheduled jobs (tasks)
- Runs Jobs at a specific time or interval

Example:

- Daily database backup at 12 AM
- Log cleanup every Sunday  

---


###   8️⃣ DaemonSet

- Ensures one Pod runs on every node ( monetering pod )
- When a new node joins, a Pod is automatically created


Example:

- Log collectors (Fluentd)
- Monitoring agents

---
---


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

###  1️⃣ ClusterIP (Default)


- Communication between inside node pods
- Exposes Pods inside the cluster only
- Not accessible from outside
  
📌 Example:
- Backend app
- Database service

---

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

###   3️⃣ LoadBalancer

- Balance out the load between nodes
- Creates an external load balancer (Cloud-based)
- Gives a public IP

📌 Example:
- Production web applications (AWS, Azure, GCP)

---

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

#  Ways to create kubernates 

### 1️⃣ Minikube

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






