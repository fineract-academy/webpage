---
layout: default
permalink: /how-to-setup-fineract-with-docker.html
title: How to setup Apache Fineract® with Docker
parent: How to
nav_order: 8
comments: true
---

## How to setup Apache Fineract® with Docker

### Introduction
The easiest way to work with Apache Fineract® is to run the required supporting systems in Docker

### Prerequisites
1. **Docker**: Docker should be installed on the machine.
   
### Steps
1. **Database(s):**
   - Apache Fineract® is supporting multiple database engines
     - Postgres (preferred):
       - Pull Docker image and start the container:
         ```bash
         docker run --name postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 -d postgres
         ```
       - Fineract configuration via environment variables:
         ```bash
         FINERACT_DEFAULT_TENANTDB_HOSTNAME=localhost;
         FINERACT_DEFAULT_TENANTDB_IDENTIFIER=default;
         FINERACT_DEFAULT_TENANTDB_NAME=fineract_default;
         FINERACT_DEFAULT_TENANTDB_PORT=5432;
         FINERACT_DEFAULT_TENANTDB_PWD=postgres;
         FINERACT_DEFAULT_TENANTDB_UID=postgres;
         FINERACT_HIKARI_DRIVER_SOURCE_CLASS_NAME=org.postgresql.Driver;
         FINERACT_HIKARI_JDBC_URL=jdbc:postgresql://localhost:5432/fineract_tenants;
         FINERACT_HIKARI_PASSWORD=postgres;
         FINERACT_HIKARI_USERNAME=postgres
         ```
     - MariaDB:
       - Pull Docker image and start the container:
         ```bash
         docker run --name mariadb-10.6.7 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql -d mariadb:10.6.7
         ```
       - Not requires any additional configuration, these are the default values in `application.properties`

     - Mysql:
       - Pull Docker image and start the container:
         ```bash
         docker run --name mysql-8.0 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql -d mysql:8.0
         ```
       - Fineract configuration via environment variables:
         ```bash
         FINERACT_HIKARI_DRIVER_SOURCE_CLASS_NAME=com.mysql.cj.jdbc.Driver;
         FINERACT_HIKARI_JDBC_URL=jdbc:mysql://localhost:3306/fineract_tenants
         ```
2. **JMS:**
   - Apache Fineract® is distributing messages via JMS to support external business events and distributed job execution for Close of Business day jobs
      - ActiveMQ (preferred):
       - Pull Docker image and start the container:
         ```bash
         docker run --name activemq -p 61616:61616 -p 8161:8161 -d apache/activemq-artemis:latest-alpine
         ```
       - Fineract configuration via environment variables:
         ```bash
         FINERACT_EXTERNAL_EVENTS_ENABLED=true;
         FINERACT_EXTERNAL_EVENTS_PRODUCER_JMS_ENABLED=true;
         FINERACT_EXTERNAL_EVENTS_PRODUCER_JMS_BROKER_PASSWORD=artemis;
         FINERACT_EXTERNAL_EVENTS_PRODUCER_JMS_BROKER_USERNAME=artemis;
         FINERACT_EXTERNAL_EVENTS_PRODUCER_JMS_TOPIC_NAME=events
         ```
   - Apache Fineract® is distributing messages via JMS to support distributed job execution for Close of Business day jobs
      - ActiveMQ (preferred):
       - Pull Docker image and start the container:
         ```bash
         docker run --name activemq -p 61616:61616 -p 8161:8161 --rm apache/activemq-artemis:latest-alpine
         ```
       - Fineract configuration via environment variables:
         ```bash
         FINERACT_REMOTE_JOB_MESSAGE_HANDLER_SPRING_EVENTS_ENABLED=false;
         FINERACT_REMOTE_JOB_MESSAGE_HANDLER_JMS_ENABLED=true;
         FINERACT_REMOTE_JOB_MESSAGE_HANDLER_JMS_BROKER_PASSWORD=artemis;
         FINERACT_REMOTE_JOB_MESSAGE_HANDLER_JMS_BROKER_USERNAME=artemis;
         FINERACT_REMOTE_JOB_MESSAGE_HANDLER_JMS_QUEUE_NAME=remote-jobs
         ```
3. **UI:**
   - Apache Fineract® does not have official UI, but Mifos X Web-App is maintained and the preferred implementation
     - Pull Docker image and start the container:
       ```bash
       docker run -d -p 80:80 -e FINERACT_API_URL=https://localhost:8443 -e FINERACT_PLATFORM_TENANT_IDENTIFIER=default openmf/web-app:master
       ```
4. **Backend:**
   - You can run Apache Fineract® backend as well with Docker quite easily
     - Pull Docker image and start the container:
       ```bash
       docker run -d -p 8443:8443 apache/fineract:latest
       ```
     - Fineract configuration via environment variables
      - Default configuration:
        - Expose backend on: `https://localhost:8443/fineract-provider/v1`
        - Default database: `MariaDB`
        - Default database host: `localhost`
        - Default database port: `3306`
        - Default databases:
          - Tenant store: `fineract_tenants`
          - Default tenant: `fineract_default`
        - Tenant: `default`
        - Spring based internal event handling
      - To override:
        - You can provide environment variables and override the default settings:
          - Example:
            - Lets override to connect to Postgres DB and turn off SSL and use `8080` port
            ```bash
            docker run -d --network host \
            -e FINERACT_DEFAULT_TENANTDB_HOSTNAME=localhost \
            -e FINERACT_DEFAULT_TENANTDB_IDENTIFIER=default \
            -e FINERACT_DEFAULT_TENANTDB_NAME=fineract_default \
            -e FINERACT_DEFAULT_TENANTDB_PORT=5432 \
            -e FINERACT_DEFAULT_TENANTDB_PWD=postgres \
            -e FINERACT_DEFAULT_TENANTDB_UID=postgres \
            -e FINERACT_HIKARI_DRIVER_SOURCE_CLASS_NAME=org.postgresql.Driver \
            -e FINERACT_HIKARI_JDBC_URL=jdbc:postgresql://localhost:5432/fineract_tenants \
            -e FINERACT_HIKARI_PASSWORD=postgres \
            -e FINERACT_HIKARI_USERNAME=postgres \
            -e server.port=8080 \
            -e FINERACT_SERVER_SSL_ENABLED=false \
            apache/fineract:latest
            ```   
6. **Conclusion:**
   - You can configure your own enviroment with the above steps quite easily and you can configure your own environments

### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
