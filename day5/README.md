### getting started 

### Revision 

<img src="rev.png">

### target for the day 

<img src="target.png">

### to add new node in running cluster 

### do all the pre-requiste in new machine 

```
  1  vim /etc/fstab 
    2  swapoff  -a
    3  free -m
    4  setenforce  0
    5  sed -i 's/SELINUX=enforcing/SELINUX=disabled/'  /etc/selinux/config
    6  modprobe br_netfilter
    7  echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
    8  yum install docker -y
    9  mkdir  /etc/docker
   10  cat  <<X  >/etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}

X

   11  systemctl enable --now docker 
   12  history 
   13  cat  <<EOF  >/etc/yum.repos.d/kube.repo
[kube]
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
gpgcheck=0
EOF

   14  yum install kubeadm-1.23* kubelet-1.23* 
   15  systemctl enable --now kubelet 
```

### print token in control plane 

```
kubeadm token create --print-join-command 
```

### output you can paste in new node 

```
 kubeadm join 10.0.0.3:6443 --toke
```

### Ingress in k8s 

<img src="ingress.png">

## Nodepool in cloud controlled k8s -- OKE 

<img src="oke1.png">

### demo of external LB in OKE 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ ls
ns.yaml
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl create deployment ashu-dep1 --image=docker.io/dockerashu/webui:oraclev1 --port 80 --namespace=ashu-app --dry-run=client -o yaml >deploy.yaml
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl apply -f deploy.yaml 
deployment.apps/ashu-dep1 created
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get deploy 
No resources found in default namespace.
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get deploy  -n ashu-app
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep1   1/1     1            1           16s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 

```

### creating service 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl  get deploy -n ashu-app
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep1   1/1     1            1           2m21s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl expose deploy ashu-dep1 --type NodePort --port 80 --name ashulb1 --namespace=ashu-app --dry-run=client -o yaml >np.yaml 
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ ls
deploy.yaml  np.yaml  ns.yaml
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl apply -f np.yaml 
service/ashulb1 created
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get svc -n ashu-app
NAME      TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
ashulb1   NodePort   10.96.42.240   <none>        80:31750/TCP   8s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 


```

### creating Lb type service 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl expose deploy ashu-dep1 --type LoadBalancer --port 80 --name ashulb2 --namespace=ashu-app --dry-run=client -o yaml >lb.yaml 
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl apply -f lb.yaml 
service/ashulb2 created
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get svc -n ashu-app
NAME      TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
ashulb2   LoadBalancer   10.96.139.192   <pending>     80:32239/TCP   9s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get svc -n ashu-app
NAME      TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
ashulb2   LoadBalancer   10.96.139.192   129.153.123.215   80:32239/TCP   56s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 
```

### Nginx ingress controller deploy 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get ns
NAME              STATUS   AGE
anand-app         Active   29m
ankita-ns         Active   29m
ashu-app          Active   31m
default           Active   76m
ingress-nginx     Active   15s
kube-node-lease   Active   76m
```

### Only external Lb we need is for Ingress 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl  get deploy -n ingress-nginx
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
ingress-nginx-controller   1/1     1            1           94s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl  get po  -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-q57pp        0/1     Completed   0          106s
ingress-nginx-admission-patch-jpdcr         0/1     Completed   1          106s
ingress-nginx-controller-7d5fb757db-f2ctk   1/1     Running     0          106s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl  get svc  -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.96.23.252   129.153.87.217   80:32338/TCP,443:30631/TCP   113s
ingress-nginx-controller-admission   ClusterIP      10.96.182.80   <none>           443/TCP                      113s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 

```

### creating deployment and scale it 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get deploy -n ashu-app
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep1   1/1     1            1           32m
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl -n ashu-app scale deploy ashu-dep1 --replicas=3
deployment.apps/ashu-dep1 scaled
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get deploy -n ashu-app
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep1   3/3     3            3           32m
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get po -o wide -n ashu-app
NAME                         READY   STATUS    RESTARTS   AGE   IP             NODE          NOMINATED NODE   READINESS GATES
ashu-dep1-6548d55bd7-7v45x   1/1     Running   0          16s   10.244.0.135   10.0.10.239   <none>           <none>
ashu-dep1-6548d55bd7-t6cnt   1/1     Running   0          33m   10.244.1.3     10.0.10.90    <none>           <none>
ashu-dep1-6548d55bd7-z25v5   1/1     Running   0          17s   10.244.0.7     10.0.10.46    <none>           <none>
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get no
NAME          STATUS   ROLES   AGE   VERSION
10.0.10.239   Ready    node    50m   v1.23.4
10.0.10.46    Ready    node    50m   v1.23.4
10.0.10.90    Ready    node    50m   v1.23.4
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 
```

