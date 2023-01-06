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


