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
/subsystem=undertow/server=\
default-server/ajp-listener=ajp:add(socket-binding=ajp)
```

## Manage applications

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

or

```sh
/subsystem=undertow/configuration=handler/file=welcome-content:write-attribute(name=path,value="/")
```



## Files
- Context

## Configure JBoss EAP security
- Security Domain

## Configuring Java Messaging Service
- JMS

