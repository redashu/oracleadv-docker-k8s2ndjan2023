### getting started 

### vm vs container 

<img src="cont.png">

### Introduction to CRE -- 

<img src="cre.png">

### OCI for CRE 

<img src="oci.png">

### docker support with windows and linux kernel 

<img src="ds.png">

### installing docker ce on amazon linux in aws cloud 

### in cloud linux vm -- aws / azure / oracle cloud 

```
[root@ip-172-31-87-240 ~]# yum install docker 
Failed to set locale, defaulting to C
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
Resolving Dependencies
--> Running transaction check
---> Package docker.x86_64 0:20.10.17-1.amzn2.0.1 will be installed
--> Processing Dependency: runc >= 1.0.0 for package: docker-20.10.17-1.amzn2.0.1.x86_64
--> Processing Dependency: libcgroup >= 0.40.rc1-5.15 for package: docker-20.10.17-1.amzn2.0.1.x86_64
--> Processing Dependency: containerd >= 1.3.2 for package: docker-20.10.17-1.amzn2.0.1.x86_64
--> Processing Dependency: pigz for package: docker-20.10.17-1.amzn2.0.1.x86_64
--> Running transaction check
---> Package containerd.x86_64 0:1.6.8-1.amzn2 will be installed
---> Package libcgroup.x86_64 0:0.41-21.amzn2 will be installed
---> Package pigz.x86_64 0:2.3.4-1.amzn2.0.1 will be installed
---> Package runc.x86_64 0:1.1.4-1.amzn2 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================================================================
 Package                  Arch                 Version                              Repository                       Size
==========================================================================================================================
Installing:
 docker                   x86_64               20.10.17-1.amzn2.0.1                 amzn2extra-docker                39 M
Installing for dependencies:
 containerd               x86_64               1.6.8-1.amzn2                        amzn2extra-docker                27 M
 libcgroup                x86_64               0.41-21.amzn2                        amzn2-core                       66 k
 pigz                     x86_64               2.3.4-1.amzn2.0.1                    amzn2-core                       81 k
 runc                     x86_64               1.1.4-1.amzn2                        amzn2extra-docker               2.9 M

Transaction Summary
==========================================================================================================================
Install  1 Package (+4 Dependent packages)

Total download size: 69 M
Installed size: 260 M
Is this ok [y/d/N]: y
Downloading packages:
```

### NOn cloud based linux vms 

[use_link](https://docs.docker.com/engine/install/rhel/)


### lets understand docker configurations 

```
[root@ip-172-31-87-240 ~]# rpm  -qc  docker 
/etc/sysconfig/docker
/etc/sysconfig/docker-storage

```

### start docker runtime engine 

```
[root@ip-172-31-87-240 ~]# systemctl start docker 
[root@ip-172-31-87-240 ~]# systemctl enable docker 
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@ip-172-31-87-240 ~]# systemctl status  docker 
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2023-01-02 05:21:39 UTC; 12s ago
     Docs: https://docs.docker.com
 Main PID: 3656 (dockerd)
   CGroup: /system.slice/docker.service
           └─3656 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --default-ulimit nofile=32768:...

Jan 02 05:21:38 ip-172-31-87-240.ec2.internal dockerd[3656]: time="2023-01-02T05:21:38.890374920Z" level=info msg="...grpc
Jan 02 05:21:38 ip-172-31-87-
```

### docker architecture and docker clients

<img src="darch.png">

### connecting users for docker client access 

```
fire@ashutoshhs-MacBook-Air ~ % ssh  ashu@54.81.195.110 
The authenticity of host '54.81.195.110 (54.81.195.110)' can't be established.
ECDSA key fingerprint is SHA256:eqoycsooTY58A3BlOqavLW5+lHG1pK5ZWsWKc6DH0Is.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '54.81.195.110' (ECDSA) to the list of known hosts.
ashu@54.81.195.110's password: 

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
[ashu@ip-172-31-87-240 ~]$ 
```

### checking docker version 

```
[ashu@ip-172-31-87-240 ~]$ 
[ashu@ip-172-31-87-240 ~]$ docker  version 
Client:
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.18.6
 Git commit:        100c701
 Built:             Wed Sep 28 23:10:17 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server:
 Engine:
  Version:          20.10.17
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.6
  Git commit:       a89b842
  Built:            Wed Sep 28 23:10:55 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.8
  GitCommit:        9cd3357b7fd7218e4aec3eae239db1f68a5a6ec6
 runc:
  Version:          1.1.4
  GitCommit:        5fd4c4d144137e991c4acebb2146ab1483a97925
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0


```

## some docker operations 

### pulling image from docker hub 

```
[ashu@ip-172-31-87-240 ~]$ docker  images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[ashu@ip-172-31-87-240 ~]$ docker  pull oraclelinux:8.4 
8.4: Pulling from library/oraclelinux
a4df6f21af84: Pull complete 
Digest: sha256:b81d5b0638bb67030b207d28586d0e714a811cc612396dbe3410db406998b3ad
Status: Downloaded newer image for oraclelinux:8.4
docker.io/library/oraclelinux:8.4
[ashu@ip-172-31-87-240 ~]$ 
[ashu@ip-172-31-87-240 ~]$ docker  images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
oraclelinux   8.4       97e22ab49eea   14 months ago   246MB
[ashu@ip-172-31-87-240 ~]$ 


```

### docker storage database 

<img src="st.png">


### exloring ddocker db 

```
[ashu@ip-172-31-87-240 ~]$ docker  info   |   grep -i root
 Docker Root Dir: /var/lib/docker
```

### checking by root user 

```
[root@ip-172-31-87-240 ~]# whoami
root
[root@ip-172-31-87-240 ~]# cd  /var/lib/docker 
[root@ip-172-31-87-240 docker]# ls
buildkit  containers  image  network  overlay2  plugins  runtimes  swarm  tmp  trust  volumes
[root@ip-172-31-87-240 docker]# cd  image/
[root@ip-172-31-87-240 image]# ls
overlay2
[root@ip-172-31-87-240 image]# cd overlay2/
[root@ip-172-31-87-240 overlay2]# ls
distribution  imagedb  layerdb  repositories.json
[root@ip-172-31-87-240 overlay2]# cd imagedb/
[root@ip-172-31-87-240 imagedb]# ls
content  metadata
[root@ip-172-31-87-240 imagedb]# cd content/
[root@ip-172-31-87-240 content]# ls
sha256
[root@ip-172-31-87-240 content]# cd sha256/
[root@ip-172-31-87-240 sha256]# ls
97e22ab49eea70a5d500e00980537605d56f30f9614b3a6d6c4ae9ddbd642489
[root@ip-172-31-87-240 sha256]# 


```