### creating clusterIP service 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get deploy -n ashu-app
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep1   3/3     3            3           34m
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl expose deploy ashu-dep1 --type ClusterIP --port 80 --name ashulb007 --namespace ashu-app --dry-run=client -o yaml >clustrip.yaml 
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl apply -f clustrip.yaml 
service/ashulb007 created
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get svc -n ashu-app
NAME        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
ashulb007   ClusterIP   10.96.248.182   <none>        80/TCP    9s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 
```


### creating ingress rule 

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ashu-app-ingress-rule # name of ingress rule 
  namespace: ashu-app # adding namespace
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx # change class name to nginx 
  rules:
  - host: japur.ashutoshh.in # my app domain name 
    http:
      paths:
      - path: / # home page of app 
        pathType: Prefix
        backend:
          service:
            name: ashulb007
            port:
              number: 80

```

### create it 

```
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ vim  ingress-rule.yaml 
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl apply -f ingress-rule.yaml  
ingress.networking.k8s.io/ashu-app-ingress-rule created
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ kubectl get ingress -n ashu-app
NAME                    CLASS   HOSTS                ADDRESS   PORTS   AGE
ashu-app-ingress-rule   nginx   japur.ashutoshh.in             80      11s
thexyzcomp@cloudshell:ashu (us-phoenix-1)$ 

```

### Storage concept in k8s 

<img src="st.png">

### creating Database deployment 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   create  deployment  ashu-db --image=mysql:5.6  --port 3306 --dry-run=client -o yaml >db.yaml 
```

### MYSQL deploy file with storage 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-db
  name: ashu-db # name of deployment 
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-db
  strategy: {}
  template: # pod template
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-db
    spec:
      volumes: # to create volume 
      - name: ashudb-vol1
        hostPath: # type of vol to pic storage from Minion Node
          path: /mnt/ashu-db-data
          type: DirectoryOrCreate 
      containers:
      - image: mysql:5.6
        name: mysql
        ports:
        - containerPort: 3306
        resources: {}
        env: # putting env for setting root password of db 
        - name: MYSQL_ROOT_PASSWORD
          value: "Oracle@098"
        volumeMounts: # attaching volume to container 
        - name: ashudb-vol1
          mountPath: /var/lib/mysql/
status: {}

```
### creating secret to store db password 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   create  secret  generic  ashu-db-sec  --from-literal  ashudbpass="Oracle@098" --dry-run=client  -o yaml >dbsec.yaml 
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  apply -f dbsec.yaml 
secret/ashu-db-sec created
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   get secret 
NAME                  TYPE                                  DATA   AGE
ashu-db-sec           Opaque                                1      4s
```

### secret info 

<img src="sec.png">

### final Deployment of MYSQL yaml 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-db
  name: ashu-db # name of deployment 
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-db
  strategy: {}
  template: # pod template
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-db
    spec:
      volumes: # to create volume 
      - name: ashudb-vol1
        hostPath: # type of vol to pic storage from Minion Node
          path: /mnt/ashu-db-data
          type: DirectoryOrCreate 
      containers:
      - image: mysql:5.6
        name: mysql
        ports:
        - containerPort: 3306
        resources: {}
        env: # putting env for setting root password of db 
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ashu-db-sec
              key: ashudbpass
        volumeMounts: # attaching volume to container 
        - name: ashudb-vol1
          mountPath: /var/lib/mysql/
status: {}

```

### deploy it 

