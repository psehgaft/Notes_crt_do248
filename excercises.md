# Install JbosEAP

 ```sh
sudo java -jar jboss-eap-7.4.0-installer.jar
sudo chown -R jboss:jboss /opt/jboss-eap-7.4
sudo -u jboss /opt/jboss-eap-7.4/bin/standalone.sh
 ```

# Jboss EAP Stand Alone

 ```sh
sudo -u jboss  ./standalone.sh -Djboss.server.base.dir=/home-dir/jboss/standalone/
sudo -u jboss  cp -r configuration deployments lib /home-dir/jboss/standalone2
sudo -u jboss /opt/jboss-eap-7.4/bin/standalone.sh -Djboss.server.base.dir=/opt/jboss/standalone2/ -Djboss.socket.binding.port-offset=10000
 ```

# App

 ```sh
./standalone.sh -Djboss.server.base.dir=/home-dir/deploy/standalone/
/jboss-cli.sh -c
cd /socket-binding-group=standard-sockets
cd socket-binding=http
write-attribute (name=port,value=8081)
:read-resource
cd /
:reload
-----
cd /subsystem=logging/console-handler=CONSOLE
:write-attribute (name=level,value=INFO)
:read-resource
---

deploy /home-dir/deploy/app.war
```

# Jboss EAP Domain

```sh
sudo cp -r /home-dir/jboss/domain /home-dir/
sudo chown -R jboss:jboss /home-dir/domain
sudo -u jboss vim /home-dir/home-dir/configuration/host-master.xml
cd /home-dir/jboss/bin
sudo -u jboss ./domain.sh -Djboss.domain.base.dir=/home-dir/domain/ --host-config=host-master.xml
 ```

## Server A

```sh
ssh servera
ssudo cp -r /home-dir/jboss/domain /home-dir/
sudo chown -R jboss:jboss /home-dir/domain
```

EDIT: /opt/domain/configuration/host-slave.xml
 ```config.xml
<host name="servera" xmlns="urn:jboss:domain:16.0">
<host name="servera"  xmlns="urn:jboss:domain:16.0">
...ouput omitted...
    <management>
        <security-realms>
            <security-realm name="ManagementRealm">
                <server-identities>
                    <secret value="cmVkaGF0MTIz"/>
                </server-identities>
...ouput omitted...
<interface name="public">
   <inet-address value="${jboss.bind.address:x.y.w.1}"/>
</interface>
...ouput omitted...
<interface name="management">
   <inet-address value="${jboss.bind.address.management:x.y.w.1}"/>
</interface>
 ```

 ```sh
cd /home-dir/jboss/bin
sudo -u jboss ./domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-slave.xml -Djboss.domain.master.address=x.y.w.0 #[Domain]
 ```

## Server b

 ```sh
ssh serverb
ssudo cp -r /home-dir/jboss/domain /home-dir/
sudo chown -R jboss:jboss /home-dir/domain
 ```

EDIT: /opt/domain/configuration/host-slave.xml
 ```config.xml
<host name="serverb" xmlns="urn:jboss:domain:16.0">
<host name="serverb"  xmlns="urn:jboss:domain:16.0">
...ouput omitted...
    <management>
        <security-realms>
            <security-realm name="ManagementRealm">
                <server-identities>
                    <secret value="cmVkaGF0MTIz"/>
                </server-identities>
...ouput omitted...
<interface name="public">
   <inet-address value="${jboss.bind.address:x.y.w.2}"/>
</interface>
...ouput omitted...
<interface name="management">
   <inet-address value="${jboss.bind.address.management:x.y.w.2}"/>
</interface>
 ```

# Deploy App domain

 ```sh
sudo -u jboss /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-master.xml
 ```

On SeverA and ServerB

 ```sh
cd /home-dir/jboss/bin
sudo -u jboss /home-dir/jboss/domain -Djboss.domain.base.dir=/home-dir/domain  --host-config=host-master.xml
sudo -u jboss /home-dir/jboss//bin/domain.sh -Djboss.domain.base.dir=/home-dir/domain/ -Djboss.domain.master.address=x.y.w.0  --host-config=host-slave.xml
 ```

