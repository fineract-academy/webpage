---
layout: default
permalink: /how-to-fix-common-issues-of-integration-tests.html
title: How to fix common Apache Fineract® integration test issues
parent: How to
nav_order: 10
comments: true
---

## How to fix common Apache Fineract® integration test issues

### Introduction
Lets see common misunderstandings and common issues you might face when you run the Apache Fineract® integration tests!

### Common problems

1. Integration tests requires a running Apache Fineract® backend!
    * Integration test module is like a "3rd party application" that executes and test different use cases, but communicate with Apache Fineract® via REST API calls
     
2. Default configuration
    * By default it tries to connect to Apache Fineract® 
      * On the following URL: `https://localhost:8443`
      * Authentication:
        * Username: `mifos`
        * Password: `password`
      * Tenant: `default`
    * To override you can provide the below environment variables:
      * Backend URL port: `BACKEND_PROTOCOL=https`
      * Backend URL port: `BACKEND_HOST=localhost`
      * Backend URL port: `BACKEND_PORT=8443`
      * Backend URL port: `BACKEND_USERNAME=mifos`
      * Backend URL port: `BACKEND_PASSWORD=password`
      * Backend URL port: `BACKEND_TENANT=default`
     
3. Running integration tests by default tries to start a cargo and run an instance of Apache Fineract® there
  *  Integration-test module `test` task depends on to start local Cargo first, which depends on `fineract-war:war` first.
  *  When the Cargo started, it deploys built `fineract-provider.war`
    * Also it's either
     * start a PostgresDB, if provided project property is `dbType=postgresql`
     * start a MysqlDB, if provided project property is `dbType=mysql`
     * start a MariaDB, if no `dbType` was provided
 * To override this default behaviour, in case you dont want local Cargo to be started and run the Apache Fineract® backend and the underlying database there, you can disable it by providing the below project variable:
   * `cargoDisabled=true`
   * Example to run the integration tests in console, but do not start local Cargo:
     *  `./gradlew -PcargoDisabled=true --no-daemon --console=plain :integration-tests:test` -> for MacOS and Linux
     *  `gradlew.bat -PcargoDisabled=true --no-daemon --console=plain :integration-tests:test` -> for Windows

4. Good to keep in mind
  * **Integration tests are not idempotent**
    
    As rule of thumb the community did the best to write integration tests that does not rely on anything that was configured or set by an another test:
    * Each test set / update global configurations if needed, create client, create loan product, create loan, etc.

    * However
      * One of the problem Fineract facing is some of the integration tests were set something (like global configuration) but forgot to undone it at the end of the test execution. -> It might affect the upcoming next test executions.
      * An another problem might be that the integration test execution order is not explicitly set, so it might executed in different order on different environments. 
      * An another problem might be, if an integration test fails, and it set a global configuration as part of the test execution but there is no fallback logic to set it back to original value in case of any exception occurs. -> It might affect the upcoming next test executions.

  * **Integration tests requires an empty, freshly deployed database**

    This one is not a rule written in stone, but we noticed situations if the integration tests were not executed on a freshly deployed database, they were failing.

    * One of the underlying problem is many integration tests were built on the assumption that it will always be a fresh, newly deployed database and assuming some “default” configurations.

  * **Integration tests are requires you to run the backend with “test” profile**

    Some integration tests are relying on APIs that are only supported for internal usage / test purposes.

    * You need to make you are running the Apache Fineract® with "test" profile!
      * `SPRING_PROFILES_ACTIVE=test`  


### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
