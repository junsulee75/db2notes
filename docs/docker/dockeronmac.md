---
id: dockeronmac
title: Install Db2 docker image on mac
sidebar_label: Install Db2 docker image on mac
---

# Install Db2 docker image on mac

This is the test example output implementing [Installing the Db2 Community Edition Docker image on macOS systems](https://www.ibm.com/docs/en/db2/11.5?topic=system-macos).    
In history, there had been a binary installation for Mac OS but it had to deal with `root` interaction etc.   
Nowadays, it is easier to have Db2 test environment on your mac using this step.    
I assume you have Docker installed already.    
I used Docker version `Docker version 20.10.5` for this test on Mac OS Big Sur.         


## 1.Create a directory to install Docker DB2

```
jsmacpro15touch:dockerdb2 kr050496$ pwd
/Users/kr050496/software/dockerdb2
```

## 2.Docker login.

```
jsmacpro15touch:dockerdb2 kr050496$ sudo docker login
Password: <<Your Mac OS user password>>
Authenticating with existing credentials...
Login Succeeded
```


## 3. Download Docker image

```
jsmacpro15touch:dockerdb2 kr050496$ docker pull ibmcom/db2
Using default tag: latest
latest: Pulling from ibmcom/db2
b855b55fea8a: Pull complete 
424ac630d77d: Pull complete 
5387ab5962e4: Pull complete 
8ce2d0d5d5ee: Pull complete 
96c1f80dab85: Pull complete 
33283ba45571: Pull complete 
182a930a0703: Pull complete 
c3af749e31de: Pull complete 
4345966eef42: Pull complete 
e9b72a3335ed: Pull complete 
e71fc1b31d43: Pull complete 
221a3dbbe132: Pull complete 
Digest: sha256:77da4492bf18c49a1012aa6071a16aee0039dca9c0a2a492345b6b030714a54f
Status: Downloaded newer image for ibmcom/db2:latest
docker.io/ibmcom/db2:latest

jsmacpro15touch:dockerdb2 kr050496$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ibmcom/db2   latest    e304e217603b   3 weeks ago   2.78GB

jsmacpro15touch:dockerdb2 kr050496$ docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```


## 4. Create a environment variable file. 

```
jsmacpro15touch:dockerdb2 kr050496$ cat .env_list
LICENSE=accept
DB2INSTANCE=db2inst1
DB2INST1_PASSWORD=password
DBNAME=testdb
BLU=false
ENABLE_ORACLE_COMPATIBILITY=false
UPDATEAVAIL=NO
TO_CREATE_SAMPLEDB=false
REPODB=false
IS_OSXFS=true
PERSISTENT_HOME=true
HADR_ENABLED=false
ETCD_ENDPOINT=
ETCD_USERNAME=
ETCD_PASSWORD=
```

## 5. Start the DB2 docker image

```
jsmacpro15touch:dockerdb2 kr050496$  docker run -h jsdb2docker --name dockerdb2 --restart=always --detach --privileged=true -p 50000:50000 --env-file .env_list -v /Users/kr050496/software/dockerdb2:/database ibmcom/db2
54211875b8e859f3fcc8a07ae6be277cbf112420ee77bf7f9ce920659d776f25

jsmacpro15touch:dockerdb2 kr050496$ docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS         PORTS                                                          NAMES
c1c2dc7ce629   ibmcom/db2   "/var/db2_setup/lib/…"   10 seconds ago   Up 9 seconds   22/tcp, 55000/tcp, 60006-60007/tcp, 0.0.0.0:50000->50000/tcp   dockerdb2

jsmacpro15touch:dockerdb2 kr050496$ docker container ls
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS                                                          NAMES
c1c2dc7ce629   ibmcom/db2   "/var/db2_setup/lib/…"   27 seconds ago   Up 26 seconds   22/tcp, 55000/tcp, 60006-60007/tcp, 0.0.0.0:50000->50000/tcp   dockerdb2
```


## 6. Enter the DB2 conatiner.

```
jsmacpro15touch:dockerdb2 kr050496$ docker exec -it dockerdb2 bash 
[root@jsdb2docker /]# whoami 
root
[root@jsdb2docker /]# id
uid=0(root) gid=0(root) groups=0(root)

[root@jsdb2docker /]# su - db2inst1
Last login: Fri Aug  6 00:54:39 UTC 2021

[db2inst1@jsdb2docker ~]$ db2_ps
Node 0
     UID        PID       PPID    C     STIME     TTY     TIME CMD
    root      15305          1    0     00:54       ? 00:00:00 db2wdog 0 [db2inst1]
db2inst1      15307      15305    2     00:54       ? 00:00:01 db2sysc 0
    root      15313      15305    0     00:54       ? 00:00:00 db2ckpwd 0
    root      15314      15305    0     00:54       ? 00:00:00 db2ckpwd 0
    root      15315      15305    0     00:54       ? 00:00:00 db2ckpwd 0
db2inst1      15317      15305    0     00:54       ? 00:00:00 db2vend (PD Vendor Process - 1) 0
db2inst1      15325      15305    0     00:54       ? 00:00:00 db2acd 0 ,0,0,0,1,0,0,00000000,0,0,0000000000000000,0000000000000000,00000000,00000000,00000000,00000000,00000000,00000000,0000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,0000000040000000,0000000000000000,0000000000000000,1,0,0,,,,,a89e05,14,1e014,2,0,1,0000000000041fc0,0x240000000,0x240000000,1600000,2,2,1e
[db2inst1@jsdb2docker ~]$ db2 list db directory

 System Database Directory

 Number of entries in the directory = 1

Database 1 entry:

 Database alias                       = TESTDB
 Database name                        = TESTDB
 Local database directory             = /database/data
 Database release level               = 15.00
 Comment                              =
 Directory entry type                 = Indirect
 Catalog database partition number    = 0
 Alternate server hostname            =
 Alternate server port number         =

```

Enjoy !!   

