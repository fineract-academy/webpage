---
layout: default
permalink: /how-to-run-fineract-integration-test-with-intellij-idea.html
title: How to run Apache Fineract® integration tests with IntelliJ IDEA
parent: How to
nav_order: 4
comments: true
---

## How to run Apache Fineract® integration tests with IntelliJ IDEA

### Prerequisites
1. **Java**: Ensure you have Java Development Kit (JDK) installed. Apache Fineract® typically requires Java 17 (Java 21 is not yet supported).
2. **Git**: You'll need Git to clone the Apache Fineract® source code from the repository.
3. **IntelliJ IDEA**: Install IntelliJ IDEA (Community or Ultimate edition).
4. **Configure IntelliJ Runner to be used for executing the integration tests
   
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
   - In the Project Structure dialog, under `Project`, make sure the `Project SDK` is set to the appropriate Java version (Java 17 is the latest supported).
4. **Gradle Configuration:**
   - Open the `build.gradle` file in the project root directory.
   - IntelliJ IDEA will automatically start downloading the dependencies, if not you can do it manually as well by opening the "Gradle" window and click on `Reload All Gradle Projects`.
   - Further configurations can be found in the `settings.gradle` file
5. **Configure to use IntelliJ Runner**
   - On the top bar click on `IntelliJ IDEA/Settings...`
   - On the left menu, select `Build, Execution, Deployment/Build Tools/Gradle`
   - Select `IntelliJ IDEA` from the dropdown menu of `Run tests using`
   - Click `Apply` and `OK`
6. **Where are the integration tests?**
   - You can find the integration tests in the `integration-tests` module
7. **Before executing integration tests**
   - You need to have a running instance of Fineract
   - By default the integration tests has the following configuration:
     - `backend.protocol=https`
     - `backend.host=localhost`
     - `backend.port=8443`
     - `backend.username=mifos`
     - `backend.password=password`
     - `backend.tenant=default`
   - To override any of the default value, just add new value with the same key as environment variable
     - Example:
       - `backend.protocol=http`
       - `backend.host=localhost`
       - `backend.port=8080`
8. **Execute integration tests**
   - **To execute integration test**
      - Open the integration test class and click on the green triangle icon next to the class name to execute all of the tests or click on the green triangle icon next to one of the test methods to execute only that test case
      - Alternatively: 
         - Go to `Run` > `Edit Configurations`.
         - Click the `+` button and select `JUnit`.
         - In the configuration settings:
           - Set the JDK: `Java 17` (example)
           - Choose the `fineract.integration-tests.test` module
           - VM options: `-ea`
           - Select `Class` as type of search
           - Fully qualified name of the class that contains tests: `org.apache.fineract.integrationtests.AccountingScenarioIntegrationTest` (example)
           - Working directory: `$MODULE_WORKING_DIR$`
   - **To execute every integration tests**
      - Right click on `src/test/java` directory in `integration-tests` module and select `Run Tests in java` option
      - Alternatively: 
         - Go to `Run` > `Edit Configurations`.
         - Click the `+` button and select `JUnit`.
         - In the configuration settings:
           - Set the JDK: `Java 17` (example)
           - Choose the `fineract.integration-tests.test` module
           - VM options: `-ea`
           - Select `All in package` as type of search
           - Working directory: `$MODULE_WORKING_DIR$`

9. **Common problems while running Integration tests**
    - On this page you might find additional informations that might be useful in case you are facing any issues around running integration tests:
      [How to fix common issues of integration test execution](/how-to-fix-common-issues-of-integration-tests.html)
### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