```
ashu@ip-172-31-87-240 k8s-resources]$ kubectl  apply -f db.yaml 
deployment.apps/ashu-db created
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get secret 
NAME                  TYPE                                  DATA   AGE
ashu-db-sec           Opaque                                1      3m51s
ashu-reg-secret       kubernetes.io/dockerconfigjson        1      27h
default-token-xnpnd   kubernetes.io/service-account-token   3      28h
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db   1/1     1            1           9s
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   get pods
NAME                      READY   STATUS    RESTARTS   AGE
ashu-db-84b5d599d-n96fz   1/1     Running   0          14s
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

### creating clusterIP type service 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   get  deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db   1/1     1            1           21m
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  expose deploy  ashu-db --name ashu-svc1 --port 3306 --dry-run=client -o yaml >dbsvc.yaml 
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  apply -f dbsvc.yaml 
service/ashu-svc1 created
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   get  svc
NAME        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
ashu-svc1   ClusterIP   10.105.96.31   <none>        3306/TCP   4s
[ashu@ip-172-31-87-240 k8s-resources]$ 

```

### webUI yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-web-app
  name: ashu-web-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-web-app
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-web-app
    spec:
      containers:
      - image: wordpress:4.8-apache
        name: wordpress
        ports:
        - containerPort: 80
        resources: {}
        env: 
        - name: WORDPRESS_DB_HOST # endpoint of db 
          value: ashu-svc1 # service name of DB deployment 
        - name: WORDPRESS_DB_PASSWORD
          valueFrom: # taking value from secret 
            secretKeyRef:
              name: ashu-db-sec
              key: ashudbpass  
status: {}

```

### deploy it 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  apply -f webui.yaml 
deployment.apps/ashu-web-app created
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get deploy 
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db        1/1     1            1           34m
ashu-web-app   1/1     1            1           32s
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

### creating service 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  expose deployment ashu-web-app  --type NodePort --port 80 --name ashulb7 --dry-run=client -o yaml  >uisvc.yaml
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  apply -f uisvc.yaml 
service/ashulb7 created
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get  svc
NAME        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
ashu-svc1   ClusterIP   10.105.96.31    <none>        3306/TCP       16m
ashulb7     NodePort    10.110.24.247   <none>        80:30841/TCP   4s
[ashu@ip-172-31-87-240 k8s-resources]$ 

```

### DS 

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: ashu-mon
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      containers:
      - name: fluentd-elasticsearch
        image: portainer/portainer
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/run/docker.sock
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/run/docker.sock
          type: Socket
```

### PDB --demo 

```
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: ashu-app-budget
spec:
  minAvailable: 5 
  selector: # target these pods having this lable 
    matchLabels:
      app: ashu-deploy1
```

### checking api 

```
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   api-resources  |  grep -i pod 
pods                              po           v1                                     true         Pod
podtemplates                                   v1                                     true         PodTemplate
horizontalpodautoscalers          hpa          autoscaling/v2                         true         HorizontalPodAutoscaler
pods                                           metrics.k8s.io/v1beta1                 true         PodMetrics
poddisruptionbudgets              pdb          policy/v1                              true         PodDisruptionBudget
podsecuritypolicies               psp          policy/v1beta1                         false        PodSecurityPolicy
```

## Helm Demo 

```
[ashu@ip-172-31-87-240 k8s-resources]$ helm repo ls
Error: no repositories to show
[ashu@ip-172-31-87-240 k8s-resources]$ helm repo add ashu-repo https://charts.bitnami.com/bitnami 
"ashu-repo" has been added to your repositories
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ helm repo ls
NAME            URL                               
ashu-repo       https://charts.bitnami.com/bitnami
[ashu@ip-172-31-87-240 k8s-resources]$ helm search repo nginx 
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION                                       
ashu-repo/nginx                         13.2.21         1.23.3          NGINX Open Source is a web server that can be a...
ashu-repo/nginx-ingress-controller      9.3.24          1.6.0           NGINX Ingress Controller is an Ingress controll...
ashu-repo/nginx-intel                   2.1.13          0.4.9           NGINX Open Source for Intel is a lightweight se...
[ashu@ip-172-31-87-240 k8s-resources]$ helm search repo mysql 
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ashu-repo/mysql                 9.4.5           8.0.31          MySQL is a fast, reliable, scalable, and easy t...
ashu-repo/phpmyadmin            10.3.8          5.2.0           phpMyAdmin is a free software tool written in P...
ashu-repo/mariadb               11.4.2          10.6.11         MariaDB is an open source, community-developed ...
ashu-repo/mariadb-galera        7.4.10          10.6.11         MariaDB Galera is a multi-primary database clus...
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

