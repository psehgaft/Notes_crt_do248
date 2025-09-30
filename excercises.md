# Install JbosEAP
sudo java -jar jboss-eap-7.4.0-installer.jar
sudo chown -R jboss:jboss /opt/jboss-eap-7.4
sudo -u jboss /opt/jboss-eap-7.4/bin/standalone.sh


# Jboss EAP Stand Alone
sudo -u jboss  ./standalone.sh -Djboss.server.base.dir=/home-dir/jboss/standalone/
sudo -u jboss  cp -r configuration deployments lib /home-dir/jboss/standalone2
sudo -u jboss /opt/jboss-eap-7.4/bin/standalone.sh -Djboss.server.base.dir=/opt/jboss/standalone2/ -Djboss.socket.binding.port-offset=10000

# App

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

# Jboss EAP Domain

sudo cp -r /home-dir/jboss/domain /home-dir/
sudo chown -R jboss:jboss /home-dir/domain
sudo -u jboss vim /home-dir/home-dir/configuration/host-master.xml
cd /home-dir/jboss/bin
sudo -u jboss ./domain.sh -Djboss.domain.base.dir=/home-dir/domain/ --host-config=host-master.xml

## Server A
ssh servera
ssudo cp -r /home-dir/jboss/domain /home-dir/
sudo chown -R jboss:jboss /home-dir/domain
EDIT: /opt/domain/configuration/host-slave.xml
´´´
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
´´´
cd /home-dir/jboss/bin
sudo -u jboss ./domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-slave.xml -Djboss.domain.master.address=x.y.w.0 #[Domain]


## Server b
ssh serverb
ssudo cp -r /home-dir/jboss/domain /home-dir/
sudo chown -R jboss:jboss /home-dir/domain
EDIT: /opt/domain/configuration/host-slave.xml
´´´
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
´´´
cd /home-dir/jboss/bin
sudo -u jboss ./domain.sh -Djboss.domain.base.dir=/opt/domain/ --host-config=host-slave.xml -Djboss.domain.master.address=x.y.w.0 #[Domain]
