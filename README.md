# Selenium BDD Cucumber Framework

A Java-based UI test automation framework built with **Selenium WebDriver**, **Cucumber BDD**, **Gherkin**, **JUnit**, and **Maven**.

Repository: https://github.com/abinash58/selenium-bdd-cucumber-fw

## Overview

This project demonstrates a Behavior-Driven Development (BDD) approach for web UI automation. Test scenarios can be expressed in business-readable Gherkin syntax and executed against web applications using Selenium WebDriver.

The framework is intended as a learning and reusable foundation for building Selenium + Cucumber automation suites.

## Technology Stack

| Technology | Purpose |
|---|---|
| Java | Programming language |
| Selenium WebDriver | Browser/UI automation |
| Cucumber | BDD test specification and execution |
| Gherkin | Business-readable test scenarios |
| JUnit 4 | Test execution |
| Maven | Dependency and build management |
| Cucumber Reporting | Test execution reporting |

The current `pom.xml` uses Selenium `3.141.59`, Cucumber Java/JUnit `1.2.5`, JUnit `4.13`, and Masterthought Cucumber Reporting `5.0.2`.

> **Note:** These are the dependency versions currently configured in the repository. For a new production framework, upgrading to supported Selenium 4.x and modern Cucumber/JUnit versions should be considered and validated for compatibility.

## Project Structure

The repository currently contains the following major areas:

```text
selenium-bdd-cucumber-fw/
│
├── src/
│   └── main/
│       └── java/
│           └── <automation source packages>
│
├── target/
│   └── classes/
│
├── json_output/
│   └── <Cucumber JSON output>
│
├── junit_xml/
│   └── <JUnit XML output>
│
├── test-output/
│   └── <test execution output>
│
├── .settings/
├── .classpath
├── .project
├── .gitignore
└── pom.xml
```

The repository also contains generated/build-output directories such as `target`, `json_output`, `junit_xml`, and `test-output`. These should normally be excluded from source control where appropriate and regenerated during the build.

## Framework Architecture

The framework follows the typical flow below:

```text
Feature File / Gherkin
        |
        v
Cucumber Runner
        |
        v
Step Definitions
        |
        v
Selenium WebDriver
        |
        v
Web Application
        |
        v
Assertions / Validation
        |
        v
Cucumber / JUnit Reports
```

### 1. Feature Files

Cucumber feature files describe application behavior using Gherkin syntax.

Typical structure:

```gherkin
Feature: Application functionality

  Scenario: Verify application behavior
    Given the user is on the application
    When the user performs an action
    Then the expected result should be displayed
```

This makes scenarios easier for QA, developers, product owners, and other stakeholders to understand.

### 2. Step Definitions

Step definition classes connect Gherkin steps to Java automation code.

For example:

```java
@Given("the user is on the application")
public void userIsOnApplication() {
    // Selenium implementation
}
```

Step definitions should focus on business actions and validations while keeping reusable browser operations in appropriate utility/page classes.

### 3. Selenium WebDriver

Selenium WebDriver provides browser automation. It is responsible for actions such as:

- Opening a browser
- Navigating to URLs
- Locating web elements
- Entering data
- Clicking elements
- Reading page information
- Performing validations
- Closing the browser

### 4. Cucumber Runner

A Cucumber runner connects the feature files with their step definitions and controls how scenarios are executed.

Typical configuration includes:

```java
@RunWith(Cucumber.class)
@CucumberOptions(
    features = "src/test/resources/features",
    glue = "stepdefinitions",
    plugin = {
        "pretty",
        "json:json_output/cucumber.json"
    }
)
public class TestRunner {
}
```

The exact package and feature paths should match the implementation in the project.

## Maven Configuration

The project is Maven-based and uses `pom.xml` to manage dependencies.

Run the following command from the project root:

```bash
mvn clean test
```

To run the build without cleaning:

```bash
mvn test
```

To clean generated build files:

```bash
mvn clean
```

## Prerequisites

Install the following before running the project:

1. **JDK**
   - Java Development Kit installed and configured.
   - Verify with:

```bash
java -version
```

2. **Maven**
   - Verify with:

```bash
mvn -version
```

3. **Browser**
   - Install a supported browser such as Chrome.

4. **IDE**
   - IntelliJ IDEA, Eclipse, or another Java/Maven-compatible IDE can be used.

## Setup

### Clone the repository

```bash
git clone https://github.com/abinash58/selenium-bdd-cucumber-fw.git
```

Move into the project:

```bash
cd selenium-bdd-cucumber-fw
```

### Install dependencies

```bash
mvn clean install
```

Maven will download the dependencies defined in `pom.xml`.

## Running Tests

Run all Maven tests:

```bash
mvn test
```

Run a clean test execution:

```bash
mvn clean test
```

If the project contains a specific JUnit/Cucumber runner, it can also be executed directly from the IDE.

## Cucumber Tags

Cucumber tags can be used to organize scenarios into groups such as:

```gherkin
@Smoke
Scenario: Verify login functionality
```

Then execute selected tags according to the Cucumber version/configuration used by the project.

For example, in a modern Cucumber setup:

```bash
mvn test -Dcucumber.filter.tags="@Smoke"
```

