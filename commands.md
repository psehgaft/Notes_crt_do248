# Usefull commands

 ```sh

$  ./domain.sh 
-Djboss.domain.base.dir=/home/student/JB248/labs/domain/machine1/domain/ 
--host-config=host-master.xml

$  ./domain.sh 
-Djboss.domain.base.dir=/home/student/JB248/labs/host/machine2/domain/ 
--host-config=host-slave.xml 
-Djboss.domain.master.address=172.25.250.254

module add --name=com.mysql 
--resources=/opt/jboss-eap-7.0/bin/mysql-connector-java.jar 
--dependencies=javax.api,javax.transaction.api

/profile=default/subsystem=datasources/jdbc-driver=mysql:add(
           driver-module-name=com.mysql,
           driver-name=mysql

/profile=default/subsystem=datasources/jdbc-driver=mysql:add(\
           driver-module-name=com.mysql,\
           driver-name=mysql\

./standalone.sh \
  --server-config=standalone-full-ha.xml \
-Djboss.server.base.dir=/home/student/JB248/labs/jgroups-cluster1 \ 
-Djboss.bind.address=172.25.250.254 \
-Djboss.bind.address.private=172.25.250.254 \
-Djboss.node.name=jgroups-cluster1

keytool -genseckey -alias vault \
-keyalg AES -storetype jceks -keysize 128 \
-keystore /home/student/vault.keystor

keytool -genkeypair -alias appserver \
-storetype jks -keyalg RSA -keysize 2048 -keystore identity.jks

./standalone.sh \
-Djboss.server.base.dir=/home/student/JB248/labs/jgroups-cluster1/ \
-Djboss.node.name=jgroups-cluster1 \
-c standalone-full-ha.xml

./standalone.sh --server-config=standalone-ha.xml \
-Djboss.server.base.dir=/home/student/JB248/labs/config-lb/server1 \ 
-Djboss.bind.address=172.25.250.254 -Djboss.socket.binding.port-offset=100 \
-Djboss.node.name=server1

$ ./standalone.sh -Djboss.server.base.dir=/path/to/base/directory \ -Djboss.home.dir=/path/to/home/directory

$ ./standalone.sh -Djboss.socket.binding.port-offset=10000

$ ./standalone.sh ---server-config standalone-full.xml

$ ./standalone.sh --c standalone-full.xml

 ```
