Excellent. This is called a **Quality Gate**.

The idea is:

```text id="qiv3jt"
Coverage >= 80%   → Pipeline Success

Coverage < 80%    → Pipeline Failed
```

GitLab itself does not automatically fail the pipeline based on `coverage_report`. We need to add a script that reads the Cobertura XML and checks the percentage.

---

# Step 1 : Coverage XML Structure

Your file:

```text id="89fqlq"
coverage.cobertura.xml
```

contains something like:

```xml id="5f7s9k"
<coverage line-rate="0.85"
          branch-rate="0.75"
          version="1.9">
```

Notice:

```text id="5mlp2l"
line-rate="0.85"
```

This means:

```text id="1cc2qb"
85%
```

---

# Step 2 : Add Coverage Validation

After `reportgenerator`, add:

```yaml id="1d4x8g"
    - |
      COVERAGE=$(grep -oP 'line-rate="\K[^"]+' **/coverage.cobertura.xml | head -1)

      PERCENT=$(awk "BEGIN {print $COVERAGE*100}")

      echo "Coverage = $PERCENT %"

      if awk "BEGIN {exit !($PERCENT < 80)}"; then
        echo "Coverage is below 80%"
        exit 1
      fi
```

---

However, `**` sometimes does not work on GitLab runners.

A safer version:

```yaml id="zrf2s1"
    - |
      FILE=$(find . -name coverage.cobertura.xml | head -1)

      COVERAGE=$(grep -oP 'line-rate="\K[^"]+' $FILE)

      PERCENT=$(awk "BEGIN {print $COVERAGE*100}")

      echo "Coverage = $PERCENT %"

      if awk "BEGIN {exit !($PERCENT < 80)}"; then
        echo "Coverage is below 80%"
        exit 1
      fi
```

---

# Example

If XML contains:

```xml id="s0d4gz"
line-rate="0.92"
```

Pipeline log:

```text id="nibw5o"
Coverage = 92 %

Job succeeded
```

---

If XML contains:

```xml id="g26azx"
line-rate="0.45"
```

Pipeline log:

```text id="f39tlm"
Coverage = 45 %

Coverage is below 80%

ERROR: Job failed
```

---

# Complete Job

```yaml id="1v5qwj"
unit_test:
  stage: test
  image: mcr.microsoft.com/dotnet/sdk:9.0

  before_script:
    - dotnet tool install --global trx2junit
    - dotnet tool install --global dotnet-reportgenerator-globaltool
    - export PATH="$PATH:/root/.dotnet/tools"

  script:
    - dotnet test --logger "trx;LogFileName=test_results.trx" --collect:"XPlat Code Coverage"

    - find . -name "*.trx" -exec trx2junit {} \;

    - reportgenerator \
      "-reports:**/coverage.cobertura.xml" \
      "-targetdir:coveragereport" \
      "-reporttypes:HtmlInline;Cobertura"

    - |
      FILE=$(find . -name coverage.cobertura.xml | head -1)

      COVERAGE=$(grep -oP 'line-rate="\K[^"]+' $FILE)

      PERCENT=$(awk -v cov="$COVERAGE" 'BEGIN { printf "%.2f", cov*100 }')

      echo "Coverage = $PERCENT %"

      if awk "BEGIN {exit !($PERCENT < 80)}"; then
        echo "Coverage is below 80%"
        exit 1
      fi

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
        path: "**/coverage.cobertura.xml"
```

---

# Pipeline Flow

```text id="k70n0e"
Run Unit Tests
       │
       ▼
Generate Coverage XML
       │
       ▼
Read line-rate
       │
       ▼
Coverage >= 80 ?
       │
 ┌─────┴─────┐
 │           │
Yes         No
 │           │
 ▼           ▼
Success    Fail Pipeline
```

---




