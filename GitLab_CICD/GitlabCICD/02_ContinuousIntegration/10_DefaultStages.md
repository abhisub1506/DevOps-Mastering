# Default Stages in GitLab CI/CD

In all the previous examples, we explicitly defined the stages in our pipeline:

```yaml
stages:
  - build
  - test
  - unit
```

However, **GitLab already provides a set of default stages**. If your pipeline uses only these default stage names, you do **not** need to define the `stages` section explicitly.

GitLab automatically recognizes and executes them in the predefined order.

---

# GitLab Default Stages

GitLab includes the following default stages:

```text
.pre
build
test
deploy
.post
```

These stages are executed in the above order.

---

# Execution Flow

```text
.pre
   │
   ▼
build
   │
   ▼
test
   │
   ▼
deploy
   │
   ▼
.post
```

---

# Example 1 – Explicitly Defining Stages

```yaml
stages:
  - build
  - test
  - deploy

build_app:
  stage: build
  script:
    - echo "Building Application"

unit_test:
  stage: test
  script:
    - echo "Running Tests"

deploy_app:
  stage: deploy
  script:
    - echo "Deploying Application"
```

This works as expected because the stages are explicitly defined.

---

# Example 2 – Using Default Stages

Now remove the `stages` section completely.

```yaml
build_app:
  stage: build
  script:
    - echo "Building Application"

unit_test:
  stage: test
  script:
    - echo "Running Tests"

deploy_app:
  stage: deploy
  script:
    - echo "Deploying Application"
```

This pipeline **also works**.

Why?

Because **build**, **test**, and **deploy** are built-in GitLab stages. GitLab automatically uses its default stage order.

---

# What Happens Internally?

When GitLab reads the pipeline above, it internally assumes:

```yaml
stages:
  - .pre
  - build
  - test
  - deploy
  - .post
```

Even though you didn't define them, GitLab automatically makes them available.

---

# Execution Order

```text
Pipeline Starts
      │
      ▼
Build Stage
      │
      ▼
build_app Job
      │
      ▼
Test Stage
      │
      ▼
unit_test Job
      │
      ▼
Deploy Stage
      │
      ▼
deploy_app Job
      │
      ▼
Pipeline Completed
```

---

# What are `.pre` and `.post`?

GitLab also provides two special stages:

### `.pre`

The **`.pre`** stage runs **before all other stages**.

It is commonly used for:

* Initial setup
* Environment preparation
* Downloading shared resources
* Validating prerequisites

Example:

```yaml
prepare_environment:
  stage: .pre

  script:
    - echo "Preparing Pipeline Environment"
```

Execution:

```text
.pre
   │
   ▼
build
```

---

### `.post`

The **`.post`** stage runs **after all other stages have completed**.

It is commonly used for:

* Cleanup
* Notifications
* Sending emails
* Deleting temporary files
* Publishing reports

Example:

```yaml
cleanup:
  stage: .post

  script:
    - echo "Cleaning Up Resources"
```

Execution:

```text
deploy
   │
   ▼
.post
```

---

# Complete Execution Order

```text
.pre
   │
   ▼
Build
   │
   ▼
Test
   │
   ▼
Deploy
   │
   ▼
.post
```

---

# When Do You Need the `stages` Keyword?

You **do not need** to define the `stages` section if you only use GitLab's default stages:

* build
* test
* deploy

However, if you want to introduce **custom stages**, you must define them explicitly.

Example:

```yaml
stages:
  - build
  - security
  - test
  - package
  - deploy
```

Since **security** and **package** are not default stages, GitLab needs the `stages` section to know their execution order.

---

# Default vs Custom Stages

| Pipeline                            | Need `stages:`? | Reason                                      |
| ----------------------------------- | --------------- | ------------------------------------------- |
| build → test → deploy               | ❌ No            | GitLab already knows these default stages.  |
| build → security → deploy           | ✅ Yes           | `security` is a custom stage.               |
| build → unit → integration → deploy | ✅ Yes           | `unit` and `integration` are custom stages. |
| build → test                        | ❌ No            | Both are default stages.                    |

---

# Summary

GitLab CI/CD provides five predefined stages: **`.pre`**, **`build`**, **`test`**, **`deploy`**, and **`.post`**. If your jobs use only the default stage names **build**, **test**, and **deploy**, you can omit the `stages` section from your `.gitlab-ci.yml` file because GitLab automatically executes them in the correct order. The **`.pre`** stage runs before all other stages and is typically used for initialization tasks, while the **`.post`** stage runs after all other stages and is commonly used for cleanup or notifications. If your pipeline introduces custom stages such as **unit**, **security**, **package**, or **integration**, you must explicitly define the `stages` section so GitLab knows the order in which those stages should execute.
