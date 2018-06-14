# DBLytix-HDP-Docker
DB Lytix™ is Fuzzy Logix’s flagship in-database analytics product, achieving high-performance analytics.  

The DB Lytix™ docker images are built on centos 6 base image and installed HDP & a pre-installed DB Lytix™, for more info about the [product](www.fuzzylogix.com) and support, please [let us know](www.fuzzylogix.com).

Docker host machine used is CentOS 7 with the latest version of [Docker](https://docs.docker.com/engine/installation/).  Older versions of Docker provided by docker-machine and/or Docker Toolbox will not work.


## Project Goals:
Provide a docker environment to run/test DB Lytix™ 


## Pre-requisits:
1.  Install docker, docker-compose on host machine.
	
2.  To test DB Lytix™, get the user manual & a license file from [Fuzzy Logix](www.fuzzylogix.com) / info@fuzzylogix.com / HadoopTeam@fuzzylogix.com for license file & user manual


## Steps:
1.  To run the cluster:
```
git clone https://github.com/Fuzzy-Logix/DBLytix-HDP-Docker.git
```

If want to customize (add datanode / mount voulme / change docker network) edit the file multi-container.yml then start the containers as:
```
docker-compose -f examples/compose/multi-container.yml up
```

Then Ambari Web UI will be accessible at localhost:8080. Default User/PW is admin/admin.


2.  To use the in-hadoop analytical functions, examples are in `DB Lytix™` user manual and an example is here:

a.  copy the dblytix.license file to host machine & copy it to datanodes as usinf docker cp command as:
```
See if you have put the license file in host machine:
[root@localhost ~]# ls -ltr dblytix.license
-rw-r--r--. 1 root root 4 Jun 13 10:17 dblytix.license

Find the container ID as:
[root@localhost ~]# docker ps
CONTAINER ID	IMAGE           COMMAND                  CREATED       	STATUS         PORTS                       NAMES
1c0d588be4c0    dblytix/worker  "/bin/sh -c /start.sh"   19 minutes ago Up 19 minutes  0.0.0.0:6667->6667/tcp...   compose_dn0.dev_1
d7dc4feb2f0f    dblytix/ambari	"/bin/sh -c /start.sh"   19 minutes ago Up 19 minutes  0.0.0.0:8080->8080/tcp...   compose_ambari-server.dev_1

Copy the license file to containers as:		
	[root@localhost ~]# docker cp dblytix.license 1c0d588be4c0:/etc/hadoop 
	[root@localhost ~]# docker cp dblytix.license d7dc4feb2f0f:/etc/hadoop 
```

b.  connect to hiveserver2 via odbc/jdbc/beeline and run DB Lytix™ functions:
```
  To login to a node (also, can connect to HS2 from host or external machines as per docker network configured):
 # docker exec -it d7dc4feb2f0f /bin/bash

  Connect to HS2 via beeline:
 # beeline -u jdbc:hive2://ambari-server.dev:10000/dblytix -n hive
 
  Call DB Lytix™ functions as SQL/HQL:
  0: jdbc:hive2://ambari-server.dev:10000/> SELECT FLLinRegr('mazdoo.tbllinregr','obsid','varid','num_val','');
  INFO  : Session is already open
  INFO  : Dag name: select fllinregr('maz...varid','num_val','')(Stage-1)
  INFO  : Status: Running (Executing on YARN cluster with App id application_1518679384209_0001)
  
  --------------------------------------------------------------------------------
          VERTICES      STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED
  --------------------------------------------------------------------------------
  Map 1 ..........   SUCCEEDED      1          1        0        0       0       0
  --------------------------------------------------------------------------------
  VERTICES: 01/01  [==========================>>] 100%  ELAPSED TIME: 315.07 s
  --------------------------------------------------------------------------------
  +-------------+--+
  | analysisid  |
  +-------------+--+
  | FL495542    |
  +-------------+--+
  1 row selected (320.778 seconds)
  0: jdbc:hive2://ambari-server.dev:10000/>
```


## Appendix:
If want to customize by editing the Dockerfile, edit the files & build:
```
docker-compose -f examples/compose/multi-container.yml build
```

A successful build looks like:
```
[root@localhost ~]# docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
dblytix/worker          latest              cacb20b1b0d3        15 seconds ago      7.682 GB
dblytix/ambari-server   latest              b0fad41dd49c        15 minutes ago      2.492 GB
```


By default it uses docker-bridge.  Possible to work with any docker network.  
Thus, can have container IP either as host/private/public & can work with odbc/jdbc/beeline hive connector.


Fuzzy Logix reqularly releases the new products and publishes latest docker images, so to work on latest dblytix image, clear the previously pulled image present locally.
