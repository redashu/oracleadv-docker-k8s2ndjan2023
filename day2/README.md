## Revision 

<img src="rev.png">

### agenda 

<img src="ag.png">

### software installer for diff linux distro 

<img src="ins.png">

### using entrypoint and cmd to replace process 

```
[ashu@ip-172-31-87-240 tasks]$ docker run -itd --name t3  --entrypoint sleep  ashualp:pycodev1   100
be690d0e4c9584f6754615a1f41090e6e99e7c1804c8eab39eecd985f642a235
[ashu@ip-172-31-87-240 tasks]$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED              STATUS              PORTS     NAMES
be690d0e4c95   ashualp:pycodev1   "sleep 100"              3 seconds ago        Up 2 seconds                  t3
75f85638e0c2   ashualp:pycodev1   "/bin/sh -c 'python3…"   About a minute ago   Up About a minute             t2
e49f9fbb7b33   ashualp:pycodev1   "/bin/sh -c 'python3…"   About a minute ago   Up About a minute             t1
8c0495c60e65   alpine             "/bin/sh"                11 minutes ago       Up 11 minutes                 test1
[ashu@ip-172-31-87-240 tasks]$ 

```

## Understanding Cgroups 

<img src="cg.png">

### creating container with no memory boundry 

```
[ashu@ip-172-31-87-240 tasks]$ docker run -itd --name ashuc1 alpine sleep 1000 
44453d8e2e18961b8bd0ef63a0295b9e353d2b3a02cce0f1f35b06b2a6b7955e
[ashu@ip-172-31-87-240 tasks]$ docker ps
CONTAINER ID   IMAGE     COMMAND        CREATED         STATUS         PORTS     NAMES
44453d8e2e18   alpine    "sleep 1000"   3 seconds ago   Up 2 seconds             ashuc1
[ashu@ip-172-31-87-240 tasks]$ 
```

### with RAM limit 

```
[ashu@ip-172-31-87-240 tasks]$ docker run -itd --name ashuc2 --memory 200M  alpine sleep 1000 
5e2b95b317d9164675a86e46c7d23f4359963153b189a3b194ce6dc85532b5c0
[ashu@ip-172-31-87-240 tasks]$ 
```

### CPU 

<img src="cpu.png">


```
[ashu@ip-172-31-87-240 tasks]$ docker run -itd --name ashuc2 --memory 200M   --cpuset-cpus=0 --cpu-shares=300     alpine sleep 1000 
f68589ea8163396736bdc94492fc35c66e85b3f825fa55db32bb0a39bf5f7373
[ashu@ip-172-31-87-240 tasks]$ docker ps
CONTAINER ID   IMAGE     COMMAND        CREATED         STATUS         PORTS     NAMES
f68589ea8163   alpine    "sleep 1000"   4 seconds ago   Up 3 seconds             ashuc2
[ashu@ip-172-31-87-240 tasks]$ 

```

## Networking in containers 

### there are two networking models for containers 

<img src="net.png">

### CNM bridges 

<img src="br.png">

### listing bridges 

```
[ashu@ip-172-31-87-240 tasks]$ docker  network  ls
NETWORK ID     NAME      DRIVER    SCOPE
9a123e28b2bd   bridge    bridge    local
fb0f31866eb8   host      host      local
f05a4d83cf7c   none      null      local
[ashu@ip-172-31-87-240 tasks]$ docker  network  inspect  bridge
[
    {
        "Name": "bridge",
        "Id": "9a123e28b2bd1523a769a80ca3166ccc455b6cce9fd8ad0d59f6ea425c86ca14",
        "Created": "2023-01-03T03:18:17.919143771Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```

