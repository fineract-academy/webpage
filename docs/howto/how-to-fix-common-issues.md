---
layout: default
permalink: /how-to-fix-common-issues.html
title: How to fix common Apache Fineract® issues
parent: How to
nav_order: 9
comments: true
---

## How to fix common Apache Fineract® issues

### Introduction
Lets see common misunderstandings and common issues you might face when you configure or run the Apache Fineract® at first time!

### Common misunderstandings

1. Apache Fineract® is a **backend** application!
    * It does not contain a packaged UI!
    * However there is an unofficial, but maintained therefor recommended open source UI application by Mifos:
      * Mifos X Web App: https://github.com/openMF/web-app
     
2. Exceptions during startup
    * Often the very first exceptions in the system does not really tells the underlying issue, it's like a chain, it starts failing for a reason and multiple exceptions emerge as a side effect.
      * Look for the last exceptions in the log to see the "real" underlying issue! 
      * Example:
        ```
        2024-12-18 22:47:18.919 -ERROR 44183  --- [           main] o.s.boot.SpringApplication               : Application run failed
        org.springframework.context.ApplicationContextException: Unable to start web server
        	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:165)
        	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:619)
        	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146)
        	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:754)
        	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:456)
        	at org.springframework.boot.SpringApplication.run(SpringApplication.java:335)
        	at org.springframework.boot.builder.SpringApplicationBuilder.run(SpringApplicationBuilder.java:149)
        	at org.apache.fineract.ServerApplication.main(ServerApplication.java:57)
        Caused by: org.springframework.boot.web.server.WebServerException: Unable to start embedded Tomcat
        	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.initialize(TomcatWebServer.java:147)
        	at org.springframework.boot.web.embedded.tomcat.TomcatWebServer.<init>(TomcatWebServer.java:107)
        	at org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory.getTomcatWebServer(TomcatServletWebServerFactory.java:516)
        	at org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory.getWebServer(TomcatServletWebServerFactory.java:222)
        	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.createWebServer(ServletWebServerApplicationContext.java:188)
        	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:162)
        	... 7 common frames omitted
        Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration': Unsatisfied dependency expressed through constructor parameter 1: Error creating bean with name 'jerseyConfig': Invocation of init method failed
        	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:795)
        	at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:237)
        	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1375)
          ...
        ```
    * However, at the end of the chain of the exception there will be the underlying issue:
      ```
      Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'hikariTenantDataSource' defined in class path resource [org/apache/fineract/infrastructure/core/config/HikariCpConfig.class]: Connection to localhost:5433 refused. Check that the hostname and port are correct and that the postmaster is accepting TCP/IP connections.
      ```
3. Successully started the Apache Fineract® but still unable to connect
    * Usually it means that you are trying to connect to the wrong endpoint!
      * By default the Apache Fineract® does not reachable through the root URI, rather it is listening on the following endpoint:
        * By default it is using a self-signed certificate and use SSL: -> `https`
        * By default it is listening on the port of `8443`
        * API path is `/fineract-provider/api/v1`
      * Example:
        `https://localhost:8443/fineract-provider/api/v1`
    * What happens if you try to connect to `https://localhost:8443`?
      * HTTP Status 404 – Not Found
    * What happens if you try to connect to `https://localhost:8443/fineract-provider`?
      * HTTP Status 404 – Whitelabel Error Page - This application has no explicit mapping for /error, so you are seeing this as a fallback.
    * What happens if you try to connect to `https://localhost:8443/fineract-provider/api`?
      * HTTP Status 400 – Whitelabel Error Page - This application has no explicit mapping for /error, so you are seeing this as a fallback.
    * What happens if you try to connect to `https://localhost:8443/fineract-provider/api/v1`?
      * HTTP Status 400 – Whitelabel Error Page - This application has no explicit mapping for /error, so you are seeing this as a fallback.
    * What happens if you try to connect to `http://localhost:8443/fineract-provider/api/v1`? (Trying to connect to http, when SSL is enabled)
      * HTTP Status 400 – Bad Request - This combination of host and port requires TLS.
    * What happens if you try to connect to `https://localhost:8443/actuator`?
      * This is the wrong endpoint for the actuator.
      * The right endpoint is: `https://localhost:8443/fineract-provider/actuator`
        
