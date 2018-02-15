# dblytix-hdp

Fuzzy Logix DB Lytix™ is a very rich library of quantitative methods implemented for Library.
Built and tested with the latest version of [Docker](https://docs.docker.com/engine/installation/) on CentOS. Older versions of Docker provided by docker-machine and/or Docker Toolbox will not work.

For support, please [let us know](HadoopTeam@fuzzylogix.com) & would get in touch with you if need additional help.

##Project Goals:
1. Provide a reusable base with which to experiment DBLytix with HDP Hadoop ecosystem, and its configs w/o VMs
2. Provide a pseudo-distributed Hadoop environment, because single node setups make bad assumptions about how software works in multi-node clusters.
3. Provide an excuse to learn & play with Docker

These containers are build from [HDP] (https://github.com/randerzander/docker-hdp) and installed in-Hadoop analytical library [DBLytix] (www.fuzzylogix.com) to DockerHub, so need building them locally only if need any customizations:
```
docker-compose -f examples/compose/single-container.yml build
```

A successful build looks like:
```
docker-hdp> docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hdp/node            latest              cacb20b1b0d3        15 seconds ago      7.682 GB
hdp/ambari-server   latest              b0fad41dd49c        15 minutes ago      2.492 GB
hdp/postgres        latest              ad42250d5c8b        23 minutes ago      320.2 MB
centos              6                   cf2c3ece5e41        3 weeks ago         194.6 MB
postgres            latest              7ee9d2061970        6 weeks ago         275.3 MB
```

##Running HDP 2.6:
To run 3 containers (postgres, ambari-server, and a "single container HDP cluster"):
```
docker-compose -f examples/compose/single-container.yml up
```

After a minute or so, you can access Ambari's Web UI at localhost:8080. Default User/PW is admin/admin.

##Using DB Lytix™ functions, described in user manual and an example here:

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


##Helpful Hints:
If you HDFS having issues starting up/not leaving SafeMode, it's probably because docker-compose is re-using containers from a previous run.

To start with fresh containers, before each run do:
```
docker-compose -f examples/compose/multi-container.yml rm
Going to remove compose_ambari-server.dev_1, compose_dn0.dev_1, compose_master0.dev_1, compose_postgres.dev_1
Are you sure? [yN] y
Removing compose_ambari-server.dev_1 ... done
Removing compose_dn0.dev_1 ... done
Removing compose_master0.dev_1 ... done
Removing compose_postgres.dev_1 ... done
```

Docker has storage space problems, adding the following to your ~/.bash_profile and restarting terminal:
```
function docker-cleanup(){
 # remove untagged images  
 docker rmi $(docker images | grep none | awk '{ print $3}')
 # remove unused volumes  
 docker volume rm $(docker volume ls -q )  
 # `shotgun` remove unused networks
 docker network rm $(docker network ls | grep "_default")   
 # remove stopped + exited containers, I skip Exit 0 as I have old scripts using data containers.
 docker rm -v $(docker ps -a | grep "Exit [0-255]" | awk '{ print $1 }')
}
```

Run "docker-cleanup" if you run into Docker errors or "No space left on device" issues inside containers.

Since Hadoop UIs often link to hostnames, add the following to your hosts file:
```
echo "127.0.0.1 ambari-server ambari-server.dev" >> /etc/hosts
echo "127.0.0.1 master0 master0.dev" >> /etc/hosts
echo "127.0.0.1 dn0 dn0.dev" >> /etc/hosts
```

TODO:
1. Steps for using latest Docker 1.12 Swarm & Compose on multiple hosts
