# kubernestes-notes

### How to set up a local Kubernetes cluster ? 
This can be using minikube and Kind to run a single-node Kubernetes cluster locally 

**Minikube**
- Designed to run a single-node Kubernetes cluster locally on your laptop/PC.

- Great for developers testing Kubernetes applications in an environment that feels close to production.

- Provides addons (like Ingress, Dashboard, metrics-server).


**Kind (Kubernetes in Docker)**

- Designed mainly for testing Kubernetes itself (e.g., CI/CD pipelines, k8s contributors).

- Spins up Kubernetes clusters inside Docker containers (not VMs).

- Lightweight, fast, great for multi-cluster setups


I will be practicing the things using KIND. 

Installing KIND on the Local maching : https://kind.sigs.k8s.io/docs/user/quick-start/#installing-with-a-package-manager

On macOS via Homebrew: ```brew install kind```

### Creating a Cluster

Creating a Kubernetes cluster is as simple as ```kind create cluster```

This will bootstrap a Kubernetes cluster using a pre-built node image.
Prebuilt images are hosted atkindest/node, but to find images suitable for a given release currently you should check the release notes for your given kind version (check with kind version) where you’ll find a complete listing of images created for a kind release.
release note : https://github.com/kubernetes-sigs/kind/releases 

To specify another image use the ```--image``` flag –> ```kind create cluster --image=...```.\
By default, the cluster will be given the name kind. Use the ```--name``` flag to assign the cluster a different context name.


Sample code :

```kind create cluster --image=kindest/node:v1.33.4@sha256:25a6018e48dfcaee478f4a59af81157a437f15e6e140bf103f85a2e7cd0cbbf2 --name cka-cluster1```

### To List Down the kind clusters 

```kind get clusters```

### In order to interact with a specific cluster, you only need to specify the cluster name as a context in kubectl: 

```kubectl cluster-info --context cka-cluster1```\
```kubectl cluster-info --context cka-cluster2```

Before using kubectl, we need to install this first : https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/.

**kubectl**  is the command-line tool for interacting with a Kubernetes cluster. It lets you communicate with the Kubernetes API server to manage and inspect cluster resources.

```
satyammishra@Satyams-MacBook-Air ~ % kubectl get nodes
NAME                               STATUS   ROLES           AGE   VERSION
cka-cluster1-control-plane   Ready    control-plane   30s   v1.33.4
```

## How to Create multi-node Cluster ? 

doc : https://kind.sigs.k8s.io/docs/user/quick-start/#advanced

```satyammishra@Satyams-MacBook-Air ~ % touch  kind-example-config.yaml```

```satyammishra@Satyams-MacBook-Air ~ % vim kind-example-config.yaml```

```
\# a cluster with 3 control-plane nodes and 3 workers
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker
```

```satyammishra@Satyams-MacBook-Air ~ % kind create cluster --config kind-example-config.yaml --name=multi-node-cluster-demo```

```satyammishra@Satyams-MacBook-Air ~ % kubectl cluster-info --context kind-multi-node-cluster-demo```

```satyammishra@Satyams-MacBook-Air ~ % kubectl get nodes```

```
NAME                                     STATUS   ROLES           AGE     VERSION
multi-node-cluster-demo-control-plane    Ready    control-plane   8m11s   v1.34.0
multi-node-cluster-demo-control-plane2   Ready    control-plane   7m32s   v1.34.0
multi-node-cluster-demo-control-plane3   Ready    control-plane   6m53s   v1.34.0
multi-node-cluster-demo-worker           Ready    <none>          5m41s   v1.34.0
multi-node-cluster-demo-worker2          Ready    <none>          5m42s   v1.34.0
multi-node-cluster-demo-worker3          Ready    <none>          5m41s   v1.34.0
```

now the questions why this returning currently created cluster nodes ? 

kubernetes cheetsheeet - https://kubernetes.io/docs/reference/kubectl/quick-reference/ must be handy.

To see which Kubernetes cluster kubectl communicates with and modifies configuration information.

**get all context names** :  ```kubectl config get-contexts``` ( current context is indicated by * ) 

**get all context names** : ```kubectl config get-contexts -o name```

**display the current-context** : ```kubectl config current-context```

**set the default context to my-cluster-name** : ```kubectl config use-context my-cluster-name```

**set a cluster entry in the kubeconfig** : ```kubectl config set-cluster my-cluster-name```


# Pod In Kubernetes 

There are two ways to create pod in the k8s : 

**imperative way** : 
kubectl run nginx-pod --image=nginx:latest
kubectl get pods
kubectl get pods -o wide 
kubectl describe pods nginx-pod


**Declarative ways:**
```
satyammishra@Satyams-MacBook-Air ~ % touch pod.yaml
satyammishra@Satyams-MacBook-Air ~ % vim pod.yaml 

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-declarative
  labels:
    env: prod
    type: frondend
spec:
  containers:
   - name: nginx-container
     image: nginx
     ports:
      - containerPort: 80


satyammishra@Satyams-MacBook-Air ~ % kubectl create -f pod.yaml 
satyammishra@Satyams-MacBook-Air ~ % kubectl get pods
satyammishra@Satyams-MacBook-Air ~ % kubectl edit pod nginx-pod ## doing this the pod config get updated and aplied automatically.
satyammishra@Satyams-MacBook-Air ~ % kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml

kubectl explain pods ( this can be useful to see the apiVersion and kind of pods) 
```