### Common issues

1. Forgot to create the two initial databases that Apache Fineract® requires!
    * With default configurations Apache Fineract® requires two database (in case of Postgres DB used) or two schemas (in case of MySql / Maria DB used):
      * `fineract_tenants`: Stores the tenant - and connection details
      * `fineract_default`: Stores the tenant data (clients, loans, etc.)
    * Here is the exception you got if you forgot to create them (for the examples I am using PostgresDB):
      ```
      Caused by: org.postgresql.util.PSQLException: FATAL: database "fineract_tenants" does not exist
    	at org.postgresql.core.v3.QueryExecutorImpl.receiveErrorResponse(QueryExecutorImpl.java:2733)
    	at org.postgresql.core.v3.QueryExecutorImpl.readStartupMessages(QueryExecutorImpl.java:2845)
    	at org.postgresql.core.v3.QueryExecutorImpl.<init>(QueryExecutorImpl.java:176)
    	at org.postgresql.core.v3.ConnectionFactoryImpl.openConnectionImpl(ConnectionFactoryImpl.java:323)
    	at org.postgresql.core.ConnectionFactory.openConnection(ConnectionFactory.java:54)
    	at org.postgresql.jdbc.PgConnection.<init>(PgConnection.java:273)
    	at org.postgresql.Driver.makeConnection(Driver.java:446)
    	at org.postgresql.Driver.connect(Driver.java:298)
    	at com.zaxxer.hikari.util.DriverDataSource.getConnection(DriverDataSource.java:137)
    	at com.zaxxer.hikari.pool.PoolBase.newConnection(PoolBase.java:360)
    	at com.zaxxer.hikari.pool.PoolBase.newPoolEntry(PoolBase.java:202)
    	at com.zaxxer.hikari.pool.HikariPool.createPoolEntry(HikariPool.java:461)
    	at com.zaxxer.hikari.pool.HikariPool.checkFailFast(HikariPool.java:550)
    	at com.zaxxer.hikari.pool.HikariPool.<init>(HikariPool.java:98)
    	at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.java:111)
    	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
    	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
    	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeCustomInitMethod(AbstractAutowireCapableBeanFactory.java:1910)
    	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1863)
    	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1802)
    	... 186 common frames omitted
      ```
2. Created the necessary databases, but the configuration points to the wrong DB host:
    * Look for the last exceptions in the exception chain:
      ```
      Caused by: org.postgresql.util.PSQLException: Connection to localhost:5433 refused. Check that the hostname and port are correct and that the postmaster is accepting TCP/IP connections.
    	at org.postgresql.core.v3.ConnectionFactoryImpl.openConnectionImpl(ConnectionFactoryImpl.java:352)
    	at org.postgresql.core.ConnectionFactory.openConnection(ConnectionFactory.java:54)
    	at org.postgresql.jdbc.PgConnection.<init>(PgConnection.java:273)
      ...
      Caused by: java.net.ConnectException: Connection refused
  	  at java.base/sun.nio.ch.Net.pollConnect(Native Method)
  	  at java.base/sun.nio.ch.Net.pollConnectNow(Net.java:672)
      ...
      ```
3. Successully started the Apache Fineract® but still unable to connect
    * Apache Fineract® default configuration is using SSL with self-signed certificate and that is usually rejected by browsers or any clients till it got accepted explicitly!
    * How you can test it? Try to open the `https://localhost:8443/fineract-provider/api/v1` in the browser and it will notify you that it got rejected but you can accept the risk and it will let you communicate via an "unsecured" connection.
      * You should see something like this: "This connection is not private", "The website might impersonate `localhost`", etc.
4. You configured everything right, and you are connecting to the right endpoints, but still you always got HTTP 400 - Bad request
    * The issue is often occurs if you forgot to send the `tenant-identifier` as HEADER or as `query param`
      * `tenant identifier` is a mandatory thing as this is by the system to decide which tenants we are trying to connect to!
 
### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
