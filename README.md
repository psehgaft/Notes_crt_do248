# Notes_crt_do248

## Install and manage JBoss EAP

```sh
java -jar [instalation.jar file]
```

- Examples
- Logs
- DB Module Postgress
- Username/Password

## Configure JBoss EAP domains, hosts and servers

```conf
# Specify options to pass to the Java VM.
#
if [ "x$JAVA_OPTS" = "x" ]; then
 JAVA_OPTS="-Xms1303m -Xmx1303m -Xss128k -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m ..."
```

```sh
/host=host3/server-config=serverX \
 /jvm=serverX-jvm:add \
 (heap-size=512m,max-heap-size=1500m, \
 jvm-options=["-server","-XX:ParallelGCThreads=4"])
```

### File
- host-master.xml
  - Change name
  - Management IP
 
 ```xml
<host name="NAME" xmlns="urn:jboss:domain:16.0">
...
<interfaces>
    <interface name="management">
        <inet-address value="${jboss.bind.address.management:w.x.y.a}"/>
    </interface>
</interfaces>
 ```

- host-slave.xml
  - Change name
  - Management IP
  - Bind IP
 ```xml
<host name="NAME" xmlns="urn:jboss:domain:16.0">
...
    <management>
        <security-realms>
            <security-realm name="ManagementRealm">
                <server-identities>
                    <secret value="cmVkaGF0MTIz"/>
                </server-identities>
...
-Djboss.domain.master.address=w.x.y.a
...
<interface name="public">
   <inet-address value="${jboss.bind.address:w.x.y.b}"/>
</interface>
<interfaces>
    <interface name="management">
        <inet-address value="${jboss.bind.address.management:w.x.y.b}"/>
    </interface>
</interfaces>
 ```


### WebConsole
- ServerGroup
  - Instance

## Configure JBoss EAP to support clustered and HA operation
- Using HA  profile

## Configure JBoss Web Connectors

```sh

./jboss-cli.sh -c

/subsystem=undertow/server=default-server/ajp-listener=ajp:add(socket-binding=ajp)

/subsystem=undertow/server=default-server/http-listener=default:write-attribute(name=max-connections, value=200)

reload
```

## Manage applications


```sh
/subsystem=undertow/server=default-server/host=default-host:writeattribute(name=default-web-module,value=myapp.war ) 
```

## Files
- Context

## Configure JBoss EAP security
- Security Domain

```sh
/subsystem=security/security-domain=bksecurity-domain:add(cache-type=default)

/subsystem=security/security-domain= \
   db-domain/authentication=classic:add \
   (login-modules=[{"code"=>"Database", "flag"=>"required", \
   "module-options"=>[("dsJndiName"=>"java:jboss/datasources/test-ds"), \
   ("principalsQuery"=>"SELECT password FROM users WHERE username = ?"), \
   ("rolesQuery"=>"SELECT role, 'Roles' FROM roles WHERE username = ?"), \
   ("hashAlgorithm"=>"SHA-256"),("hashEncoding"=>"base64")]}])
```

```xml
<security-domain name="db-domain" cache-type="default"> 
  <authentication>
    <login-module code="Database" flag="required"> 
      <module-option name="dsJndiName" 
            value="java:jboss/datasources/test-ds"/>
      <module-option name="principalsQuery"
            value="SELECT password FROM users WHERE username = ?"/>
      <module-option name="rolesQuery"
            value="SELECT role, 'Roles' FROM roles WHERE username = ?"/>
      <module-option name="hashAlgorithm" value="SHA-256"/> 
      <module-option name="hashEncoding" value="base64"/> 
    </login-module>
  </authentication>
</security-domain>
```

## Configuring Java Messaging Service
- JMS

```sh
cd /profile=full/subsystem=messaging-activemq/​server=default

./pooled-connection-factory=mycf:add(connectors=[in-vm], entries=[java:/jms/MyCF])
./jms-queue=TestQueue:add(entries=["java:/jms/queue/AD248TestQueue"])

Messaging → Server → default → Destinations.[JMS Queue]
```