### deploy first helm chart 

```
[ashu@ip-172-31-87-240 k8s-resources]$ helm install ashu-webapp ashu-repo/nginx  
NAME: ashu-webapp
LAST DEPLOYED: Fri Jan  6 11:39:55 2023
NAMESPACE: ashu-project
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: nginx
CHART VERSION: 13.2.21
APP VERSION: 1.23.3

** Please be patient while the chart is being deployed **
NGINX can be accessed through the following DNS name from within your cluster:

    ashu-webapp-nginx.ashu-project.svc.cluster.local (port 80)

To access NGINX from outside the cluster, follow the steps below:

1. Get the NGINX URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace ashu-project -w ashu-webapp-nginx'

    export SERVICE_PORT=$(kubectl get --namespace ashu-project -o jsonpath="{.spec.ports[0].port}" services ashu-webapp-nginx)
    export SERVICE_IP=$(kubectl get svc --namespace ashu-project ashu-webapp-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo "http://${SERVICE_IP}:${SERVICE_PORT}"
```

### uninstall 

```
[ashu@ip-172-31-87-240 k8s-resources]$ helm ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
ashu-webapp     ashu-project    1               2023-01-06 11:39:55.563773397 +0000 UTC deployed        nginx-13.2.21   1.23.3     
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl   get  deploy 
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
ashu-webapp-nginx   1/1     1            1           53s
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get svc
NAME                TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
ashu-webapp-nginx   LoadBalancer   10.110.108.234   <pending>     80:32255/TCP   59s
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  get hpa
No resources found in ashu-project namespace.
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ helm uninstall ashu-webapp
release "ashu-webapp" uninstalled
[ashu@ip-172-31-87-240 k8s-resources]$ 
```

### pulling charts 

```
 837  helm pull  ashu-repo/nginx  
  838  ls
  839  tar xvzf nginx-13.2.21.tgz 
  840  history 
[ashu@ip-172-31-87-240 k8s-resources]$ ls
asdhupdb.yaml          autopod.json  dbsec.yaml  dsmon.yaml     mysecret.yaml  nginx              ocrdeploy.yaml  uisvc.yaml
ashu-app-ingress.yaml  autopod.yaml  dbsvc.yaml  hpa.yaml       mytask.yaml    nginx-13.2.21.tgz  ocrpod.yaml     webui.yaml
ashupod1.yaml          db1.yaml      db.yaml     mydeploy.yaml  nettest1.yaml  nodeport.yaml      ok.yaml
[ashu@ip-172-31-87-240 k8s-resources]$ cd nginx/
[ashu@ip-172-31-87-240 nginx]$ ls
Chart.lock  charts  Chart.yaml  README.md  templates  values.schema.json  values.yaml
[ashu@ip-172-31-87-240 nginx]$ 
[ashu@ip-172-31-87-240 nginx]$ 
[ashu@ip-172-31-87-240 nginx]$ ls  templates/
deployment.yaml  health-ingress.yaml  hpa.yaml      NOTES.txt  prometheusrules.yaml         serviceaccount.yaml  svc.yaml
extra-list.yaml  _helpers.tpl         ingress.yaml  pdb.yaml   server-block-configmap.yaml  servicemonitor.yaml  tls-secrets.yaml
[ashu@ip-172-31-87-240 nginx]$ 
[ashu@ip-172-31-87-240 nginx]$ 
```

### config file with all restriction 

