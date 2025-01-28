---
layout: default
permalink: /how-to-configure-multi-tenancy-for-fineract.html
title: How to configure and use multi-tenancy for Apache Fineract®
parent: How to
nav_order: 7
comments: true
---

## How to configure and use multi-tenancy for Apache Fineract®

### Introduction
Apache Fineract® supports multi-tenancy, which allows multiple clients (tenants) to use the same instance of the application but keep their data isolated from each other.

### Prerequisites
1. **Database(s)**: Storing tenant details and tenant data.
2. **Database client**: Client application to connect to the database(s).
   
### Steps
1. **Create a new database for tenant details (mandatory):**
   - Apache Fineract® is storing the tenant details in a separate database.
     - First thing is to create the tenant details database:
       ```sql
       CREATE DATABASE `fineract_tenants`;
       ```
     - Second thing is to configure JDBC connection details to connect to the tenant details database in Apache Fineract®. Default configuration is using MariaDB related configuration.
       - JDBC driver to be used to connect to database(s) (default value: `org.mariadb.jdbc.Driver`)
         - `spring.datasource.hikari.driverClassName=${FINERACT_HIKARI_DRIVER_SOURCE_CLASS_NAME:org.mariadb.jdbc.Driver}`
         - To override to use PostgresDB instead, set the below environment variable:
           - `FINERACT_HIKARI_DRIVER_SOURCE_CLASS_NAME=org.postgresql.Driver`
       - JDBC URL to connect to the tenant details database (default value: `jdbc:mariadb://localhost:3306/fineract_tenants`)
         - `spring.datasource.hikari.jdbcUrl=${FINERACT_HIKARI_JDBC_URL:jdbc:mariadb://localhost:3306/fineract_tenants}`
         - To override to use PostgresDB instead, set the below environment variable:
           - `FINERACT_HIKARI_JDBC_URL=jdbc:postgresql://localhost:5432/fineract_tenants`
       - Username to connect to the tenant details database (default value: `root`)
         - `spring.datasource.hikari.username=${FINERACT_HIKARI_USERNAME:root}`
         - To override to use `postgres` instead, set the below environment variable:
           - `FINERACT_HIKARI_USERNAME=postgres`
       - Password to connect to the tenant details database (default value: `mysql`)
         - `spring.datasource.hikari.password=${FINERACT_HIKARI_PASSWORD:mysql}`
         - To override to use `postgres` instead, set the below environment variable:
           - `FINERACT_HIKARI_PASSWORD=postgres`
   - **Important: Apache Fineract® is always connecting to this database to fetch tenant details and connection informations first**!

2. **Create a new database for the 1st tenant (mandatory):**
   - With Apache Fineract® there must be at least 1 tenant to be configured, so first thing is to create the database for the 1st tenant (default value is `fineract_defaul`, but can be overridden):
       ```sql
       CREATE DATABASE `fineract_default`;
       ```
     - Second thing is to configure the 1st tenant in Apache Fineract®. By default the liquibase scripts are reading the 1st (default) tenant details from the `application.properties`
       - Tenant database host (default value: `localhost`)
         - `fineract.tenant.host=${FINERACT_DEFAULT_TENANTDB_HOSTNAME:localhost}`
         - To override to connect to `different_host` instead, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_HOSTNAME=different_host`
       - Tenant database port (default value: `3306`)
         - `fineract.tenant.port=${FINERACT_DEFAULT_TENANTDB_PORT:3306}`
         - To override to connect to `5432` port instead, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_PORT=5432`
       - Tenant database username (default value: `root`)
         - `fineract.tenant.username=${FINERACT_DEFAULT_TENANTDB_UID:root}`
         - To override to `user`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_UID=user`
       - Tenant database password (default value: `mysql`)
         - `fineract.tenant.password=${FINERACT_DEFAULT_TENANTDB_PWD:mysql}`
         - To override to `password`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_UID=password`
       - Tenant database connection parameters (no default value)
         - `fineract.tenant.parameters=${FINERACT_DEFAULT_TENANTDB_CONN_PARAMS:}`
         - To override, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_CONN_PARAMS=serverTimezone=UTC`
       - Tenant timezone (default value: `Asia/Kolkata`)
         - `fineract.tenant.timezone=${FINERACT_DEFAULT_TENANTDB_TIMEZONE:Asia/Kolkata}`
         - To override to `Europe/Budapest`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_TIMEZONE=Europe/Budapest`
       - Tenant identifier (default value: `default`)
         - `fineract.tenant.identifier=${FINERACT_DEFAULT_TENANTDB_IDENTIFIER:default}`
         - To override to `tenant1`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_IDENTIFIER=tenant1`
       - Tenant database name (default value: `fineract_default`)
         - `fineract.tenant.name=${FINERACT_DEFAULT_TENANTDB_NAME:fineract_default}`
         - To override to `fineract_tenant_a`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_NAME=fineract_tenant_a`
       - Tenant description (default value: `Default Demo Tenant`)
         - `fineract.tenant.description=${FINERACT_DEFAULT_TENANTDB_DESCRIPTION:Default Demo Tenant}`
         - To override to `Tenant A database`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_DESCRIPTION=Tenant A database`
       - Tenant database password encryption master password (default value: `fineract`)
         - `fineract.tenant.master-password=${FINERACT_DEFAULT_TENANTDB_MASTER_PASSWORD:fineract}`
         - To override to `vErYsTrOnGp4SsW0rD`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_MASTER_PASSWORD=vErYsTrOnGp4SsW0rD`
       - Tenant database password encryption (default value: `AES/CBC/PKCS5Padding`)
         - `fineract.tenant.encrytion=${FINERACT_DEFAULT_TENANTDB_ENCRYPTION:"AES/CBC/PKCS5Padding"}`
         - To override to `RSA/ECB/OAEPWithSHA-256AndMGF1Padding`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_ENCRYPTION=RSA/ECB/OAEPWithSHA-256AndMGF1Padding`
     - Optionally for each tenant read-only connection can be also configured:
       - Tenant read-only database host (no default value)
         - `fineract.tenant.read-only-host=${FINERACT_DEFAULT_TENANTDB_RO_HOSTNAME:}`
         - To override to connect to `different_host` instead, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_RO_HOSTNAME=different_host`
       - Tenant read-only database port (no default value)
         - `fineract.tenant.read-only-port=${FINERACT_DEFAULT_TENANTDB_RO_PORT:}`
         - To override to connect to `5432` port instead, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_RO_PORT=5432`
       - Tenant read-only database username (no default value)
         - `fineract.tenant.read-only-username=${FINERACT_DEFAULT_TENANTDB_RO_UID:}`
         - To override to `user`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_RO_UID=user`
       - Tenant read-only database password (no default value)
         - `fineract.tenant.read-only-password=${FINERACT_DEFAULT_TENANTDB_RO_PWD:}`
         - To override to `password`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_RO_PWD=password`
       - Tenant read-only database connection parameters (no default value)
         - `fineract.tenant.read-only-parameters=${FINERACT_DEFAULT_TENANTDB_RO_CONN_PARAMS:}`
         - To override, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_RO_CONN_PARAMS=serverTimezone=UTC`
       - Tenant read-only database name (no default value)
         - `fineract.tenant.read-only-name=${FINERACT_DEFAULT_TENANTDB_RO_NAME:}`
         - To override to `fineract_tenant_a`, set the below environment variable:
           - `FINERACT_DEFAULT_TENANTDB_RO_NAME=fineract_tenant_a`
   - **Important: Apache Fineract® is not supporting different JDBC driver to be used for connecting to `tenant details database` and `tenant database`!**!
     
