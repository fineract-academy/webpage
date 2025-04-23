---
layout: default
permalink: /how-to-run-fineract-units-test-with-intellij-idea.html
title: How to run Apache Fineract® unit tests with IntelliJ IDEA
parent: How to
nav_order: 3
comments: true
---

## How to run Apache Fineract® unit tests with IntelliJ IDEA

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
5. **Where are the unit tests?**
   - You can find the unit tests in the `fineract-provider` module under `/src/test` directory
   - There are 2 types of tests:
      * JUnit tests (legacy)
      * Cucumber tests (new way)
6. **Execute unit tests**
   - **To execute JUnit tests**
      - Open the unit test class and click on the green triangle icon next to the class name to execute all of the tests or click on the green triangle icon next to one of the test methods to execute only that test case
      - Alternatively: 
         - Go to `Run` > `Edit Configurations`.
         - Click the `+` button and select `JUnit`.
         - In the configuration settings:
           - Set the JDK: `Java 21` (example)
           - Choose the `fineract.fineract-provider.test` module
           - VM options: `-ea`
           - Select `Class` as type of search
           - Fully qualified name of the class that contains tests: `org.apache.fineract.infrastructure.businessdate.api.BusinessDateApiTest` (example)
           - Working directory: `$MODULE_WORKING_DIR$`
   - **To execute Cucumber tests**
      - **IMPORTANT: This functionality requires [Cucumber for Java](https://plugins.jetbrains.com/plugin/7212-cucumber-for-java) plugin to be installed in the IntelliJ IDEA!**
      - Open the cucumber feature file you would like to execute
         - You can find them under `/src/test/resources/features` directory
         - Click on the green triangle icon next to the Feature name to execute all of the tests or click on the green triangle icon next to one of the scenarios to execute only that test case
      - Alternatively: 
         - Go to `Run` > `Edit Configurations`.
         - Click the `+` button and select `Cucumber Java`.
         - In the configuration settings:
           - Main class: `io.cucumber.core.cli.Main`
           - Feature or folder path: `/<your fineract installation path>/fineract-provider/src/test/resources/features/accounting/accounting.common.feature` (example)
           - Program arguments: `--plugin org.jetbrains.plugins.cucumber.java.run.CucumberJvm5SMFormatter`
           - Working directory: `$MODULE_WORKING_DIR$`
           - Use classpath of module: `fineract.fineract-provider.test`
   - **To execute every unit tests (JUnit and Cucumber as well)**
      - Right click on `src/test/java` directory in `fineract-provider` module and select `Run Tests in java` option
      - Alternatively: 
         - Go to `Run` > `Edit Configurations`.
         - Click the `+` button and select `JUnit`.
         - In the configuration settings:
           - Set the JDK: `Java 21` (example)
           - Choose the `fineract.fineract-provider.test` module
           - VM options: `-ea`
           - Select `All in directory` as type of search
           - Fully qualified path of the directory: `/<your fineract installation path>/fineract-provider/src/test/java`
           - Working directory: `$MODULE_WORKING_DIR$`
             
### Important
Keep in mind that Apache Fineract® is a complex project, and you may encounter issues or need to configure additional settings based on your specific environment and requirements. It's a good practice to refer to the official Apache Fineract® documentation and the project's developer community for more details and troubleshooting!
