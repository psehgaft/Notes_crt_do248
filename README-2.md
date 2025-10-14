# Notes_crt_ad280

Here are exercises you can solve to prepare effectively — each one addresses objectives of the EX248 exam:

## Basic Installation
- Install JBoss EAP 7.4 in standalone mode on a Linux server. Start the server, access it via CLI and the web console.  
- Configure the service to start automatically on system boot (systemd).  

## Domain Mode
- Set up a domain spanning two hosts: one as a host controller, another as a secondary. Create multiple servers inside the domain.  
- Adjust Java memory parameters at host, server-group, and server levels.  

## Security
- Configure a security domain that uses an external LDAP. Secure web or JMS endpoints.  
- Configure SSL for web connectors.  

## High Availability / Clustering
- Configure clustering between JBoss EAP nodes using TCP unicast binding or mod_cluster.  
- Simulate a node failure and verify that another node takes over the load (failover).  

## Datasources & JMS
- Create XA and non-XA datasources with different database providers.  
- Configure JMS topics and queues, send messages, consume messages, and control access permissions.  

## Monitoring and Maintenance
- Enable advanced logging, review logs (`server.log`, `domain.log`), identify common errors.  
- Take configuration snapshots, restore previous configurations.  

## Web Connectors
- Configure HTTP/S connectors, adjust web properties such as timeouts, buffers, number of threads, etc.  

## Optimization
- Tune the JVM (heap, GC) for high-load scenarios.  
- Run performance tests with load tools, identify bottlenecks.  
