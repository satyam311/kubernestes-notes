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













