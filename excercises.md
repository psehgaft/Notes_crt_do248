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
