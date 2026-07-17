Absolutely. Since you want to use **GitLab Package Registry (Generic Package Registry)** instead of JFrog, the pipeline becomes much simpler because GitLab already provides built-in variables.

---

# Pipeline Flow

```text
Git Push
    ↓
Build
    ↓
Unit Test + Coverage
    ↓
Package ZIP
    ↓
Upload to GitLab Package Registry
```

---

# GitLab Variables Automatically Available

No need to create tokens manually in most cases.

GitLab provides:

```text
CI_API_V4_URL
CI_PROJECT_ID
CI_JOB_TOKEN
CI_PIPELINE_ID
CI_COMMIT_TAG
```

---

# Complete Pipeline

```yaml
stages:
  - build
  - test
  - package
  - upload

variables:
  PACKAGE_NAME: "ProductApi"
  PACKAGE_VERSION: "1.0.${CI_PIPELINE_IID}"
  ZIP_FILE: "ProductApi-${CI_PIPELINE_IID}.zip"

############################################################
# BUILD
############################################################

build_app:
  stage: build
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet --version
    - dotnet restore
    - dotnet publish ProductApi/ProductApi.csproj -c Release -o publish
    - ls -lrt publish

  artifacts:
    paths:
      - publish/
    expire_in: 1 day

############################################################
# TEST
############################################################

unit_test:
  stage: test
  image: mcr.microsoft.com/dotnet/sdk:9.0

  before_script:
    - dotnet tool install --global trx2junit
    - dotnet tool install --global dotnet-reportgenerator-globaltool
    - export PATH="$PATH:/root/.dotnet/tools"

  script:

    - dotnet test \
      --logger "trx;LogFileName=test_results.trx" \
      --collect:"XPlat Code Coverage"

    - find . -name "*.trx" -exec trx2junit {} \;

    - |
      reportgenerator \
      "-reports:**/coverage.cobertura.xml" \
      "-targetdir:coveragereport" \
      "-reporttypes:HtmlInline;Cobertura"

    - |
      FILE=$(find . -name coverage.cobertura.xml | head -1)

      COVERAGE=$(grep -oP 'line-rate="\K[^"]+' "$FILE")

      PERCENT=$(awk -v cov="$COVERAGE" \
      'BEGIN { printf "%.2f", cov*100 }')

      echo "Coverage = $PERCENT %"

      if awk -v c="$PERCENT" \
      'BEGIN { exit !(c < 10) }'
      then
        echo "Coverage below threshold"
        exit 1
      fi

  coverage: '/Coverage = (\d+\.\d+)/'

  artifacts:
    when: always

    paths:
      - coveragereport/
      - "**/*.trx"
      - "**/*.xml"

    reports:
      junit:
        - "**/*.xml"

      coverage_report:
        coverage_format: cobertura
        path: ProductUnitTest/TestResults/*/coverage.cobertura.xml

############################################################
# PACKAGE
############################################################

package_app:
  stage: package
  image: mcr.microsoft.com/dotnet/sdk:9.0

  needs:
    - job: build_app
      artifacts: true

  before_script:
    - apt-get update
    - apt-get install -y zip

  script:
    - zip -r "$ZIP_FILE" publish/
    - ls -lrt

  artifacts:
    paths:
      - "*.zip"
    expire_in: 7 days

############################################################
# UPLOAD TO PACKAGE REGISTRY
############################################################

upload_package_registry:
  stage: upload

  image: curlimages/curl:latest

  needs:
    - job: package_app
      artifacts: true

  script:

    - ls -lrt

    - |
      curl --fail \
        --header "JOB-TOKEN: ${CI_JOB_TOKEN}" \
        --upload-file "${ZIP_FILE}" \
        "${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/generic/${PACKAGE_NAME}/${PACKAGE_VERSION}/${ZIP_FILE}"

    - echo "Package Uploaded Successfully"

    - echo "Package URL"

    - echo "${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/generic/${PACKAGE_NAME}/${PACKAGE_VERSION}/${ZIP_FILE}"
```

---

# Package Structure in GitLab

After successful upload:

```text
Deploy
   ↓
Package Registry
   ↓
ProductApi
     ↓
1.0.15
     ↓
ProductApi-15.zip
```

---

# Download Package

You can download manually:

```bash
curl \
--header "JOB-TOKEN: <token>" \
-o ProductApi.zip \
"${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/generic/ProductApi/1.0.15/ProductApi-15.zip"
```

---

# Using Git Tags for Versioning

Instead of:

```yaml
PACKAGE_VERSION: "1.0.${CI_PIPELINE_IID}"
```

you can use:

```yaml
PACKAGE_VERSION: "${CI_COMMIT_TAG}"
```

Example:

```text
v1.0.0
v1.0.1
v2.0.0
```

Pipeline:

```bash
git tag v1.0.0
git push origin v1.0.0
```

Then registry becomes:

```text
ProductApi
   ↓
v1.0.0
```

---

# Enterprise Flow

```text
Build
   ↓
Test
   ↓
Coverage
   ↓
Package Registry
   ↓
Deploy Dev
   ↓
Deploy QA
   ↓
Deploy Prod
```

---

# Difference from JFrog

| Feature           | GitLab Package Registry | JFrog         |
| ----------------- | ----------------------- | ------------- |
| Setup             | Built-in                | External      |
| Tokens            | CI_JOB_TOKEN            | Access Token  |
| Versioning        | Yes                     | Yes           |
| Generic Packages  | Yes                     | Yes           |
| Security Scanning | Basic                   | Advanced Xray |
| Promotion         | Limited                 | Excellent     |

For your GitLab course, this pipeline is perfect because it demonstrates:

1. Build
2. Test
3. Coverage
4. Package
5. Package Registry Upload
6. Versioning
7. Artifact Repository concepts

all using only GitLab.