3. **Create a new database for the 2nd tenant (optional):**
   - First thing is to create the database for the 2nd tenant (like: `fineract_test`):
       ```sql
       CREATE DATABASE `fineract_test`;
       ```
   - Secong thing is different now, we cannot configure additional tenants the same way we did for the 1st one, we need to manually create 2 more entries in the `fineract_tenants` database:
     - First entry is into the `tenant_server_connections` table:
       - `id`: PK key of the newly created entry (like: `2`).
       - `schema_server`: Tenant database hostname (like: `rdsinstance.xxx.xxx.rds.amazonaws.com`)
       - `schema_name`: Tenant database name (like: `fineract_tenant_a`)
       - `schema_server_port`: Tenant database port (like: `5432`)
       - `schema_username`: Tenant database username (like: `tenant_user`)
       - `schema_password`: Tenant database password (like: `tenant_password`)
       - `auto_update`: Not really used...
       - `pool_initial_size`: Connection pool size (like: `5`)
       - `pool_validation_interval`: Connection validated as alive timeout (like: `30000`)
       - `pool_remove_abandoned`: Not really used...
       - `pool_remove_abandoned_timeout`: Not really used...
       - `pool_log_abandoned`: Not really used...
       - `pool_abandon_when_percentage_full`: Not really used...
       - `pool_test_on_borrow`: Not really used...
       - `pool_max_active`: Configure maximum connection pool size (like: `40`)
       - `pool_min_idle`: Not really used...
       - `pool_max_idle`: Not really used...
       - `pool_suspect_timeout`: Not really used...
       - `pool_time_between_eviction_runs_millis`: Not really used...
       - `pool_min_evictable_idle_time_millis`: Not really used...
       - `schema_connection_parameters`: Tenant database JDBC connection parameters
       - `readonly_schema_server`: Read-only tenant database hostname (if applicable)
       - `readonly_schema_name`: Read-only tenant database name (if applicable)
       - `readonly_schema_server_port`: Read-only tenant database port (if applicable)
       - `readonly_schema_username`: Read-only tenant database username (if applicable)
       - `readonly_schema_password`: Read-only tenant database password (if applicable)
       - `readonly_schema_connection_parameters`: Read-only tenant database JDBC connection parameters (if applicable)
       - `master_password_hash`: Tenant database password encryption password hash (if applicable)
    - Second entry is into the `tenants` table:
      - `id`: PK key of the newly created entry (like: `2`)
      - `identifier`: Tenant identifier which will be used in Apache Fineract® (like: `default`)
      - `name`: Tenant name (like: `Default Demo Tenant`)
      - `timezone_id`: Tenant timezone (like: `Asia/Kolkata`)
      - `county_id`: Not really used...
      - `joined_date`: Not really used...
      - `created_date`: Not really used...
      - `lastmodified_date`: Not really used...
      - `oltp_id`: Id of entry of `tenant_server_connections` (like: `2`)
      - `report_id`: Id of entry of `tenant_servier_connections`, it's possible to use a different connection details for reporting (but usually it is the same as `oltp_id`) (like: `2`)  
  - **Important: Apache Fineract® will initialize the new tenant database via liquibase when the application got restarted!**!
    
4. **How to use tenants:**
  - When we configured each tenants, we can target them the following ways:
     - As header parameter
        - `Fineract-Platform-TenantId: {tenant identifier}`
           - Example: `Fineract-Platform-TenantId: default` -> target `default` tenant
     - As query parameter
        - `tenantIdentifier={tenant identifier}`
           - Example: `tenantIdentifier=default` -> target `default` tenant
   - **Important: Apache Fineract® will always requires a tenant to be targeted**!

5. **Conclusion:**
   - Each tenant can have its own database which contains the users, configurations, products, etc.
   - First thing is identifying the tenant based on the provided parameters and after authenticating and authorizing the request
   - By default the `default` tenant is created and configured

### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
