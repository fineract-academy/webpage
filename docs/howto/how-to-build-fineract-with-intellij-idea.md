---
layout: default
permalink: /how-to-build-fineract-with-intellij-idea.html
title: How to build Apache Fineract® with IntelliJ IDEA
parent: How to
nav_order: 2
comments: true
---

## How to build Apache Fineract® with IntelliJ IDEA

### Prerequisites
1. **Java**: Ensure you have Java Development Kit (JDK) installed. Apache Fineract® requires Java 21.
2. **Git**: You'll need Git to clone the Apache Fineract® source code from the repository.
3. **IntelliJ IDEA**: Install IntelliJ IDEA (Community or Ultimate edition).
   
### Steps
1. **Clone the Apache Fineract® Repository:**
   - Open a terminal or command prompt and navigate to the directory where you want to store the Apache Fineract® source code.
   - Run the following command to clone the repository:
     ```bash
     git clone https://github.com/apache/fineract.git
     ```
2. **Import the Project into IntelliJ IDEA:**
   - Open IntelliJ IDEA.
   - Click on `File` > `Open...` and select the `fineract` directory you cloned in step 1. IntelliJ will recognize it as a Gradle project.
   - IntelliJ IDEA will analyze the project and download its dependencies.
3. **Configure JDK:**
   - Verify that IntelliJ IDEA is using the correct Java SDK. Go to `File` > `Project Structure`.
   - In the Project Structure dialog, under `Project`, make sure the `Project SDK` is set to the appropriate Java version (Java 21 is the latest supported).
4. **Gradle Configuration:**
   - Open the `build.gradle` file in the project root directory.
   - IntelliJ IDEA will automatically start downloading the dependencies, if not you can do it manually as well by opening the "Gradle" window and click on `Reload All Gradle Projects`.
   - Further configurations can be found in the `settings.gradle` file
5. **Set Up Run Configurations:**
   - Go to `Run` > `Edit Configurations`.
   - Click the `+` button and select `Gradle`.
   - In the configuration settings:
       - Set the Name (e.g., "Fineract").
       - Choose the `fineract` directory as Gradle project.
       - Set `bootRun` as the Run command.
6. **Create two databases**
   - Create `fineract_tenants` and `fineract_default` databases
     - **MariaDB** (default db engine)
       - Execute `createDB` gradle task with the following params:
         - ```bash
           ./gradlew createDB -PdbName=fineract_tenants
           ./gradlew createDB -PdbName=fineract_default
           ``` 
     - **MySql**
       - Execute `createMySQLDB` gradle task with the following params:
         - ```bash
           ./gradlew createMySQLDB -PdbName=fineract_tenants
           ./gradlew createMySQLDB -PdbName=fineract_default
           ```
     - **Postgres**
       - Execute `createPGDB` gradle task with the following params:
         - ```bash
           ./gradlew createPGDB -PdbName=fineract_tenants
           ./gradlew createPGDB -PdbName=fineract_default
           ```
7. **Build the Project:**
   - Go to `Run` > `Run 'Fineract'`.
   - IntelliJ IDEA will build and run the Apache Fineract® server. You can access it using a web browser at `https://localhost:8443/fineract-provider`.

This should get you started with building and running Apache Fineract® using IntelliJ IDEA. 

### Default configuration
- By default the application will be using a self-signed TLS protocol (`https`) and port: `8443`
- By default the application will connect to MariaDB on localhost (port:`3306`).
  * Default credentials: `root/mysql`
  * Default tenants storage database: `fineract_tenants`
     * This database will store the tenant details and connection information to the tenant databases
  * Database driver: `org.mariadb.jdbc.Driver` (by default)
     * To override set the `FINERACT_HIKARI_DRIVER_SOURCE_CLASS_NAME` environment variable
  * JDBC url: `jdbc:mariadb://localhost:3306/fineract_tenants`
     * To override set the `FINERACT_HIKARI_JDBC_URL` environment variable
  * Database username: `root`
     * To override set the `FINERACT_HIKARI_USERNAME` environment variable
  * Database password: `password`
     * To override set the `FINERACT_HIKARI_PASSWORD` environment variable
- By default the liquibase scripts will create a new tenant with the below configurations
   * Default tenant identifier: `default`
      * To override set the `FINERACT_DEFAULT_TENANTDB_IDENTIFIER` environment variable
   * Default tenant database host: `localhost`
      * To override set the `FINERACT_DEFAULT_TENANTDB_HOSTNAME` environment variable
   * Default tenant database port: `3306`
      * To override set the `FINERACT_DEFAULT_TENANTDB_PORT` environment variable
   * Default tenant database username: `root`
      * To override set the `FINERACT_DEFAULT_TENANTDB_UID` environment variable
   * Default tenant database password: `mysql`
      * To override set the `FINERACT_DEFAULT_TENANTDB_PWD` environment variable
   * Default tenant timezone: `Asia/Kolkata`
      * To override set the `FINERACT_DEFAULT_TENANTDB_TIMEZONE` environment variable
   * Default tenant name: `fineract_default`
      * To override set the `FINERACT_DEFAULT_TENANTDB_NAME` environment variable
   * Default tenant description: `Default Demo Tenant`
      * To override set the `FINERACT_DEFAULT_TENANTDB_DESCRIPTION` environment variable
   * Default tenant master password: `fineract` 
      * To override set the `FINERACT_DEFAULT_TENANTDB_MASTER_PASSWORD` environment variable
      * **IMPORTANT: During the first time the liquibase executed the plain text password got encrypted with the provided master password**
   * Default tenant encryption: `AES/CBC/PKCS5Padding`
      * To override set the `FINERACT_DEFAULT_TENANTDB_ENCRYPTION` environment variable
    
**The list of configuration is not complete!**
    
### Optional extra configurations
1. Use `http` instead of `https` and use `8080` port instead of `8443`
  - Set the following `Environment variables`:
    ```
    FINERACT_SERVER_SSL_ENABLED=false
    server.port=8080
    ```
2. Use `Postgres` as Database engine
   - Set the following `Environment variables`:
     - Use Postgres Driver
       ```
       FINERACT_HIKARI_DRIVER_SOURCE_CLASS_NAME=org.postgresql.Driver
       ```
     - Connecting to `fineract_tenants` database
       ``` 
       FINERACT_HIKARI_JDBC_URL=jdbc:postgresql://localhost:5432/fineract_tenants
       FINERACT_HIKARI_USERNAME=postgres
       FINERACT_HIKARI_PASSWORD=postgres
       ```
     - Connecting to `fineract_default` database
       ```
       FINERACT_DEFAULT_TENANTDB_PORT=5432
       FINERACT_DEFAULT_TENANTDB_PWD=postgres
       FINERACT_DEFAULT_TENANTDB_UID=postgres
       ```

### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
