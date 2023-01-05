### getting started with Namespaces in K8s 

<img src="ns.png">

### listing namespaces 

```
[ashu@ip-172-31-87-240 ashu-apps]$ kubectl  get  namespaces 
NAME              STATUS   AGE
default           Active   20h
kube-node-lease   Active   20h
kube-public       Active   20h
kube-system       Active   20h
[ashu@ip-172-31-87-240 ashu-apps]$ kubectl   get  pods
No resources found in default namespace.
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ whoami
ashu
[ashu@ip-172-31-87-240 ashu-apps]$
```
### creating ns and set as default 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  create  namespace  ashu-project --dry-run=client -o yaml 
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: ashu-project
spec: {}
status: {}
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  create  namespace  ashu-project 
namespace/ashu-project created
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get  ns
NAME              STATUS   AGE
ashu-project      Active   4s
default           Active   20h
kube-node-lease   Active   20h
kube-public       Active   20h
kube-system       Active   20h
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  config set-context --current --namespace=ashu-project 
Context "kubernetes-admin@kubernetes" modified.
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get  pods
No resources found in ashu-project namespace.
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

### checking default ns 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  config  get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-project
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

### doing the same in personal namespace 

```
[ashu@ip-172-31-87-240 k8s-resources]$ ls
ashupod1.yaml  autopod.json  autopod.yaml  mysecret.yaml  ocrpod.yaml
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl apply -f mysecret.yaml  -f  ocrpod.yaml 
secret/ashu-reg-secret created
pod/ashunewpod created
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get  secret 
NAME                  TYPE                                  DATA   AGE
ashu-reg-secret       kubernetes.io/dockerconfigjson        1      10s
default-token-xnpnd   kubernetes.io/service-account-token   3      4m18s
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get  po
NAME         READY   STATUS    RESTARTS   AGE
ashunewpod   1/1     Running   0          14s
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl delete -f mysecret.yaml  -f ocrpod.yaml 
secret "ashu-reg-secret" deleted
pod "ashunewpod" deleted
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

## Introduction to k8s controllers 

<img src="cont1.png">

### stateless vs statefull apps in k8s 

<img src="st.png">

### creating deployment yaml 

```
kubectl  create  deployment  ashu-deploy1  --image=docker.io/dockerashu/oraclejava:webappv1  --port  8080 --dry-run=client -o yaml >mydeploy.yaml
```

### YAML 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-deploy1
  name: ashu-deploy1 # name of deployment 
spec:
  replicas: 1 # number of pod we want 
  selector:
    matchLabels:
      app: ashu-deploy1
  strategy: {}
  template: # to create pods deployment will be using template of pod
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-deploy1
    spec:
      containers:
      - image: docker.io/dockerashu/oraclejava:webappv1
        name: oraclejava
        ports:
        - containerPort: 8080
        resources: {}
status: {}

```

### deploy it 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl apply -f mydeploy.yaml 
deployment.apps/ashu-deploy1 created
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get  deployment 
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
ashu-deploy1   1/1     1            1           6s
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get pods
NAME                           READY   STATUS    RESTARTS   AGE
ashu-deploy1-7d7d754b6-9lhfw   1/1     Running   0          17s
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl delete pod ashu-deploy1-7d7d754b6-9lhfw
pod "ashu-deploy1-7d7d754b6-9lhfw" deleted
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get pods
NAME                           READY   STATUS    RESTARTS   AGE
ashu-deploy1-7d7d754b6-wvkbq   1/1     Running   0          4s
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

### using secret in deployment file 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-deploy1
  name: ashu-deploy1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-deploy1
  strategy: {}
  template: # this is of pod 
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-deploy1
    spec:
      imagePullSecrets:
      - name: ashu-reg-secret
      containers:
      - image: phx.ocir.io/ax8yv0ztaclr/newapp:v1
        name: newapp
        ports:
        - containerPort: 8080
        resources: {}
status: {}

```
