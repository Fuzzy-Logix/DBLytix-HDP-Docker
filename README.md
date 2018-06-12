# dblytix-hdp

Fuzzy Logix DB Lytix™ is a very rich library of quantitative methods implemented for Library.
Built and tested with the latest version of [Docker](https://docs.docker.com/engine/installation/) on CentOS. Older versions of Docker provided by docker-machine and/or Docker Toolbox will not work.

For support, please [let us know] (www.fuzzylogix.com) (info@fuzzylogix.com) & would get in touch with you if need additional help.


##Project Goals:
Provide a docker environment to run/test DB Lytix™ 

```
To test DB Lytix™, get the user manual & a license file from Fuzzy Logix http://www.fuzzylogix.com

```


##Steps to launch the Hadoop cluster:
1.  To run the cluster:
```
docker-compose -f examples/compose/single-container.yml up
```
or 

```
docker-compose -f examples/compose/multi-container.yml up
```

After a minute or so, you can access Ambari's Web UI at localhost:8080. Default User/PW is admin/admin.



2.  To use the in-hadoop analytical functions, examples are in `DB Lytix™` user manual and an example is here:
```
a.  copy the dblytix.license file to /etc/hadoop/ on each datanode:
	```
	docker exec -it <container ID> /bin/bash
	[root@ambari-server /]# vi /etc/hadoop/dblytix.license
	
	```


b.  connect to hiveserver2 via odbc/jdbc/beeline:
 # beeline -u jdbc:hive2://ambari-server.dev:10000/dblytix -n hive
  0: jdbc:hive2://ambari-server.dev:10000/> select fllinregr('mazdoo.tbllinregr','obsid','varid','num_val','');
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


##Appendix:
These docker images are made  from [HDP] (https://github.com/randerzander/docker-hdp) with pre-installed DB Lytix™, for more info about the [product] (www.fuzzylogix.com):
```
git clone https://github.com/Fuzzy-Logix/DBLytix-HDP-Docker.git
```

Build them locally only if need any customizations:
```
docker-compose -f examples/compose/single-container.yml build
```

or 

```
docker-compose -f examples/compose/multi-container.yml build
```

A successful build looks like:
```
docker-dblytix> docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
dblytix/node            latest              cacb20b1b0d3        15 seconds ago      7.682 GB
dblytix/ambari-server   latest              b0fad41dd49c        15 minutes ago      2.492 GB
dblytix/postgres        latest              ad42250d5c8b        23 minutes ago      320.2 MB
dblytix/postgres        latest              7ee9d2061970        6 weeks ago         275.3 MB
```


Possible to add mode data nodes (copy & add another containers/node,  edit example/compose/multi-container.yml to start those nodes)

Possible to work with any docker network.  By default used docker-bridge.
Thus, can have container IP either as host/private/public & can work with odbc/jdbc hive connector.
