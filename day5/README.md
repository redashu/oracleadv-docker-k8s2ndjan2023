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