> The exact tag command depends on the Cucumber version. The repository currently uses an older Cucumber 1.x dependency, so verify the runner configuration before using modern command-line properties.

## Reports and Test Output

The repository contains output locations for different test/report formats:

### JSON

```text
json_output/
```

Cucumber JSON can be consumed by reporting tools or CI/CD systems.

### JUnit XML

```text
junit_xml/
```

JUnit XML output can be integrated with CI systems that understand JUnit-style test reports.

### Test Output

```text
test-output/
```

This directory contains generated test execution output where applicable.

### Target

```text
target/
```

Maven uses this directory for compiled classes and other build artifacts.

## Recommended .gitignore

Generated files should generally not be committed to source control.

A recommended `.gitignore` for this project is:

```gitignore
# Maven
target/

# IDE
.classpath
.project
.settings/
.idea/
*.iml

# Test/report output
test-output/
json_output/
junit_xml/

# OS files
.DS_Store
Thumbs.db
```

If report artifacts are intentionally stored in Git for demonstration purposes, keep the existing project behavior instead.

## Best Practices for Extending the Framework

### Page Object Model

For maintainable Selenium automation, page-specific locators and actions should be separated from Cucumber step definitions.

Recommended structure:

```text
src/
├── main/
│   └── java/
│       ├── pages/
│       ├── utilities/
│       └── driver/
│
└── test/
    ├── java/
    │   ├── runners/
    │   ├── stepdefinitions/
    │   └── hooks/
    │
    └── resources/
        └── features/
```

This separation helps reduce duplication and makes UI changes easier to maintain.

### WebDriver Management

Centralize WebDriver creation and cleanup instead of creating drivers independently in every step definition.

A driver factory can be used to support:

- Chrome
- Firefox
- Edge
- Headless execution
- Remote WebDriver/Grid

### Explicit Waits

Prefer explicit waits over fixed sleeps:

```java
WebDriverWait wait = new WebDriverWait(driver, 10);
wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
```

Avoid excessive use of:

```java
Thread.sleep(5000);
```

because fixed waits increase execution time and can make tests unstable.

### Reusable Utilities

Common functionality should be placed in reusable utilities, such as:

- Screenshot capture
- Explicit waits
- JavaScript execution
- Window/tab handling
- File operations
- Configuration reading
- Logging
- Test data handling

## CI/CD Integration

This framework can be integrated into CI/CD systems such as Jenkins or GitHub Actions.

A basic CI workflow is:

```text
Developer
   |
   v
Git Push
   |
   v
CI Pipeline
   |
   v
mvn clean test
   |
   v
Cucumber/JUnit Reports
   |
   v
Publish Test Results
```

Example Maven command for CI:

```bash
mvn clean test
```

For headless browser execution, browser options should be configured in the WebDriver initialization layer.

## Improving the Framework

The repository is a useful foundation for Selenium + Cucumber automation. The following enhancements can make it more production-ready:

- Upgrade Selenium and Cucumber dependencies to supported versions.
- Introduce a clear Page Object Model.
- Add centralized WebDriver/DriverFactory management.
- Add Cucumber hooks for setup and teardown.
- Add screenshot capture for failed scenarios.
- Add structured logging using Log4j2 or SLF4J.
- Add environment-specific configuration.
- Add browser selection through Maven properties.
- Add parallel execution where test isolation permits it.
- Add CI/CD pipeline integration.
- Add richer HTML reporting.
- Add API/database validation where required.
- Add test-data management.
- Add retry handling only for known transient infrastructure failures.
- Add static analysis and dependency/security checks.

## Example BDD Flow

A typical scenario follows this pattern:

```gherkin
Feature: Login

  Scenario: Successful login
    Given the user opens the login page
    When the user enters valid credentials
    And clicks the login button
    Then the home page should be displayed
```

The corresponding automation architecture should be:

```text
Feature
  ↓
Step Definition
  ↓
Page Object
  ↓
Selenium WebDriver
  ↓
Application
  ↓
Assertion
```

## Troubleshooting

### Maven command not recognized

Verify Maven installation:

```bash
mvn -version
```

If it is not recognized, configure Maven in the system `PATH`.

### Java command not recognized

Verify Java:

```bash
java -version
```

Set `JAVA_HOME` to the JDK installation directory if required.

### Browser/driver issue

Check that the browser and Selenium/driver configuration are compatible. When upgrading to Selenium 4, Selenium Manager can simplify driver management.

### Tests are not discovered

Check:

- Cucumber runner class
- `@CucumberOptions`
- Feature file path
- Step definition `glue`
- JUnit runner configuration
- Maven test source structure

### Undefined Cucumber steps

Run the test and copy the generated step-definition snippets. Implement the corresponding Java methods in the configured glue package.

## Learning Objectives

This project can be used to practice:

- Selenium WebDriver
- Cucumber BDD
- Gherkin
- JUnit
- Maven
- UI automation
- Page Object Model
- Test reporting
- Automation framework design
- CI/CD integration

## References

- Repository: https://github.com/abinash58/selenium-bdd-cucumber-fw
- Selenium: https://www.selenium.dev/
- Cucumber: https://cucumber.io/
- Maven: https://maven.apache.org/

## Author

**Abinash Behera**

This repository is maintained as a Selenium + Cucumber BDD automation framework and learning project.
