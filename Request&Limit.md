# Kubernetes Requests and Limits

## What's the Deal with Requests & Limits?

Think of your Kubernetes cluster as a bustling city and pods as tenants in an apartment building. Each tenant (pod) requires specific resources like CPU and memory to function:

- **Requests**: This is the minimum amount of resources a pod needs to operate smoothly. Think of it as a guaranteed reservation for the pod.
- **Limits**: This is the maximum amount of resources a pod can use. It acts as a safety cap to prevent any pod from consuming more than its fair share and disrupting others.


## Why are Requests & Limits Important?

- **Resource Control:** By setting limits, you prevent a single pod from monopolizing resources, which can lead to issues like out-of-memory (OOM) kills or CPU starvation. ☠️ Why OOM can be a good thing? Because it kills the pod; otherwise, the container would have consumed all the memory and could kill the node. Killing the pod is a better option than killing a node itself.
- **Predictability:** Requests help the scheduler allocate resources efficiently and ensure pods have the necessary resources to run effectively.



## Practical 

metric-server.yaml : https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day16/metrics-server.yaml
```
kubectl top node

NAME                               CPU(cores)   CPU(%)   MEMORY(bytes)   MEMORY(%)   
multi-node-cluster-control-plane   347m         4%       758Mi           19%         
multi-node-cluster-worker          80m          1%       246Mi           6%          
multi-node-cluster-worker2         57m          0%       192Mi           4%
```
1. **Exceeding Available Memory:**
   - A pod requesting more memory than is available will be killed due to an OOM (Out of Memory) error.
```
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-2
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-2-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "50Mi"
      limits:
        memory: "100Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
```

```kubectl create ns mem-example```
kubectl apply -f exceeding-pod.yaml
kubectl get pods -n mem-example
NAME            READY   STATUS      RESTARTS   AGE
memory-demo-2   0/1     OOMKilled   0          14s


2. The Below pod will be scheduled
```
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "100Mi"
      limits:
        memory: "200Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]

```
```
kubectl apply -f pod.yaml


kubectl get pod -n mem-example
NAME            READY   STATUS      RESTARTS      AGE
memory-demo     1/1     Running     0             24s
```

    




