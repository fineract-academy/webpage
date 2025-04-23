---
layout: default
permalink: /how-to-run-fineract-e2e-test-with-intellij-idea.html
title: How to run Apache Fineract® E2E tests with IntelliJ IDEA
parent: How to
nav_order: 5
comments: true
---

## How to run Apache Fineract® E2E tests with IntelliJ IDEA

### Prerequisites
1. **Java**: Ensure you have Java Development Kit (JDK) installed. Apache Fineract® requires Java 21.
2. **Git**: You'll need Git to clone the Apache Fineract® source code from the repository.
3. **IntelliJ IDEA**: Install IntelliJ IDEA (Community or Ultimate edition).
4. **Cucumber for Java plugin**: Install the Cucumber for Java plugin in IntelliJ IDEA for easier execution

### Fineract E2E test framework
A new testing framework got introduced to address some of the problems with the existing integration tests.

**The problems:**
1. Handcrafted, low level API calls via REST Assured library
2. No type-safety, instead manual JSON parsing
3. Hard to read and understand
4. Full of duplicated logics and configurations
5. etc.

**The new framework is using:**
1. Using exclusively the `fineract-client` for communication with the running backend, hence type-safety
2. We can write the test cases in Cucumber / Gherkin -> Easier to read + easier to specify test cases = easier contributions from non-developers
   
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
5. **Where are the E2E tests?**
   - You can find the E2E tests in the 
      - `fineract-e2e-tests-core` module: Contains all the core / common logics like calling APIs, configurations, enums, communication, etc.
      - `fineract-e2e-tets-runner` module: Contains the runner configuration and all the test specifications
6. **Before executing E2E tests**
   - You need to have a running instance of Fineract
   - By default the E2E tests has the following configurations:
     ### **Connection details to running backend**
     - `fineract-test.api.base-url=${BASE_URL:https://localhost:8443}`
     - `fineract-test.api.username=${TEST_USERNAME:mifos}`
     - `fineract-test.api.password=${TEST_PASSWORD:password}`
     - `fineract-test.api.tenant-id=${TEST_TENANT_ID:default}`
     ### **Initialize default test data**
     - `fineract-test.initialization.enabled=${INITIALIZATION_ENABLED:false}`
     ### **Testrail configuration**
     - `fineract-test.testrail.enabled=${TESTRAIL_ENABLED:false}`
     - `fineract-test.testrail.base-url=${TESTRAIL_BASEURL:}`
     - `fineract-test.testrail.username=${TESTRAIL_USERNAME:}`
     - `fineract-test.testrail.password=${TESTRAIL_PASSWORD:}`
     - `fineract-test.testrail.run-id=${TESTRAIL_RUN_ID:0}`
     ### **JMS messaging configuration (for external events)**
     - `fineract-test.messaging.jms.broker-url=${ACTIVEMQ_BROKER_URL:}`
     - `fineract-test.messaging.jms.broker-username=${ACTIVEMQ_BROKER_USERNAME:}`
     - `fineract-test.messaging.jms.broker-password=${ACTIVEMQ_BROKER_PASSWORD:}`
     - `fineract-test.messaging.jms.topic-name=${ACTIVEMQ_TOPIC_NAME:}`
     ### **External event processing configuration**
     - `fineract-test.event.wait-timeout-in-sec=${EVENT_WAIT_TIMEOUT_IN_SEC:5}`
     - `fineract-test.event.verification-enabled=${EVENT_VERIFICATION_ENABLED:false}`
   - To override any of the default value, just add new value with the same key as environment variable
     - **Example:**
       - `BASE_URL=http://localhost:8080`
7. **Execute E2E tests**
   - **To execute a E2E test**
      - Open any feature file in the `resources/features` directory of `fineract-e2e-tests-runner` module and click on the green triangle icon next to the class name to execute all of the tests or click on the green triangle icon next to one of the test methods to execute only that test case
      - Alternatively: 
         - Go to `Run` > `Edit Configurations`.
         - Click the `+` button and select `Cucumber for Java`.
         - In the configuration settings:
           - Main task: `io.cucumber.core.cli.Main`
           - Feature or folder path: `<fully qualified path to fineract directory>/fineract-e2e-tests-runner/src/test/resources/features/Datatables.feature` (example)
           - Program arguments: ` --plugin org.jetbrains.plugins.cucumber.java.run.CucumberJvm5SMFormatter`
           - Working directory: `$MODULE_WORKING_DIR$`
           - Use classpath of module: `fineract.fineract-e2e-tests-runner.test`
   - **To execute every E2E tests**
      - Above configuration but
        - Feature or folder path: `<fully qualified path to fineract directory>/fineract-e2e-tests-runner/src/test/resources/features/` 
      - Alternatively: 
         - Go to `Run` > `Edit Configurations`.
         - Click the `+` button and select `Gradle`.
         - In the configuration settings:
           - Run: `cucumber`
           - Gradle project: `<fully qualified path to fineract directory>/fineract-e2e-tests-runner`
             
### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
