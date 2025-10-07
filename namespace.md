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





