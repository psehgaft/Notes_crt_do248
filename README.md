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
- host.xml
  - change name

### WebConsole
- ServerGroup
  - Instance

## Configure JBoss EAP to support clustered and HA operation
- Using HA  profile

## Configure JBoss Web Connectors

```sh

./jboss-cli.sh -c

/subsystem=undertow/server=sever-01/ajp-listener=ajp:add(socket-binding=ajp)

/subsystem=undertow/server=sever-01/http-listener=default:write-attribute(name=max-connections, value=200)

reload
```

## Manage applications


```sh
/subsystem=undertow/server=sever-01/host=default-host:write-attribute(name=default-web-module,value=app.war )
```

---

or

Unpoacage

```sh
jar -xd [appfile.war]
```

From the application perspective, you must use the `$WEBAPP/WEB-INF/jbossweb.xml` file within the application to define the application context root, and to
reference the virtual host and the server instance:

```xml
<jboss-web>
 <context-root>/</context-root>
 <virtual-host>version-host</virtual-host>
 <server-instance>default-server</server-instance>
</jboss-web>
```

package

```sh
jar -cf [appfile.war]
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


```xml
<subsystem xmlns="urn:jboss:domain:messaging-activemq:1.0">
 <server name="default">
 ....
 <connection-factory
 name="InVmConnectionFactory"
 connectors="in-vm"
 entries="java:/ConnectionFactory" />
 <connection-factory
 name="RemoteConnectionFactory"
 connectors="http-connector"
 entries="java:jboss/exported/jms/RemoteConnectionFactory"/>
 <pooled-connection-factory
 name="activemq-ra" transaction="xa"
 connectors="in-vm"
 entries="java:/JmsXA java:jboss/DefaultJMSConnectionFactory" />
 ...
 </server>
</subsystem>

```

```sh
cd /profile=full/subsystem=messaging-activemq/​server=default

./pooled-connection-factory=mycf:add(\
connectors=[in-vm], entries=[java:/jms/MyCF])
```

