# Publishing JUnit Test Reports in GitLab

In the previous pipeline, the **unit_test** job executed the React unit tests using:

```bash
npm test
```

The tests were executed successfully, but GitLab only displayed the job logs.

To make the test results easier to view, GitLab can parse **JUnit XML reports** and display them in the **Tests** tab of the pipeline.

---

# Vite Configuration

Your project is already configured to generate JUnit reports.

```javascript
test: {
  environment: 'jsdom',
  setupFiles: './tests/setup.js',

  reporters: ['verbose', 'junit', 'html'],

  outputFile: {
    junit: './reports/junit.xml',
    html: './reports/html/index.html'
  }
}
```

### What does this configuration do?

When `npm test` runs:

* Executes all unit tests.
* Prints verbose output in the terminal.
* Generates a **JUnit XML** report.
* Generates an HTML report.

The reports are stored in:

```text
reports/
│
├── junit.xml
└── html/
      └── index.html
```

The important file for GitLab is:

```text
reports/junit.xml
```

---

# Updated Unit Test Job

Modify the **unit_test** job as follows:

```yaml
unit_test:
  stage: unit
  image: node:22-alpine

  script:
    - npm ci
    - npm test

  artifacts:
    when: always

    reports:
      junit: reports/junit.xml
```

---

# Understanding the Artifacts Section

## when: always

```yaml
when: always
```

By default, GitLab uploads artifacts **only if the job succeeds**.

Using:

```yaml
when: always
```

ensures the JUnit report is uploaded **whether the tests pass or fail**.

This is important because if a test fails, you still want GitLab to display the failed test results.

---

## reports

```yaml
reports:
```

The `reports` keyword tells GitLab that the uploaded artifact is a **special report** rather than just a downloadable file.

GitLab understands different report types such as:

* JUnit
* Code Quality
* Coverage
* SAST
* DAST
* Dependency Scanning

In this example we are using:

```yaml
reports:
  junit: reports/junit.xml
```

---

## junit

```yaml
junit: reports/junit.xml
```

This tells GitLab:

> "The JUnit XML report is located at `reports/junit.xml`."

GitLab reads this XML file and displays the test results in the pipeline UI.

---

# Pipeline Flow

```text
Developer Pushes Code
        │
        ▼
Pipeline Starts
        │
        ▼
Unit Test Job
        │
        ▼
npm ci
        │
        ▼
npm test
        │
        ▼
Creates reports/junit.xml
        │
        ▼
GitLab Uploads JUnit Report
        │
        ▼
Job Completed
        │
        ▼
GitLab Parses junit.xml
        │
        ▼
Displays Test Results
```

---

# What Happens Internally?

```text
Docker Container
│
├── npm ci
│
├── npm test
│
├── reports/
│     ├── junit.xml
│     └── html/
│
└── Job Finished
         │
         ▼
GitLab Uploads junit.xml
         │
         ▼
Parses XML
         │
         ▼
Shows Test Results in Pipeline
```

---

# Viewing the Test Results

After the pipeline completes:

1. Open your GitLab project.
2. Navigate to **Build → Pipelines**.
3. Open the completed pipeline.
4. Click the **Tests** tab.

GitLab automatically reads the uploaded `reports/junit.xml` file and displays:

* Total tests executed
* Passed tests
* Failed tests
* Skipped tests
* Test duration
* Individual test case names

Instead of reading the console output, you get a structured view of the test results directly in the GitLab interface.

---

# Complete Unit Test Job

```yaml
unit_test:
  stage: unit
  image: node:22-alpine

  script:
    - npm ci
    - npm test

  artifacts:
    when: always

    reports:
      junit: reports/junit.xml
```

---

# Expected Pipeline Output

```text
$ npm ci

added 1350 packages...

$ npm test

✓ App.test.jsx

Test Files  1 passed
Tests       5 passed

Uploading artifacts...

reports/junit.xml: found 1 matching file

Uploading artifacts as "junit"...

Job succeeded
```

After the upload, GitLab processes the report and makes it available in the **Tests** tab of the pipeline.

---

# Summary

The **unit_test** job now publishes its test results as a **JUnit report**. The `npm test` command generates the `reports/junit.xml` file as configured in `vite.config.js`. By adding the `artifacts.reports.junit` section, GitLab recognizes this file as a JUnit test report instead of a regular artifact. The `when: always` option ensures the report is uploaded even if one or more tests fail, allowing GitLab to display detailed test results in the **Tests** tab of the pipeline. This provides a much more user-friendly way to review unit test results than reading the job log alone.
