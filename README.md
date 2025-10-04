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
```



















