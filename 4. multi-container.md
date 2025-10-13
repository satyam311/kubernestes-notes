# Multi Container Pod Kubernetes - Sidecar vs Init Container

A Pod is the smallest deployable unit in Kubernetes.
Normally, a Pod runs a single container (like one nginx or one redis).

But sometimes, you need multiple containers inside the same Pod — that’s called a multi-container Pod.

**All containers in the same Pod:**
 - Run on the same Node
 - Share the same network namespace → same IP, same ports
 - Can share storage volumes
 - Can communicate via localhost


There can be two pateerns : 

**sidecar container :** Helper container adds functionality (logging, monitoring, proxy, etc.) 

**init container :** Special container that runs before main containers start 

init container : 

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels: 
    app.kubernetes.io/env: prod
spec:
  containers: 
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c','echo the app is running && sleep 3600']
    env: 
    - name: FIRSTNAME
      value: "satyam"
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh','-c']
    args: ['until nslookup myservice.default.svc.cluster.local; do echo waiting for the service to get started; sleep 2;done']

```
kubectl create -f pods.yaml

```
NAME        READY   STATUS     RESTARTS   AGE
myapp-pod   0/1     Init:0/1   0          3m24s

```

```kubectl describe pod  myapp-pod```

**Imperative way to create a deployment**

``` kubectl expose deployment myapp-deploy --name=myapp-service --port=80```

**Way to place a service infront if the deployment inperatively**:

```cluster ip : kubectl expose deployment myapp-deploy --name=myservice --port=80```

```kubectl logs  myapp-pod```
```kubectl logs pod/myapp-pod -c init-myservice```

later on, we can print the enviroments variabke as well :

```kubectl exec -it myapp-pod -- printenv

kubectl exec -it myapp-pod -- sh 

/ # echo $FIRSTNAME
satyam
```

#

### Example 2 : Apply more init containers, create deployments and services in declarative way