# Configuring Servers in a Managed Domain

 ```sh
sudo -u jboss /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-master.xml
sudo -u jboss /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 --host-config=host-slave.xml
sudo -u jboss /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 --host-config=host-slave.xml
sudo -u jboss /opt/jboss-eap-7.4/bin/jboss-cli.sh --connect --controller=172.25.250.9:9990
 ```

 ```jboss
/server-group=Group1:add (profile=full,socket-binding-group=full-sockets)
/server-group=Group2:add (profile=full,socket-binding-group=full-sockets)
server-group=Group1:read-resource
/server-group=Group2:read-resource
/host=servera/server-config=servera.1:add(auto-start=true,group=Group1,socket-binding-port-offset=0)
/host=servera/server-config=servera.2:add(auto-start=true,group=Group2,socket-binding-port-offset=100)
/host=serverb/server-config=serverb.1:add(auto-start=true,group=Group1,socket-binding-port-offset=0)
/host=serverb/server-config=serverb.2:add(auto-start=true,group=Group2,socket-binding-port-offset=100)
/server-group=Group1:start-servers(blocking=true)
/server-group=Group2:start-servers(blocking=true)
deploy /tmp/helloworld-mdb.war --server-groups=Group1
 ```

# Configuring Data Sources

 ```sh
sudo -u jboss /opt/jboss-eap-7.4/bin/jboss-cli.sh --command="module add --name=com.mariadb --resources=/home/jboss/mariadb-java-client-3.2.0.jar --dependencies=javaee.api,sun.jdk,ibm.jdk,javax.api,javax.transaction.api"

ssh jboss@servera '/opt/jboss-eap-7.4/bin/jboss-cli.sh --command="module add --name=com.mariadb --resources=/home/jboss/mariadb-java-client-3.2.0.jar --dependencies=javaee.api,sun.jdk,ibm.jdk,javax.api,javax.transaction.api"'

ssh jboss@serverb '/opt/jboss-eap-7.4/bin/jboss-cli.sh --command="module add --name=com.mariadb --resources=/home/jboss/mariadb-java-client-3.2.0.jar --dependencies=javaee.api,sun.jdk,ibm.jdk,javax.api,javax.transaction.api"'

sudo -u jboss /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-master.xml

ssh -t jboss@servera /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 --host-config=host-slave.xml

ssh -t jboss@serverb /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 --host-config=host-slave.xml

sudo -u jboss /opt/jboss-eap-7.4/bin/jboss-cli.sh --connect --controller=172.25.250.9:9990

/profile=full/subsystem=datasources/jdbc-driver=mariadb:add(driver-name=mariadb,driver-module-name=com.mariadb)

data-source add --profile=full --name=kitchensink --driver-name=mariadb --jndi-name=java:jboss/datasources/KitchensinkQuickstartDS --connection-url=jdbc:mariadb://172.25.250.9:3306/lab_db --user-name=developer --password=redhat123

deploy /tmp/kitchensink.war --server-groups=Group1

 ```

# Configuring the Logging Subsystem

 ```sh
sudo -u jboss /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-master.xml

ssh -t jboss@servera /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 --host-config=host-slave.xml

ssh -t jboss@serverb /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 --host-config=host-slave.xml

udo -u jboss /opt/jboss-eap-7.4/bin/jboss-cli.sh --connect --controller=172.25.250.9:9990

/profile=full/subsystem=logging/ size-rotating-file-handler=kitchensink_LOG_HANDLER:add (file={"relative-to"=>"custom.log.dir", "path"=>"${jboss.server.name}/kitchensink.log"}, enabled=true, append=true, autoflush=true, rotate-size=1m, max-backup-index=5,level=DEBUG)

/profile=full/subsystem=logging /size-rotating-file-handler=kitchensink_LOG_HANDLER:read-resource

/profile=full/subsystem=logging/ logger=org.jboss.as.quickstarts:add (category=org.jboss.as.quickstarts,level=DEBUG,handlers=["kitchensink_LOG_HANDLER"])

/profile=full/subsystem=logging/ root-logger=ROOT:write-attribute(name=level,value=WARN)

deploy /tmp/kitchensink.war --server-groups=Group1
ssh jboss@servera tail /var/log/jboss/servera.1/kitchensink.log
ssh jboss@serverb tail /var/log/jboss/serverb.1/kitchensink.log

 ```

# Configuring the Messaging Subsystem

 ```sh
sudo -u jboss /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain --host-config=host-master.xml
ssh -t jboss@servera /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 -bprivate=172.25.250.10 --host-config=host-slave.xml
ssh -t jboss@serverb /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain/ -Djboss.domain.master.address=172.25.250.9 -bprivate=172.25.250.11 --host-config=host-slave.xml

sudo -u jboss /opt/jboss-eap-7.4/bin/jboss-cli.sh --connect --controller=172.25.250.9:9990
/profile=full-ha/subsystem=logging/root-logger=ROOT:write-attribute(name=level,value=INFO)
cd /host=servera/server=servera.1/subsystem=messaging-activemq

cd /profile=full-ha/subsystem=messaging-activemq/server=default
./pooled-connection-factory=custom:add(connectors=[in-vm], entries=[java:/jms/CustomCF])
/jms-queue=TestQueue:add(entries=[java:/jms/queue/AD248TestQueue])

deploy /tmp/mdb-client.war --server-groups=Group1
deploy /tmp/helloworld-mdb.jar --server-groups=Group1
ssh servera tail /opt/domain/servers/servera.1/log/server.log
curl --data "count=5" --data "label=first" --data "message=test message body" http://172.25.250.10:8080/mdb-client/queue
ssh servera tail /opt/domain/servers/servera.1/log/server.log

/host=servera/server=servera.1/subsystem=messaging-activemq/server=default/jms-queue=TestQueue:read-attribute(name=messages-added)
/host=serverb/server=serverb.1/subsystem=messaging-activemq/server=default/jms-queue=TestQueue:read-attribute(name=messages-added)

 ```