# ReplicatiController vs replicaSet vs Deployement in Kubernetes


### ReplicationController

kubectl explain rc 

what is the template to create a rc.yaml ? 

```
apiVersion: v1
kind : ReplicationController
metadata: 
  name:
  label:
    version:
    env:
spec: 
  template:
    replicas:
    selector: 
    metadata:
      < container data>
    spec: 
      <containeer data>
```

**example :**

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
  labels:
    env: prod
    version: v1
spec:
  replicas: 3
  selector:
    env: prod
    version: v1
  template:
    metadata:
      labels:
        env: prod
        version: v1
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        ports:
        - containerPort: 80
```
To create ReplicationController : ```kubectl create -f rc.yaml```

To list down the ReplicationController : ```kubectl get rc ```
```
NAME       DESIRED   CURRENT   READY   AGE
nginx-rc   3         3         1       12s
```

To list down the pods created by ReplicationController: ```kubectl get pods ```
```
NAME             READY   STATUS    RESTARTS   AGE
nginx-rc-r8dfk   1/1     Running   0          28s
nginx-rc-vnt4s   1/1     Running   0          28s
nginx-rc-xp597   1/1     Running   0          28s
```
### Difference Between ReplicationController and ReplicaSet

**ReplicationController (RC)**

- Older way to ensure a specified number of identical pods are running.
- If a pod goes down, it creates a new one.
- Uses equality-based selectors (e.g., env=prod) to manage pods.

**ReplicaSet (RS)**

- The newer and recommended replacement for ReplicationController.
- Does everything RC does plus more.
- Uses set-based selectors (e.g., env in (prod, qa)), which are more powerful and flexible.
- Usually not created directly — you use Deployment, which internally manages a ReplicaSet.

**Difference in plain words:**

ReplicationController: “I take care of my own pods (based on simple equality labels).”\
ReplicaSet: “I take care of any pod that matches my selector, no matter who created it.”\
That’s why ReplicaSet is more powerful and became the replacement for RC.

### ReplicaSet 

In case of ReplicaSet we add MatchLabels key in the yaml file and we also check the apiVersion but doing ```kubectl explain rs```

example : 
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    env: prod
    version: v1
spec:
  replicas: 3
  selector:
    matchLabels:
      env: prod
      version: v1
  template:
    metadata:
      labels:
        env: prod
        version: v1
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        ports:
        - containerPort: 80

```

```kubectl apply -f rs.yaml```

```kubectl get rs```

```
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       5m47s
```

Delete the replicaSet :  ```kubectl delete rs/ngnix-rs```

Editing the replicaSet on the go ( ex: changing the replicas ) : ```kubectl eidt rs/nginx-rs```

how to increase replicas using imperative command : ```kubectl scale --replicas=10 rs/nginx-rs```


**Deployement**


Both Replicaset and Deployment ensures that the desired number of the replicas will be running but deployment gives more flexibility and power.

Easiness: Deployments are easy to manage, you just need to declare the spec and it will manage everything for you like replicaet and pods.

Version Control : Deployement Store Rollback History and if needed you can rollback to previous version

Rolling Update: In case of replicaset, when updating the image image, it will delete all the pods and spin new pods due to which there could be downtime. but in case of deployement, pods are updated gradually ( one at time ) due to which there is no downtime.


```kubectl explain deployment```

example of yaml : 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    env: prod
    version: v1
spec:
  replicas: 3
  selector:
    matchLabels:
      env: prod
      version: v1
  template:
    metadata:
      labels:
        env: prod
        version: v1
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        ports:
        - containerPort: 80
```

```
satyammishra@Satyams-MacBook-Air ~ % kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-67b9b76749-42qw5   1/1     Running   0          3m10s
pod/nginx-deployment-67b9b76749-jjz55   1/1     Running   0          3m10s
pod/nginx-deployment-67b9b76749-vckh9   1/1     Running   0          3m10s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   21h

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     3            3           3m10s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-67b9b76749   3         3         3       3m10s

```

### How to update the life object like the updating the version of nginx container.

```
satyammishra@Satyams-MacBook-Air ~ % kubectl set image deploy/nginx-deployment \
nginx=nginx:1.28.0-alpine

```
If we do ```kubectl describe deployment```
- we can notice few things that
   - The version is updated but Here, the yaml want change since we have updated the live object.
     
   - RollingUpdateStrategy:  25% max unavailable, 25% max surge
      By default, a Deployment in Kubernetes uses the RollingUpdate strategy with two important settings:

      maxUnavailable = 25%
      → At most 25% of the desired replicas can be unavailable during the update.
      For 3 replicas → 25% of 3 = 0.75 → rounded down to 1 pod.
      So K8s makes sure at least 2 pods are always running.
      
      maxSurge = 25%
      → K8s can temporarily create up to 25% more than desired replicas during the rollout.
      For 3 replicas → 25% of 3 = 0.75 → rounded up to 1 pod.
      So at most 4 pods total can exist during the rollout.

    - ```kubectl rollout history deploy/nginx-deployment```
      ```
      REVISION  CHANGE-CAUSE
      1         <none>
      2         <none>
      ```

    - ```kubectl rollout undo deploy/nginx-deployment```
      
 ### Creating our deployment yaml file using dry run

 ```kubectl create deploy deploy/deploy-new --image=nginx --dry-run=client -o yaml > deploy.yaml```

 

 
      
      









    

