### listing containers in bridge 

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker network  inspect  bridge 
[
    {
        "Name": "bridge",
        "Id": "9a123e28b2bd1523a769a80ca3166ccc455b6cce9fd8ad0d59f6ea425c86ca14",
        "Created": "2023-01-03T03:18:17.919143771Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1b06842110a1ab5db3aeeb6f38dc685ebb94e7eff75ca7b40d7fa861484a06bf": {
                "Name": "sudeepc1",
                "EndpointID": "ad1fbc2ad56007d0a69e59e4af54d60b38209d256472a5caf278911726312479",
                "MacAddress": "02:42:ac:11:00:06",
                "IPv4Address": "172.17.0.6/16",
                "IPv6Address": ""
            },
            "1f75fabae5b4732c39a36cd1e8db51bc452ce207dbdb68b7e3a3f8c12d159330": {
                "Name": "anand1",
                "EndpointID": "4fd2230c415e025c13d9011705cb862e38d7294c601ba920fc8abfe614f2e917",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "65e4daad4a1f29f205775e0a8a5200c92667ad258ba7e88580f7f846c5b351a4": {
                "Name": "ashuc1",
                "EndpointID": "69d1be0bccba5b606e51e6f8548f127f723e8703ba3864047cb9156c38880fa6",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "824033af76caf996e0dd4fbfc155c329db40730c48283c6a2e20eb1527504c02": {
                "Name": "Saurabhc2",
                "EndpointID": "7f709ed333f692a64562e8b0b6631cbd03a4fd60233b4ad3ae948988d8432eb9",
                "MacAddress": "02:42:ac:11:00:04",
                "IPv4Address": "172.17.0.4/16",
                "IPv6Address": ""
            },
            "95790864474d4930b74db46fc7e2cbdcca92871dd83f2bb57b5ddfdb9b12deef": {
                "Name": "ankitac1",
                "EndpointID": "4829233add5428e11ea9fc96521f3023fa5378c793621eea5f6b2c61c1758f53",
                "MacAddress": "02:42:ac:11:00:07",
                "IPv4Address": "172.17.0.7/16",
                "IPv6Address": ""
            },
            "9c0fe8cbe13fabd23148b97555f43268f62ef0044b140f21860adbb2202a7937": {
                "Name": "sivac1",
                "EndpointID": "b2cab768695e24be33c8a0d0e6feb34eb5bc90d062ebb144334610b7f365d6cc",
                "MacAddress": "02:42:ac:11:00:08",
                "IPv4Address": "172.17.0.8/16",
                "IPv6Address": ""
            },
            "a5996ecc9d61d01476146da98c643cc8ff62361b4f89bf2c8920fb9dddf99c6a": {
                "Name": "sibashisc1",
                "EndpointID": "470e9a087338b3bf9bd9ec281d56dc71920b46224384e368a121528ed0a15d27",
                "MacAddress": "02:42:ac:11:00:05",
                "IPv4Address": "172.17.0.5/16",
                "IPv6Address": ""
```

### filtering json details 

```
[ashu@ip-172-31-87-240 tasks]$ docker  inspect  ashuc1  --format='{{.Id}}'
65e4daad4a1f29f205775e0a8a5200c92667ad258ba7e88580f7f846c5b351a4
[ashu@ip-172-31-87-240 tasks]$ docker  inspect  ashuc1  --format='{{.State.Status}}'
running
[ashu@ip-172-31-87-240 tasks]$ docker  inspect  ashuc1  --format='{{.NetworkSettings.IPAddress}}'
172.17.0.2
[ashu@ip-172-31-87-240 tasks]$ docker  inspect  ashuc1  --format='{{.NetworkSettings.Networks.bridge.IPAddress}}'
172.17.0.2
[ashu@ip-172-31-87-240 tasks]$ docker  inspect  Saurabhc2  --format='{{.NetworkSettings.Networks.bridge.IPAddress}}'
172.17.0.4
[ashu@ip-172-31-87-240 tasks]$ 
```

### containers in the same bridge can connect to each other by default 

```
[ashu@ip-172-31-87-240 tasks]$ docker  exec -it ashuc1  sh 
/ # ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
          inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:13 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:1070 (1.0 KiB)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/ # ping  172.17.0.4 
PING 172.17.0.4 (172.17.0.4): 56 data bytes
64 bytes from 172.17.0.4: seq=0 ttl=64 time=0.108 ms
64 bytes from 172.17.0.4: seq=1 ttl=64 time=0.070 ms
^C
--- 172.17.0.4 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.070/0.089/0.108 ms
```

### NAT in docker host 

<img src="nat.png">

### checking 

```
ashu@ip-172-31-87-240 tasks]$ docker  exec -it ashuc1  sh 
/ # ping www.google.com 
PING www.google.com (142.251.163.105): 56 data bytes
64 bytes from 142.251.163.105: seq=0 ttl=95 time=1.270 ms
64 bytes from 142.251.163.105: seq=1 ttl=95 time=1.180 ms
64 bytes from 142.251.163.105: seq=2 ttl=95 time=1.157 ms
64 bytes from 142.251.163.105: seq=3 ttl=95 time=1.297 ms
^C
--- www.google.com ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 1.157/1.226/1.297 ms
/ # 
/ # wget  https://raw.githubusercontent.com/redashu/pythonLang/main/while.py
Connecting to raw.githubusercontent.com (185.199.111.133:443)
saving to 'while.py'
while.py             100% |***************************************************************************************************************|   232  0:00:00 ETA
'while.py' saved
/ # ls
bin       etc       lib       mnt       proc      run       srv       tmp       var
dev       home      media     opt       root      sbin      sys       usr       while.py
/ # 


```

### to Understanding port forwarding / mapping -- we are containerizing webapp with nginx webserver 

<img src="nginx.png">

### taking sample html ui code 

```
[ashu@ip-172-31-87-240 ashu-apps]$ ls
db-apps  java-apps  node-app  tasks
[ashu@ip-172-31-87-240 ashu-apps]$ mkdir  webapps
[ashu@ip-172-31-87-240 ashu-apps]$ cd  webapps/
[ashu@ip-172-31-87-240 webapps]$ ls
[ashu@ip-172-31-87-240 webapps]$ git clone  https://github.com/microsoft/project-html-website.git
Cloning into 'project-html-website'...
remote: Enumerating objects: 24, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 24 (delta 0), reused 3 (delta 0), pack-reused 19
Receiving objects: 100% (24/24), 465.86 KiB | 24.52 MiB/s, done.
[ashu@ip-172-31-87-240 webapps]$ ls
project-html-website
[ashu@ip-172-31-87-240 webapps]$ 

```

### adding dockerfile 

```
[ashu@ip-172-31-87-240 webapps]$ ls
project-html-website
[ashu@ip-172-31-87-240 webapps]$ touch Dockerfile
[ashu@ip-172-31-87-240 webapps]$ ls
Dockerfile  project-html-website
[ashu@ip-172-31-87-240 webapps]$ ls  project-html-website/
css  fonts  img  index.html  LICENSE  README.md  SECURITY.md
[ashu@ip-172-31-87-240 webapps]$ 

```

### adding dockerfile and .dockerignore 

```
[ashu@ip-172-31-87-240 webapps]$ ls -a
.  ..  Dockerfile  .dockerignore  project-html-website
```

### lets build it 

```
[ashu@ip-172-31-87-240 webapps]$ ls -a
.  ..  Dockerfile  .dockerignore  project-html-website
[ashu@ip-172-31-87-240 webapps]$ 
[ashu@ip-172-31-87-240 webapps]$ docker build -t  ashunginx:appv1  .  
Sending build context to Docker daemon    834kB
Step 1/3 : FROM nginx
latest: Pulling from library/nginx
3f4ca61aafcd: Already exists 
50c68654b16f: Pull complete 
3ed295c083ec: Pull complete 
40b838968eea: Pull complete 
88d3ab68332d: Pull complete 
5f63362a3fa3: Pull complete 
Digest: sha256:0047b729188a15da49380d9506d65959cce6d40291ccfb4e039f5dc7efd33286
Status: Downloaded newer image for nginx:latest
 ---> 1403e55ab369
Step 2/3 : LABEL name=ashutoshh
 ---> Running in 1187f50740b7
Removing intermediate container 1187f50740b7
 ---> e81be24e9987
Step 3/3 : COPY project-html-website /usr/share/nginx/html/
 ---> 0e9ef82e621c
Successfully built 0e9ef82e621c
Successfully tagged ashunginx:appv1
[ashu@ip-172-31-87-240 webapps]$ 
```

### understanding and doing port mapping 

<img src="portm.png">

### port mapping 

```
ashu@ip-172-31-87-240 webapps]$ docker run -itd --name ashuwebapp1 -p  1234:80  ashunginx:appv1  
c796e213864e5eb88f792afca55729101445dfb408eab31399ab6d2beea83b7f
[ashu@ip-172-31-87-240 webapps]$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED                  STATUS                  PORTS                                   NAMES
8a24a82782d6   sibashisngnix:appv1   "/docker-entrypoint.…"   Less than a second ago   Up Less than a second   0.0.0.0:9966->80/tcp, :::9966->80/tcp   sibashisngnixc1
c796e213864e   ashunginx:appv1       "/docker-entrypoint.…"   3 seconds ago            Up 2 seconds            0.0.0.0:1234->80/tcp, :::1234->80/tcp   ashuwebapp1
ccfbc5d9362d   webapp:shailesh       "/docker-entrypoint.…"   8 seconds ago            Up 8 seconds            0.0.0.0:2345->80/tcp, :::2345->80/tcp   shaileshweb
[ashu@ip-172-31-87-240 webapps]$ 

```

## Docker bridge None 

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker  network  ls
NETWORK ID     NAME      DRIVER    SCOPE
9a123e28b2bd   bridge    bridge    local
fb0f31866eb8   host      host      local
f05a4d83cf7c   none      null      local
[ashu@ip-172-31-87-240 ashu-apps]$ docker run -it --rm  --network none  alpine  
/ # 
/ # ifconfig 
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/ # ping google.com 
ping: bad address 'google.com'
/ # ping  172.17.0.1
PING 172.17.0.1 (172.17.0.1): 56 data bytes
ping: sendto: Network unreachable
/ # exit
[ashu@ip-172-31-87-240 ashu-apps]$ 
```

### Host network bridge 

<img src="host.png">

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker run -it --rm  --network host   alpine  
/ # ifconfig 
docker0   Link encap:Ethernet  HWaddr 02:42:4F:88:68:DB  
          inet addr:172.17.0.1  Bcast:172.17.255.255  Mask:255.255.0.0
          inet6 addr: fe80::42:4fff:fe88:68db/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:5277 errors:0 dropped:0 overruns:0 frame:0
          TX packets:5689 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:5775072 (5.5 MiB)  TX bytes:45138549 (43.0 MiB)

eth0      Link encap:Ethernet  HWaddr 12:C6:F8:D1:44:53  
          inet addr:172.31.87.240  Bcast:172.31.95.255  Mask:255.255.240.0
          inet6 addr: fe80::10c6:f8ff:fed1:4453/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:9001  Metric:1
          RX packets:559224 errors:0 dropped:0 overruns:0 frame:0
          TX packets:249405 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:511541454 (487.8 MiB)  TX bytes:68972414 (65.7 MiB)
```

### creating custom bridge in two days 

```
 docker  network create  ashubr1 
  docker network create   ashubr2 --subnet 192.168.100.0/24  --gateway  192.168.100.1
```

### lets test it 

```
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
de1297431794   ashubr1   bridge    local
7cd95c087527   ashubr2   bridge    local
9a123e28b2bd   bridge    bridge    local
fb0f31866eb8   host      host      local
f05a4d83cf7c   none      null      local
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ docker run -itd --name ashub1  --network ashubr1  alpine 
9ca897dbb401f858e5f0736f2997801e9835bda476a192aee2fc717b21e3fdd4
[ashu@ip-172-31-87-240 ashu-apps]$ docker run -itd --name ashub2  --network ashubr1  alpine 
b01813f14202ab4f6034e3dab3fc697c6c97a3f324f148c467634c71919b2c01
[ashu@ip-172-31-87-240 ashu-apps]$ docker  exec -it ashub1 sh 
/ # ping ashub2
PING ashub2 (172.22.0.3): 56 data bytes
64 bytes from 172.22.0.3: seq=0 ttl=64 time=0.089 ms
64 bytes from 172.22.0.3: seq=1 ttl=64 time=0.085 ms
^C
--- ashub2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.085/0.087/0.089 ms
/ # 
[ashu@ip-172-31-87-240 ashu-apps]$ 

```

### understanding concept in custom bridge 

<img src="brc.png">

### more command 

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
de1297431794   ashubr1   bridge    local
7cd95c087527   ashubr2   bridge    local
9a123e28b2bd   bridge    bridge    local
fb0f31866eb8   host      host      local
f05a4d83cf7c   none      null      local
[ashu@ip-172-31-87-240 ashu-apps]$ docker network prune 
WARNING! This will remove all custom networks not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Networks:
ashubr1
ashubr2

[ashu@ip-172-31-87-240 ashu-apps]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
9a123e28b2bd   bridge    bridge    local
fb0f31866eb8   host      host      local
f05a4d83cf7c   none      null      local
```

### compose by docker 

<img src="compose.png">

### version of compose file

<img src="v.png">

### checking docker compose version 

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker -v
Docker version 20.10.17, build 100c701
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ docker-compose  -v
Docker Compose version v2.14.2
[ashu@ip-172-31-87-240 ashu-apps]$ 

```

### docker compsoe example  1

```
version:  '3.8' # compose file version i am gonna use 
services: # all your micro services components
  ashu-ui-app: # name of service 
    image: ashunginx:appv1 
    container_name: ashu-ui-c1
    ports:
    - "1234:80"
 
```

### lets run and verify 

```
ashu@ip-172-31-87-240 ashu-apps]$ ls
ashu-compose  db-apps  java-apps  node-app  tasks  webapps
[ashu@ip-172-31-87-240 ashu-apps]$ cd  ashu-compose/
[ashu@ip-172-31-87-240 ashu-compose]$ ls
docker-compose.yaml
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  up  -d 
[+] Running 2/2
 ⠿ Network ashu-compose_default  Created                                                                                      0.0s
 ⠿ Container ashu-ui-c1          Started                                                                                      0.6s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         22 seconds ago      Up 21 seconds       0.0.0.0:1234->80/tcp, :::1234->80/tcp
[ashu@ip-172-31-87-240 ashu-compose]$ 

```

### few more commands 

```
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         3 minutes ago       Up 3 minutes        0.0.0.0:1234->80/tcp, :::1234->80/tcp
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  stop 
[+] Running 1/1
 ⠿ Container ashu-ui-c1  Stopped                                                                                                                          0.2s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND             SERVICE             CREATED             STATUS              PORTS
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  start
[+] Running 1/1
 ⠿ Container ashu-ui-c1  Started                                                                                                                          0.5s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         3 minutes ago       Up 4 seconds        0.0.0.0:1234->80/tcp, :::1234->80/tcp
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose exec  ashu-ui-app  bash 
root@9a823384268c:/# 
root@9a823384268c:/# exit
exit
```

### adding more service to compose 

```
version:  '3.8' # compose file version i am gonna use 
services: # all your micro services components
  ashu-test-app:
    image: alpine
    container_name: ashuct1
    command: sleep 1000
    restart: always # restart policy of container 
  ashu-ui-app: # name of service 
    image: ashunginx:appv1 
    container_name: ashu-ui-c1
    ports:
    - "1234:80"
  
```

### lets rerun it 

```
ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         5 minutes ago       Up 2 minutes        0.0.0.0:1234->80/tcp, :::1234->80/tcp
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  up -d
[+] Running 2/2
 ⠿ Container ashuct1     Started                                                                                                                          0.5s
 ⠿ Container ashu-ui-c1  Running                                                                                                                          0.0s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         6 minutes ago       Up 2 minutes        0.0.0.0:1234->80/tcp, :::1234->80/tcp
ashuct1             alpine              "sleep 1000"             ashu-test-app       10 seconds ago      Up 9 seconds        
[ashu@ip-172-31-87-240 ashu-compose]$ 
```

### more comamnds 

```
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  stop 
[+] Running 2/2
 ⠿ Container ashuct1     Stopped                                                                                                                         10.2s
 ⠿ Container ashu-ui-c1  Stopped                                                                                                                          0.2s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND             SERVICE             CREATED             STATUS              PORTS
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps -a
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS                        PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         7 minutes ago       Exited (0) 21 seconds ago     
ashuct1             alpine              "sleep 1000"             ashu-test-app       2 minutes ago       Exited (137) 11 seconds ago   
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  start
[+] Running 2/2
 ⠿ Container ashuct1     Started                                                                                                                          0.8s
 ⠿ Container ashu-ui-c1  Started                                                                                                                          0.7s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         8 minutes ago       Up 5 seconds        0.0.0.0:1234->80/tcp, :::1234->80/tcp
ashuct1             alpine              "sleep 1000"
```

### lets clean up 

```
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  down 
[+] Running 3/3
 ⠿ Container ashu-ui-c1          Removed                                                                                                                  0.2s
 ⠿ Container ashuct1             Removed                                                                                                                 10.2s
 ⠿ Network ashu-compose_default  Removed  
```

### Example 3 

```
version:  '3.8' # compose file version i am gonna use 
services: # all your micro services components
  ashu-java-test:
    build: 
      context: ../java-apps
      dockerfile: Dockerfile
    image: ashujava:composev1 # image i want to build 
    container_name: ashujc1 
  ashu-test-app:
    image: alpine
    container_name: ashuct1
    command: sleep 1000
    restart: always # restart policy of container 
  ashu-ui-app: # name of service 
    image: ashunginx:appv1 
    container_name: ashu-ui-c1
    ports:
    - "1234:80"
  
```

### run it 

```
ashu@ip-172-31-87-240 ashu-compose]$ ls
docker-compose.yaml
[ashu@ip-172-31-87-240 ashu-compo
[ashu@ip-172-31-87-240 ashu-compose]$ 
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  up -d --build 
[+] Building 3.7s (10/10) FINISHED                                                                                                 
 => [internal] load build definition from Dockerfile                                                                          0.0s
 => => transferring dockerfile: 599B                                                                                          0.0s
 => [internal] load .dockerignore                                                                                             0.0s
 => => transferring context: 2B                                                                                               0.0s
 => [internal] load metadata for docker.io/library/openjdk:latest                                                             0.0s
 => [1/5] FROM docker.io/library/openjdk                                                                                      0.1s
 => [internal] load build context                                                                                             0.1s
 => => transferring context: 427B                                                                                             0.0s
 => [2/5] RUN mkdir /mycode                                                                                                   0.8s
 => [3/5] COPY ashucode.java /mycode/                                                                                         0.1s
 => [4/5] WORKDIR /mycode                                                                                                     0.0s
 => [5/5] RUN javac ashucode.java                                                                                             2.5s
 => exporting to image                                                                                                        0.1s
 => => exporting layers                                                                                                       0.1s
 => => writing image sha256:a7cac9ed3aac490064501377b4e5cab0c5f91d9f798402796b662ba61f6498ce                                  0.0s
 => => naming to docker.io/library/ashujava:composev1                                                                         0.0s
[+] Running 4/4
 ⠿ Network ashu-compose_default  Created                                                                                      0.1s
 ⠿ Container ashu-ui-c1          Started                                                                                      1.7s
 ⠿ Container ashujc1             Started                                                                                      1.4s
 ⠿ Container ashuct1             Started      
```

### few commands 

```
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  stop   ashu-java-test
[+] Running 1/1
 ⠿ Container ashujc1  Stopped                                                                                                                                                                 0.2s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED              STATUS              PORTS
ashu-ui-c1          ashunginx:appv1     "/docker-entrypoint.…"   ashu-ui-app         About a minute ago   Up About a minute   0.0.0.0:1234->80/tcp, :::1234->80/tcp
ashuct1             alpine              "sleep 1000"             ashu-test-app       About a minute ago   Up About a minute   
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps -a
NAME                IMAGE                COMMAND                  SERVICE             CREATED              STATUS                       PORTS
ashu-ui-c1          ashunginx:appv1      "/docker-entrypoint.…"   ashu-ui-app         About a minute ago   Up About a minute            0.0.0.0:1234->80/tcp, :::1234->80/tcp
ashuct1             alpine               "sleep 1000"             ashu-test-app       About a minute ago   Up About a minute            
ashujc1             ashujava:composev1   "java ashucode"          ashu-java-test      About a minute ago   Exited (143) 9 seconds ago   
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  start  ashu-java-test
[+] Running 1/1
 ⠿ Container ashujc1  Started                                                                                                                                                                 0.5s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  ps
NAME                IMAGE                COMMAND                  SERVICE             CREATED              STATUS              PORTS
ashu-ui-c1          ashunginx:appv1      "/docker-entrypoint.…"   ashu-ui-app         About a minute ago   Up About a minute   0.0.0.0:1234->80/tcp, :::1234->80/tcp
ashuct1             alpine               "sleep 1000"             ashu-test-app       About a minute ago   Up About a minute   
ashujc1             ashujava:composev1   "java ashucode"  
```

### Introduction to docker storage 

<img src="st.png">

### storage for engine 

<img src="st1.png">

### creating xfs filesystem and mounting for external storage 

```
[root@ip-172-31-87-240 ~]# lsblk 
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   50G  0 disk 
`-xvda1 202:1    0   50G  0 part /
xvdf    202:80   0  500G  0 disk 
[root@ip-172-31-87-240 ~]# mkfs.xfs   /dev/xvdf 
meta-data=/dev/xvdf              isize=512    agcount=4, agsize=32768000 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=131072000, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=64000, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@ip-172-31-87-240 ~]# mkdir  /opt/docker
[root@ip-172-31-87-240 ~]# mount  /dev/xvdf  /opt/docker/
[root@ip-172-31-87-240 ~]# vim /etc/fstab 
[root@ip-172-31-87-240 ~]# mount -a
[root@ip-172-31-87-240 ~]# 

```

### lets configure docker to use it 

```
root@ip-172-31-87-240 ~]# cat  /etc/sysconfig/docker
# The max number of open files for the daemon itself, and all
# running containers.  The default value of 1048576 mirrors the value
# used by the systemd service unit.
DAEMON_MAXFILES=1048576

# Additional startup options for the Docker daemon, for example:
# OPTIONS="--ip-forward=true --iptables=true"
# By default we limit the number of open files per container
OPTIONS="--default-ulimit nofile=32768:65536  -g  /opt/docker/"


```

====

```
 80  systemctl daemon-reload 
   81  systemctl restart docker
```

### check it 

```
[root@ip-172-31-87-240 ~]# docker info   |   grep -i root
 Docker Root Dir: /opt/docker
```

### now no old data will be available because all the data is in /var/lib/docker

### lets transfer it 

```
 rsync  -avp  /var/lib/docker/   /opt/docker/
 systemctl restart docker 
```

### MYSQL container with docker storage 

### creating volume 
```
[ashu@ip-172-31-87-240 ashu-compose]$ docker  volume  ls
DRIVER    VOLUME NAME
[ashu@ip-172-31-87-240 ashu-compose]$ docker  volume  create  ashu-db-vol
ashu-db-vol
[ashu@ip-172-31-87-240 ashu-compose]$ docker  volume  ls
DRIVER    VOLUME NAME
local     ashu-db-vol
[ashu@ip-172-31-87-240 ashu-compose]$ docker  volume  inspect  ashu-db-vol
[
    {
        "CreatedAt": "2023-01-03T11:16:08Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/opt/docker/volumes/ashu-db-vol/_data",
        "Name": "ashu-db-vol",
        "Options": {},
        "Scope": "local"
    }
]
```

### lets create container 

```
[ashu@ip-172-31-87-240 ashu-compose]$ docker  run -itd --name ashudbc1  -v  ashu-db-vol:/var/lib/mysql/  -e  MYSQL_ROOT_PASSWORD="Oracle@098"  mysql 
db17756c461024be444b84417265ea105fb32e8a952e91dcbb6bc11ecc7ff050
[ashu@ip-172-31-87-240 ashu-compose]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                 NAMES
7e8769346e85   mysql     "docker-entrypoint.s…"   1 second ago    Up 1 second    3306/tcp, 33060/tcp   sibashisdbc1
db17756c4610   mysql     "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   3306/tcp, 33060/tcp   ashudbc1
[ashu@ip-172-31-87-240 ashu-compose]$ docker  logs   ashudbc1
2023-01-03 11:22:01+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2023-01-03 11:22:02+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2023-01-03 11:22:02+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2023-01-03 11:22:02+00:00 [Note] [Entrypoint]: Initializing database files
2023-01-03T11:22:02.271802Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2023-01-03T11:22:02.273267Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.31) initializing of server in progress as process 79
2023-01-03T11:22:02.297622Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-01-03T11:22:02.786159Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-01-03T11:22:04.272477Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2023-01-03 11:22:08+00:00 [Note] [Entrypoint]: Database files initi
```

### creating a database in container 

```
[ashu@ip-172-31-87-240 ashu-compose]$ 
[ashu@ip-172-31-87-240 ashu-compose]$ docker  exec -it  ashudbc1  bash 
bash-4.4# 
bash-4.4# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.31 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> create  database hello;
Query OK, 1 row affected (0.00 sec)

mysql> exit;
Bye
bash-4.4# exit
exit
```

### MYSQL container + with volume _ with network using compose 

```
version: '3.8'
volumes: # creating volume 
  ashu-db-vol2: # name of volume 
networks: # for creating network 
  ashubr-net: # name of network bridge 
services:
  ashudb-app:
    image: mysql
    container_name: ashudbc1
    volumes: # mounting volume 
    - "ashu-db-vol2:/var/lib/mysql/"
    environment: # setting mysql root user password 
      MYSQL_ROOT_PASSWORD: "Oracle@098"
    networks: # using bridge 
    - ashubr-net 


```

### running it 

```
[ashu@ip-172-31-87-240 ashu-compose]$ ls
docker-compose.yaml  mysql.yaml
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  -f  mysql.yaml  up -d 
[+] Running 3/3
 ⠿ Network ashu-compose_ashubr-net     Created                                                                                0.0s
 ⠿ Volume "ashu-compose_ashu-db-vol2"  Created                                                                                0.0s
 ⠿ Container ashudbc1                  Started                                                                                0.6s
[ashu@ip-172-31-87-240 ashu-compose]$ docker-compose  -f mysql.yaml  ps
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
ashudbc1            mysql               "docker-entrypoint.s…"   ashudb-app          15 seconds ago      Up 14 seconds       3306/tcp, 33060/tcp
[ashu@ip-172-31-87-240 ashu-compose]$ 


```