# Securing JBoss EAP

 ```sh
cd /opt/jboss-eap-7.4/bin
sudo -u jboss ./domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-master.xml
ssh -t jboss@servera /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain --host-config=host-slave.xml -Djboss.domain.master.address=172.25.250.9 -bprivate=172.25.250.10
ssh -t jboss@serverb /opt/jboss-eap-7.4/bin/domain.sh -Djboss.domain.base.dir=/opt/domain --host-config=host-slave.xml -Djboss.domain.master.address=172.25.250.9 -bprivate=172.25.250.11
cd /opt/jboss-eap-7.4/bin
sudo -u jboss ./jboss-cli.sh --connect --controller=172.25.250.9:9990
data-source add --profile=full --name=bksecurity-ds --driver-name=mariadb --jndi-name=java:/jboss/datasources/bksecurity-ds --connection-url=jdbc:mariadb://172.25.250.9:3306/bksecurity --user-name=bkadmin --password=redhat123
/host=servera/server=servera.1/subsystem=datasources/data-source=bksecurity-ds:test-connection-in-pool

/profile=full/subsystem=security/security-domain=ad248-sd:add
/profile=full/subsystem=security/security-domain=ad248-sd/authentication=classic:add

/profile=full/subsystem=security\
/security-domain=ad248-sd/authentication=\
classic/login-module=database:add( \
  code=Database, \
  flag=required, \
  module-options=[ \
    ("dsJndiName"=>"java:jboss/datasources/bksecurity-ds"), \
    ("principalsQuery"=>"select password from users where username=?"), \
    ("rolesQuery"=>"select role, 'Roles' from roles where username=?"), \
    ("hashAlgorithm"=>"SHA-256"), \
    ("hashEncoding"=>"base64") \
  ])

 :reload-servers(blocking=true)

/profile=full/subsystem=security\
/security-domain=ad248-sd/authentication=classic/\
login-module=database:read-resource

deploy /tmp/messaging-client-secure.war --server-groups=Group1

...

sudo -u jboss keytool \
-genseckey -alias vault \
-keyalg AES -storetype jceks -keysize 128 \
-keystore /opt/jboss-eap-7.4/vault.keystore

sudo scp /opt/jboss-eap-7.4/vault.keystore \
servera:/opt/jboss-eap-7.4

sudo scp /opt/jboss-eap-7.4/vault.keystore \
serverb:/opt/jboss-eap-7.4

ssh root@servera \
chown jboss:jboss /opt/jboss-eap-7.4/vault.keystore

ssh root@serverb \
chown jboss:jboss /opt/jboss-eap-7.4/vault.keystore

 cd /opt/jboss-eap-7.4/bin

sudo -u jboss ./vault.sh

sudo scp /opt/jboss-eap-7.4/VAULT.dat \
servera:/opt/jboss-eap-7.4

sudo scp /opt/jboss-eap-7.4/VAULT.dat \
serverb:/opt/jboss-eap-7.4

ssh root@servera \
chown jboss:jboss /opt/jboss-eap-7.4/VAULT.dat

ssh root@serverb \
chown jboss:jboss /opt/jboss-eap-7.4/VAULT.dat

sudo -u jboss ./jboss-cli.sh --connect \
--controller=172.25.250.9:9990

/host=master/core-service=vault:add\
(vault-options=[("KEYSTORE_URL" => "/opt/jboss-eap-7.4/vault.keystore"),\
("KEYSTORE_PASSWORD" => "MASK-31x/z0Xn83H4JaL0h5eK/N"),\
("KEYSTORE_ALIAS" => "vault"),("SALT" => "12345678"),\
("ITERATION_COUNT" => "50"),\
("ENC_FILE_DIR" => "/opt/jboss-eap-7.4/")])

/host=servera/core-service=vault:add\
(vault-options=[("KEYSTORE_URL" => "/opt/jboss-eap-7.4/vault.keystore"),\
("KEYSTORE_PASSWORD" => "MASK-31x/z0Xn83H4JaL0h5eK/N"),\
("KEYSTORE_ALIAS" => "vault"),("SALT" => "12345678"),\
("ITERATION_COUNT" => "50"),\
("ENC_FILE_DIR" => "/opt/jboss-eap-7.4/")])

/host=serverb/core-service=vault:add\
(vault-options=[("KEYSTORE_URL" => "/opt/jboss-eap-7.4/vault.keystore"),\
("KEYSTORE_PASSWORD" => "MASK-31x/z0Xn83H4JaL0h5eK/N"),\
("KEYSTORE_ALIAS" => "vault"),("SALT" => "12345678"),\
("ITERATION_COUNT" => "50"),\
("ENC_FILE_DIR" => "/opt/jboss-eap-7.4/")])

:reload-servers(blocking=true)

/profile=full/subsystem=datasources/\
data-source=bksecurity-ds:write-attribute(name=enabled,value=false)

:reload-servers(blocking=true)

/profile=full/subsystem=datasources/\
data-source=bksecurity-ds:write-attribute\
(name=password,value=${VAULT::bksecurity::password::1})

/profile=full/subsystem=datasources/\
data-source=bksecurity-ds:write-attribute(name=enabled,value=true)

/host=servera/server=servera.1/\
subsystem=datasources/data-source=bksecurity-ds:test-connection-in-pool

/profile=full/subsystem=messaging-activemq/\
server=default/security-setting=#:remove

/profile=full/subsystem=messaging-activemq/\
server=default/security-setting=#:add

/profile=full/subsystem=messaging-activemq/\
server=default/security-setting=#/role=admin:add

/profile=full/subsystem=messaging-activemq\
/server=default/security-setting=#\
/role=admin:write-attribute(name=consume, value=true)

/profile=full/subsystem=messaging-activemq\
/server=default/security-setting=#\
/role=admin:write-attribute(name=manage, value=true)

/profile=full/subsystem=messaging-activemq\
/server=default/security-setting=#\
/role=admin:write-attribute(name=send, value=true)

 /profile=full/subsystem=messaging-activemq\
/server=default:write-attribute(name=security-domain,value=ad248-sd)

:reload-servers(blocking=true)

deploy /tmp/messaging-mdb-secure.jar \
--server-groups=Group1

ssh jboss@servera \
cat /opt/domain/servers/servera.1/log/server.log

/profile=full/subsystem=messaging-activemq\
/server=default/security-setting=#\
/role=admin:write-attribute(name=consume, value=false)

/profile=full/subsystem=messaging-activemq\
/server=default/security-setting=#\
/role=admin:write-attribute(name=send, value=false)

 ```