```
apiVersion: v1
clusters:
- name: ashu-k8s-cluster
  cluster:
    server: https://158.101.3.169:6443
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJek1ERXdOREE0TURZMU1Gb1hEVE16TURFd01UQTRNRFkxTUZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTDIwCnlxK2JCVjFtWk4vcUoxdGp3czBpZm5iNDI4L1NxUjV0VTRyaGtHQ0VReDUyMmxrRFI5Nko5cXNkUXc2Q3FVejQKZUQ5Ty9VSUt6bHU0V0tGdGMvVE9kaVJuR29xYThkeGI4clJIRHVsRDU2U1FabE1hdEpsMjJmU2d3d2pFcDNONgpLRmN1WHV6SFNseWtWZEVtcmk2dVl0ZEZSSXl6bFZVMDZxSm5RbWcvY0RhOTl4NWw5aDB1di9zWDZDWHozSEJBCjFHRXRUWnpLRDY3dE53Zk8yb0J1U0VLSUFpbWEycWU5V1pxdS9aR3hGeFNOMmhFbVlIdldGcGovWUxtaStLVTQKNWhzbW0yR0lHa2FkZ0l1M2FjN1FyMlBFU2FiT2pnTkJHT1diTEJwUlFmOUVVb3lBeUhGaDJJY3UxZlFEZGhIeQpSY05vZFRNYkFsaVpreVpBZC9jQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZMUmd1NEphenlMZFVMVUcwZjA1K1E1eWpvOXFNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBQmJPTlh1cmgyUVZLRWdiMERIRgpWdFcrV0dHVVBLc2F5T2o4QUNnSk1iQ0tMTU1tb3hFWUllRUp1cU5QdnlWNjNrMEkrNTMrWWVYUkxHSmRxSWtUCkFTc2NpcU5uZE1xZS9WR3JKZHpnQWhCNkdkSnhpZmhLZHdJQmRMdFNRbVhQNVgvZ0JtaGc3RURWb2tBdGlHcncKYUVTK0U0VVlYbHhRTTd0OUR5RVZpTXFlZ3ZnNEtrWlBCR1FucjdxbGtmWERGVWsrelNQTlNRVFdCYVNaSHlCTgo5T1J5MlNFNGp4Ukd3NlU2VU5VYXEvQXZ1RzRFNU5MSUlEdS9MTTFZZ0VPVlZQTVZrRFhlVHJtOTkzRVB0L01QCnI5OStTY25DT3lseFB2SURwRkgxQzNSaVl4WXZLaldRb2MwZEQvd0NWaldGU3VCR1U5MTAyTkx3NVZYZUNmTTYKUW04PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
contexts:
- name: dev-env
  context:
    cluster: ashu-k8s-cluster
    namespace: test-access 
    user: default 
current-context: dev-env
users:
- name: default
  user:
    token: eyJhbGciOiJSUzI1NiIsImtpZCI6IlNCdXBndDRjdHh0U19aakhuc2xWZklHMFR5XzA5N2V1ZmdsQ2FnM1E2QzgifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJ0ZXN0LWFjY2VzcyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLW1yNW5jIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIzYWJhOTRkYi1jYjQyLTQwMTAtYWI3YS1lNjUwYzNkMGQwZDUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6dGVzdC1hY2Nlc3M6ZGVmYXVsdCJ9.IvPwg8gMcXVEPfubI6r9qG0sLYBtIcubuy1WXEjEJBB296P0cmrFhFFkBgdZXJTmRYT7exBzIjSoJpq_0voxsvIYim9h1i86hZkvL-rJW0Xs9r747Z5HmYRNZLhYoUPAzr0J_XZDm9yCHL5jBQhe_J6JWJrjs9ZT6vTX71YAKsMEuvAw-XKxzoq_XJ3zpKc77zWqkCvN0MKzBCIV9P3VJIvCqS5OuQJFFmenEQ3ioq7t-EBH7i8J35S69LajZEQR4qoAeUhRnJjAVnf_I9_K4nYll3fFLFD7yik-BCAwq6HJ_a-feyle2MZ6jxkZLxD6GOxuTmqEj1-F-QwEMKTUIA
```

### user role and rolebinding 

```
 882  kubectl  create role  test-pod -h
  883  kubectl create role foo --verb=get,list,watch --resource=pods -n test-access
  884  kubectl  create  rolebinding -h
  885  kubectl  create  rolebinding  bind1  --role=foo --serviceaccount=test-access:default -n test-access
  886  history 
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl create role foo1 --verb=get,list,watch --resource=service -n test-access
role.rbac.authorization.k8s.io/foo1 created
[ashu@ip-172-31-87-240 k8s-resources]$ 
[ashu@ip-172-31-87-240 k8s-resources]$ kubectl  create  rolebinding  bind2  --role=foo1  --serviceaccount=test-access:default -n test-access
rolebinding.rbac.authorization.k8s.io/bind2 created
```




