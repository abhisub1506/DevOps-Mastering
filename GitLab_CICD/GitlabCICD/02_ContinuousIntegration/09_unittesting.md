# Adding Unit Testing to the GitLab CI Pipeline

So far, our pipeline performs two tasks:

1. **Build** the React application.
2. **Verify** that the build output (`build/index.html`) exists.

The next step is to add **Unit Testing**.

The project already contains a unit test file:

```text
src/
│
├── App.jsx
├── App.test.jsx
├── main.jsx
└── ...
```

The file **`App.test.jsx`** contains the unit tests for the **`App.jsx`** component. It verifies that the component behaves as expected. The tests are executed using the project's configured test framework (such as Vitest or Jest, depending on the project setup). ([devsecops.puziol.com.br][1])

> We don't need to understand the test code yet. Our objective is simply to execute the existing unit tests as part of our CI pipeline.

---

# Why Run Unit Tests?

Unit testing helps verify that individual components or functions work correctly.

Whenever a developer pushes new code:

* The application is built.
* Unit tests are executed automatically.
* If any test fails, the pipeline stops.
* If all tests pass, the pipeline continues.

This helps catch bugs early before the application is deployed.

---

# Updated Pipeline

Add a new stage called **unit**.

```yaml
stages:
  - build
  - test
  - unit

build_app:
  stage: build
  image: node:22-alpine

  script:
    - node --version
    - npm --version
    - npm ci
    - npm run build

  artifacts:
    paths:
      - build/

verify_build:
  stage: test
  image: node:22-alpine

  script:
    - test -f build/index.html

unit_test:
  stage: unit
  image: node:22-alpine

  script:
    - npm ci
    - npm test
```

---

# Understanding the Unit Test Job

## Stage

```yaml
stage: unit
```

This job belongs to the **unit** stage.

Pipeline execution becomes:

```text
Build
   │
   ▼
Test
   │
   ▼
Unit
```

The Unit stage starts only after the previous stages complete successfully.

---

## Docker Image

```yaml
image: node:22-alpine
```

The Runner creates a lightweight Docker container based on the **Node.js 22 Alpine** image.

The container already contains:

* Node.js
* npm

so it is ready to execute the project's test commands.

---

## Install Dependencies

```bash
npm ci
```

Before running the tests, all project dependencies must be installed.

`npm ci`:

* Reads `package-lock.json`
* Installs the exact package versions
* Creates the `node_modules` directory

This ensures the testing environment is identical every time the pipeline runs.

---

## Execute Unit Tests

```bash
npm test
```

This command runs the project's unit tests.

GitLab executes the **test** script defined in `package.json`.

For this React project, the command automatically discovers the test file:

```text
src/App.test.jsx
```

and executes all the test cases defined in that file. React projects commonly place test files alongside the source code using names such as `App.test.jsx` or `Component.test.js`, and the test runner automatically detects them. ([Create React App][2])

---

# Pipeline Execution

```text
Developer Pushes Code
          │
          ▼
Build Stage
          │
          ▼
Creates build/
          │
          ▼
Uploads Artifact
          │
          ▼
Test Stage
          │
          ▼
Verifies build/index.html
          │
          ▼
Unit Stage
          │
          ▼
Runs npm ci
          │
          ▼
Runs npm test
          │
          ▼
Executes src/App.test.jsx
          │
          ▼
All Tests Pass
          │
          ▼
Pipeline Succeeds
```

---

# Successful Test Output

A successful test run will look similar to:

```text
$ npm test

✓ src/App.test.jsx

Test Files  1 passed
Tests       3 passed

Job succeeded
```

The exact output depends on the testing framework configured in the project, but the pipeline will succeed only if all unit tests pass. ([devsecops.puziol.com.br][1])

---

# If a Test Fails

If any assertion inside `App.test.jsx` fails, the output will resemble:

```text
$ npm test

❌ src/App.test.jsx

1 test failed

ERROR: Job failed
```

GitLab immediately marks the **unit_test** job as **Failed**, and the pipeline stops until the failing tests are fixed.

---

# Complete Pipeline Flow

```text
Pipeline
│
├── Build Stage
│     │
│     ├── npm ci
│     ├── npm run build
│     └── Upload build/ artifact
│
├── Test Stage
│     │
│     └── Verify build/index.html
│
└── Unit Stage
      │
      ├── npm ci
      ├── npm test
      └── Execute src/App.test.jsx
```

---

# Summary

The **Unit** stage adds automated testing to the CI pipeline. The **unit_test** job uses the lightweight **`node:22-alpine`** Docker image, installs the project's dependencies with **`npm ci`**, and executes **`npm test`**. During execution, the test runner automatically discovers and runs the tests defined in **`src/App.test.jsx`**. If all unit tests pass, the pipeline continues successfully. If any test fails, GitLab marks the **unit_test** job as failed and stops the pipeline, helping developers identify issues early in the development lifecycle.

[1]: https://devsecops.puziol.com.br/en/pipeline/gitlab-ci/test/?utm_source=chatgpt.com "Tests | DevSecOps and Platform Engineering Knowledgement"
[2]: https://create-react-app.dev/docs/running-tests/?utm_source=chatgpt.com "Running Tests"
