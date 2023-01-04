### Revision 

<img src="rev.png">

### volume combinations with containers

<img src="volc.png">

### two docker volume with single container 

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker volume  create  v1
v1
[ashu@ip-172-31-87-240 ashu-apps]$ docker volume  create  v2
v2
[ashu@ip-172-31-87-240 ashu-apps]$ docker volume inspect  v1
[
    {
        "CreatedAt": "2023-01-04T04:33:09Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/opt/docker/volumes/v1/_data",
        "Name": "v1",
        "Options": {},
        "Scope": "local"
    }
]
[ashu@ip-172-31-87-240 ashu-apps]$ docker volume inspect  v2
[
    {
        "CreatedAt": "2023-01-04T04:33:11Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/opt/docker/volumes/v2/_data",
        
============
[ashu@ip-172-31-87-240 ashu-apps]$ docker run -itd --name ashuc1 -v  v1:/mnt/data1:rw  -v  v2:/mnt/data2:ro  alpine 
efd9830be37df829d0b320fd82c4e6dffde90270d2628ac1118c3bba8bdba7e1
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ docker ps
CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
efd9830be37d   alpine    "/bin/sh"   4 seconds ago   Up 3 seconds             ashuc1
```

### verify container 

```
 360  docker run -itd --name ashuc1 -v  v1:/mnt/data1:rw  -v  v2:/mnt/data2:ro  alpine 
  361  docker ps
  362  docker  inspect  ashuc1
  363  history 
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ docker  exec -it ashuc1  sh 
/ # cd  /mnt/
/mnt # ls
data1  data2
/mnt # cd  data1
/mnt/data1 # ls
/mnt/data1 # mkdir hello 
/mnt/data1 # touch a.txt
/mnt/data1 # ls
a.txt  hello
/mnt/data1 # cd ../data2
/mnt/data2 # ls
/mnt/data2 # mkdir hiii
mkdir: can't create directory 'hiii': Read-only file system
/mnt/data2 # exit
```

### mounting same volume to other container 

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker run -itd --name ashuc2   -v  v2:/ashulogs:rw   alpine 
58dd9ecb721f9d405a3124d0e820cb3f7f7d42fb074babe9e34c65e54960cdaf
[ashu@ip-172-31-87-240 ashu-apps]$ 
[ashu@ip-172-31-87-240 ashu-apps]$ docker  exec -it ashuc2  sh 
/ # ls
ashulogs  dev       home      media     opt       root      sbin      sys       usr
bin       etc       lib       mnt       proc      run       srv       tmp       var
/ # cd /ashulogs/
/ashulogs # ls
/ashulogs # echo hii i am data >abc.logs
/ashulogs # ls
abc.logs
/ashulogs # echo hii i am data >abc1.logs
/ashulogs # exit
[ashu@ip-172-31-87-240 ashu-apps]$ 
```

### checking 

```
[ashu@ip-172-31-87-240 ashu-apps]$ docker  exec -it ashuc1  ls  /mnt/data2
[ashu@ip-172-31-87-240 ashu-apps]$ docker  exec -it ashuc1  ls  /mnt/data2
abc.logs
[ashu@ip-172-31-87-240 ashu-apps]$ docker  exec -it ashuc1  ls  /mnt/data2
abc.logs   abc1.logs
[ashu@ip-172-31-87-240 ashu-apps]$ 


```

### accessing volume without container 

```
[root@ip-172-31-87-240 ~]# cd  /opt/docker/
[root@ip-172-31-87-240 docker]# ls
buildkit  containers  image  network  overlay2  plugins  runtimes  swarm  tmp  trust  volumes
[root@ip-172-31-87-240 docker]# cd  volumes/
[root@ip-172-31-87-240 volumes]# ls
backingFsBlockDev  metadata.db  v1  v2
[root@ip-172-31-87-240 volumes]# cd v1
[root@ip-172-31-87-240 v1]# ls
_data
[root@ip-172-31-87-240 v1]# cd _data/
[root@ip-172-31-87-240 _data]# ls
a.txt  hello
[root@ip-172-31-87-240 _data]# cd ..
[root@ip-172-31-87-240 v1]# ls
_data
[root@ip-172-31-87-240 v1]# cd ..
[root@ip-172-31-87-240 volumes]# ls
backingFsBlockDev  metadata.db  v1  v2
[root@ip-172-31-87-240 volumes]# cd v2
[root@ip-172-31-87-240 v2]# ls
_data
[root@ip-172-31-87-240 v2]# cd _data/
[root@ip-172-31-87-240 _data]# ls
abc.logs  abc1.logs
[root@ip-172-31-87-240 _data]#
```
### mounting non docker area inside container without creating any volume 

```
[ashu@ip-172-31-87-240 python_code]$ docker  run -itd --name t1  -v /home/ashu/ashu-apps/python_code:/mnt/new:ro  ashualp:pycodev1
56e541200e22795fef7324dece4d6106a03fbe0802a574cc6b978cb5414d71d1
[ashu@ip-172-31-87-240 python_code]$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED          STATUS          PORTS     NAMES
56e541200e22   ashualp:pycodev1   "/bin/sh -c 'python3…"   3 seconds ago    Up 2 seconds              t1
ad0eee81f728   alpine             "/bin/sh"                9 minutes ago    Up 9 minutes              ankitac2
58dd9ecb721f   alpine             "/bin/sh"                10 minutes ago   Up 10 minutes             ashuc2
46671b8986eb   alpine             "/bin/sh"                18 minutes ago   Up 18 minutes             ankitac1
efd9830be37d   alpine             "/bin/sh"                21 minutes ago   Up 21 minutes             ashuc1
[ashu@ip-172-31-87-240 python_code]$ docker  exec -it t1 sh 
/ # cd  /mnt/new/
/mnt/new # ls
test.py
/mnt/new # python3  test.py 
Hello world
Hello world
^CTraceback (most recent call last):
  File "/mnt/new/test.py", line 4, in <module>
    time.sleep(5)
KeyboardInterrupt

```





