# Namespaces

A Namespace is a logical partition within a Kubernetes cluster that helps you organize and isolate resources.

**default** -> Where resources go if no namespace is specified
**kube-system**	-> System resources (like DNS, kube-proxy, etc.)
**kube-public**	-> Public resources readable by all (rarely used)
**kube-node-lease**	-> Tracks node heartbeats


### COMMANDS 

```kubectl get ns```

NAME                 STATUS   AGE
default              Active   2d10h
kube-node-lease      Active   2d10h
kube-public          Active   2d10h
kube-system          Active   2d10h
local-path-storage   Active   2d10h


```kubectl  get all --namespace=kube-system```\
```kubectl get all -n kube-system```

 #### how to create own namespace

**Declarative Approach:**

```vim ns.yaml```

```
apiVersion: v1
kind : Namespace
metadata:
  name : demo-ns
```

```kubectl create -f ns.yaml```

**Imperative Approach**

```kubectl create ns demo```

#### Can Pods talk to each other created in the different Namespaces ? 


<img width="2058" height="960" alt="image" src="https://github.com/user-attachments/assets/160066fc-d8f0-4f01-a68d-88b8233d44ae" />


YES ! they can talk to each other using curl <ip_address> 
Ip address of other pod can fetched using ```kubectl get pods -o wide```

**Issue** : what is pods get failed and recreated again due to deployment then ip address get changed and we need to check the ip address every time.

Service Comes into the picture, we will exposing a service in front ofthe deployment.

<img width="1024" height="535" alt="image" src="https://github.com/user-attachments/assets/55c1960f-5bee-47cf-ab10-66611839f3b9" />
















