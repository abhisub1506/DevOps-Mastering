# GitLab CI/CD for ASP.NET Core + MSTest (Step by Step)

You currently have:

```text id="k1jlnx"
Solution
│
├── ProductApi          → ASP.NET Core Empty Project
│
└── ProductUnitTest     → MSTest Project
```

The API project currently returns:

```text id="if0zv0"
Hello World!
```

and your MSTest project contains:

```csharp id="6a76zd"
namespace ProductUnitTest
{
    [TestClass]
    public sealed class Test1
    {
        [TestMethod]
        public void TestMethod1()
        {
            Assert.AreEqual(1, 1);
        }
    }
}
```

This is enough to start learning GitLab CI/CD.

---

# Step 1 : Push the Solution to GitLab

Create an empty GitLab repository.

Example:

```text id="pcx3iw"
ProductApiGitlabDemo
```

Open terminal in solution folder.

### Initialize Git

```bash id="k3m7ee"
git init
```

### Add Files

```bash id="lrppq8"
git add .
```

### Commit

```bash id="zwru7m"
git commit -m "Initial ASP.NET Core Project"
```

### Add Remote Repository

```bash id="tq1fnv"
git remote add origin https://gitlab.com/<username>/ProductApiGitlabDemo.git
```

### Push

```bash id="74qes5"
git branch -M main
git push -u origin main
```

After pushing, your repository should contain:

```text id="snq95r"
ProductApi.sln
ProductApi/
ProductUnitTest/
```

---

# Step 2 : Create First Pipeline

Create file:

```text id="zb35uh"
.gitlab-ci.yml
```

---

# Step 3 : Build Stage

Create a simple pipeline.

```yaml id="61r4qg"
stages:
  - build

build_app:
  stage: build
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet --version
    - dotnet restore
    - dotnet build
```

---

# What Happens?

```text id="e2omys"
GitLab Runner
      │
      ▼
Pull .NET SDK Docker Image
      │
      ▼
dotnet restore
      │
      ▼
dotnet build
      │
      ▼
Pipeline Success
```

---

# Step 4 : Save Build Artifacts

After build, we usually save the published application.

Modify pipeline:

```yaml id="27f3pn"
stages:
  - build

build_app:
  stage: build
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet restore
    - dotnet publish ProductApi/ProductApi.csproj
      -c Release
      -o publish

  artifacts:
    paths:
      - publish/
```

---

# What is `dotnet publish`?

```text id="m66ntf"
dotnet build
        ↓
Compiles application

dotnet publish
        ↓
Creates deployable output
```

The publish folder contains:

```text id="1nbggg"
publish/
│
├── ProductApi.dll
├── appsettings.json
├── web.config
└── other runtime files
```

GitLab stores this folder as an artifact.

---

# Pipeline Flow

```text id="9l1t0k"
Build Stage
      │
      ▼
dotnet publish
      │
      ▼
publish/
      │
      ▼
Upload Artifacts
```

---

# Step 5 : Add Test Stage

Now let's execute MSTest.

Pipeline:

```yaml id="aen7je"
stages:
  - build
  - test

build_app:
  stage: build
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet restore
    - dotnet publish ProductApi/ProductApi.csproj
      -c Release
      -o publish

  artifacts:
    paths:
      - publish/

unit_test:
  stage: test
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet test
```

---

# What Happens?

```text id="hgs5um"
Build Stage
      │
      ▼
Publish Application
      │
      ▼
Test Stage
      │
      ▼
dotnet test
      │
      ▼
Run MSTest Tests
```

---

# Expected Output

```text id="ogv1w5"
Passed! - Failed: 0
```

---

# Step 6 : Generate Test Results

GitLab can display test reports.

Generate TRX report.

```yaml id="t0zkx7"
unit_test:
  stage: test
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet test
      --logger:
      "trx;LogFileName=test_results.trx"
```

This creates:

```text id="9gvl2q"
TestResults/
        │
        └── test_results.trx
```

---

# Step 7 : Save Test Artifacts

```yaml id="xnkfux"
unit_test:
  stage: test
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet test
      --logger:
      "trx;LogFileName=test_results.trx"

  artifacts:
    when: always

    paths:
      - "**/*.trx"
```

---

# Why `when: always`?

If a test fails, the pipeline also fails.

But we still want the report.

```yaml id="x0h5si"
when: always
```

ensures the report is uploaded even when tests fail.

---

# Pipeline Flow

```text id="yn7k9e"
dotnet test
      │
      ▼
Create test_results.trx
      │
      ▼
Upload Artifact
      │
      ▼
Download From GitLab
```

---

# Step 8 : Publish Results in GitLab Tests Tab

GitLab works best with JUnit XML.

Install converter.

```yaml id="kfq6nh"
before_script:
  - dotnet tool install -g trx2junit
  - export PATH="$PATH:/root/.dotnet/tools"
```

Then:

```yaml id="1qv4f4"
script:
  - dotnet test
    --logger:
    "trx;LogFileName=test_results.trx"

  - trx2junit **/*.trx
```

---

# Publish JUnit Reports

```yaml id="ovdb8i"
artifacts:
  when: always

  reports:
    junit:
      - "**/*.xml"
```

---

# Final Pipeline

```yaml id="hrpft2"
stages:
  - build
  - test

build_app:
  stage: build
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet restore
    - dotnet publish ProductApi/ProductApi.csproj
      -c Release
      -o publish

  artifacts:
    paths:
      - publish/

unit_test:
  stage: test
  image: mcr.microsoft.com/dotnet/sdk:9.0

  before_script:
    - dotnet tool install -g trx2junit
    - export PATH="$PATH:/root/.dotnet/tools"

  script:
    - dotnet test
      --logger:
      "trx;LogFileName=test_results.trx"

    - trx2junit **/*.trx

  artifacts:
    when: always

    paths:
      - "**/*.trx"

    reports:
      junit:
        - "**/*.xml"
```

---

# Complete Flow

```text id="t4g45g"
Developer Push
      │
      ▼
GitLab Pipeline
      │
      ▼
Build Stage
      │
      ├── dotnet restore
      ├── dotnet publish
      └── publish/
                │
                ▼
            Artifact
      │
      ▼
Test Stage
      │
      ├── dotnet test
      ├── Generate TRX
      ├── Convert TRX → JUnit
      └── Upload Reports
                │
                ▼
          Tests Tab
```

---

# Folder Structure After Pipeline

```text id="6nct7y"
publish/

TestResults/
    test_results.trx

TestResults/
    test_results.xml
```

---

# Topics Learned

✅ GitLab Repository Creation

✅ Build Pipeline

✅ Docker Image (`mcr.microsoft.com/dotnet/sdk:9.0`)

✅ Build Artifacts

✅ MSTest Execution

✅ TRX Report Generation

✅ Test Artifacts

✅ JUnit Reports

✅ GitLab Tests Tab

This is an excellent beginner CI/CD project for your GitLab course because later you can extend it with:

* Code Coverage
* SonarQube
* Docker Build
* Container Registry
* Deployment
* Release Pipelines
* Environments
* Manual Approvals
