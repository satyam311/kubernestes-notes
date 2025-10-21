### ConfigMap in K8s

declaring key value pair in the env ( only take one key value pair ) 
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    env: prod
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    env: 
    - name: FIRSTNAME
      value:  "satyam"
    command: ['sh','-c','echo The app is running ! && sleep 3600']

```
```
kubectl apply -f pod.yaml
kubectl get po  
kubectl describe pod myapp-pod
Environment:
      FIRSTNAME:  satyam

```

---

**How to imperatively create a configMap object** 

```
kubectl create cm cm-app --from-literal=firstname=satyam \ 
> --from-literal-lastname=mishra \

kubectl get cm
NAME               DATA   AGE
cm-app             2      10s
kube-root-ca.crt   1      47h
```

**how to attach the configMap to the pod:**

Sometimes a Pod won't require access to all the values in a ConfigMap. For example, you could have another Pod which only uses the username value from the ConfigMap. For this use case, you can use the env.valueFrom syntax.
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod-02
  labels:
    env: prod
spec:
  containers:
  - name: myapp-container-02
    image: busybox:1.28
    env:
    - name: CONFIGMAP_USERNAME
      valueFrom:
        configMapKeyRef:
          name: cm-app
          key: firstname
    command: ['sh','-c','echo The app is running ! && sleep 3600']


kubectl exec -it myapp-pod-02 -- sh
/ # echo $CONFIGMAP_USERNAME
satyam
```

--- 

The following Pod consumes the content of the ConfigMap as environment variables:

The envFrom field instructs Kubernetes to create environment variables from the sources nested within it. The inner configMapRef refers to a ConfigMap by its name and selects all its key-value pairs.

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod-03
  labels:
    env: prod
spec:
  containers:
  - name: myapp-container-02
    image: busybox:1.28
    envFrom:
    - configMapRef:
        name: cm-app
    command: ['sh','-c','echo The app is running ! && sleep 3600']

kubectl get pods

kubectl exec -it myapp-pod-03 -- sh

/ # echo $firstname
satyam
/ # echo $lastname
mishra
```
--- 
### Some Advance Concept

Doc : https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/

Create a file in the current directory : 

vim app-config.properties

APP_ENV=staging
DB_HOST=postgres-service
DB_PORT=5432

kubectl create configmap game-config --from-file=app-config.properties

kubectl get configmaps prod-config -o yaml

```
apiVersion: v1
data:
  app-config.properties: |+
    APP_ENV=staging
    DB_HOST=postgres-service
    DB_PORT=5432

kind: ConfigMap
metadata:
  creationTimestamp: "2025-10-16T07:54:58Z"
  name: prod-config
  namespace: default
  resourceVersion: "79657"
  uid: f656b60f-5599-4c12-a079-32c2eddf657c

```

we can also use --dry-run=client to copy the configMap object to a file : 
```

kubectl create cm run-config --from-literal=env=p2 \
--from-literal=access=root\
--from-literal=productline=event --dry-run=client -o yaml > cm.yaml

```