# Configuring the Java Virtual Machine

 ```sh
udo -u jboss /opt/jboss-eap-7.4/bin/domain.sh \
-Djboss.domain.base.dir=/opt/domain/ --host-config=host-master.xml

ssh -t jboss@servera /opt/jboss-eap-7.4/bin/domain.sh \
-Djboss.domain.base.dir=/opt/domain/ \
-Djboss.domain.master.address=172.25.250.9 \
--host-config=host-slave.xml

ssh -t jboss@serverb /opt/jboss-eap-7.4/bin/domain.sh \
-Djboss.domain.base.dir=/opt/domain/ \
-Djboss.domain.master.address=172.25.250.9 \
--host-config=host-slave.xml

sudo -u jboss \
/opt/jboss-eap-7.4/bin/jboss-cli.sh \
--connect --controller=172.25.250.9:9990

/server-group=Group1:\
stop-servers(blocking=true)

/server-group=Group2:\
stop-servers(blocking=true)

/host=servera\
/jvm=default:write-attribute(name=heap-size,value=512m)

/host=servera\
/jvm=default:write-attribute(name=max-heap-size,value=512m)

/server-group=Group1\
/jvm=group1-jvm:add\
(heap-size=512m,max-heap-size=1024m,jvm-options=["-XX:+UseShenandoahGC"])

/host=serverb\
/server-config=serverb.2\
/jvm=serverb.2-jvm:add\
(heap-size=1024m,max-heap-size=1024m,\
jvm-options=["-XX:+AggressiveOpts","-server"])

/server-group=Group1\
:start-servers(blocking=true)

/server-group=Group2\
:start-servers(blocking=true)

ssh jboss@servera ps -efa|grep servera
ssh jboss@serverb ps -efa|grep serverb

/server-group=Group1:stop-servers(blocking=true)
/server-group=Group2:stop-servers(blocking=true)

/host=servera:shutdown()
/host=serverb:shutdown()

```

#  Configuring the Web Subsystem


