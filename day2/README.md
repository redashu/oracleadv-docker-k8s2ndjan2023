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



