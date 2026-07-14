# Angular Project with GitLab CI/CD (Build → Unit Test → Code Coverage)

Since you already have:

```text id="dl8y6m"
dotnetproject
│
├── ProductApi
├── ProductUnitTest
└── .gitlab-ci.yml
```

We will now add an Angular project into the **same repository**.

---

# Final Repository Structure

```text id="x15j1u"
dotnetproject
│
├── ProductApi
├── ProductUnitTest
├── angular-ui
│
└── .gitlab-ci.yml
```

---

# Step 1 : Install Angular CLI

First verify NodeJS.

```bash id="r8fpyh"
node -v
npm -v
```

Install Angular CLI globally.

```bash id="gq4a0f"
npm install -g @angular/cli
```

Verify:

```bash id="0y4c2h"
ng version
```

---

# Step 2 : Create Angular Project

Go to root repository.

```bash id="iq5rz0"
cd dotnetproject
```

Create Angular project.

```bash id="n2m42c"
ng new angular-ui
```

Options:

```text id="gdxn91"
Would you like to add Angular routing ? → Yes

Which stylesheet format ? → CSS
```

---

# Repository becomes:

```text id="uwx4yq"
dotnetproject
│
├── ProductApi
├── ProductUnitTest
└── angular-ui
```

---

# Step 3 : Run Angular Application

Go inside Angular project.

```bash id="f5tbdg"
cd angular-ui
```

Install packages.

```bash id="shf9vi"
npm install
```

Run application.

```bash id="dzh6zt"
ng serve
```

Open:

```text id="s9x2e5"
http://localhost:4200
```

---

# Step 4 : Create Simple Component

Generate component.

```bash id="mnqm0l"
ng generate component products
```

or

```bash id="v7syi3"
ng g c products
```

---

# Step 5 : Add Simple Code

### products.ts

```typescript id="jlwmu0"
import { Component } from '@angular/core';

@Component({
  selector: 'app-products',
  imports: [],
  templateUrl: './products.html',
  styleUrl: './products.css',
})
//export class Products { }
export class Products {

  title:string="GitLab Angular Demo";

  getMessage()
  {
      return "Hello Angular";
  }

}
```

---

### products.component.html

```html id="wq0v4r"
<h1>{{title}}</h1>

<p>{{getMessage()}}</p>
```
### app.ts
```
import { Component, signal } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { Products } from './products/products';

@Component({
  selector: 'app-root',
  imports: [Products],
  templateUrl: './app.html',
  styleUrl: './app.css'
})


// @Component({
//   selector: 'app-root',
//   imports: [RouterOutlet],
//   templateUrl: './app.html',
//   styleUrl: './app.css'
// })
export class App {
  protected readonly title = signal('angular-ui');
}

```
### app.spec.ts
```
import { TestBed } from '@angular/core/testing';
import { App } from './app';

describe('App', () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [App],
    }).compileComponents();
  });

  it('should create the app', () => {
    const fixture = TestBed.createComponent(App);
    const app = fixture.componentInstance;
    expect(app).toBeTruthy();
  });

  it('should render products component', () => {

  const fixture =
      TestBed.createComponent(App);

  fixture.detectChanges();

  const compiled =
      fixture.nativeElement as HTMLElement;

  expect(compiled.textContent)
      .toContain('GitLab Angular Demo');

});
});

```
---

### app.html

```html id="4r3ft4"
<app-products></app-products>
```

Run:

```bash id="35h4r8"
ng serve
```

---

# Step 6 : Build Angular Project

```bash id="j8rv3f"
ng build
```

Creates:

```text id="7k0l31"
dist/
```

---

# Step 7 : Unit Testing

Angular already comes with:

```text id="u70vk0"
karma
jasmine
```

and sample test files.

Example:

```text id="q2wq9d"
products.component.spec.ts
```

---

Modify:

```typescript id="lpf7wy"
import { TestBed } from '@angular/core/testing';
import { App } from './app';

describe('App', () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [App],
    }).compileComponents();
  });

  it('should create the app', () => {
    const fixture = TestBed.createComponent(App);
    const app = fixture.componentInstance;
    expect(app).toBeTruthy();
  });

  it('should render products component', () => {

  const fixture =
      TestBed.createComponent(App);

  fixture.detectChanges();

  const compiled =
      fixture.nativeElement as HTMLElement;

  expect(compiled.textContent)
      .toContain('GitLab Angular Demo');

});
});
```

---

Run tests.

```bash id="5w0kh7"
ng test
```

Browser opens.

---

# Generate Coverage

```bash id="7j9yva"
ng test --code-coverage
```

Creates:

```text id="sy29vv"
coverage/
```

Open:

```text id="br0g2l"
coverage/index.html
```

You can see coverage report.

---

# Push to GitLab

```bash id="zj95zi"
git add .
git commit -m "Added Angular Project"
git push
```

---

# Angular .gitignore

Angular automatically creates one.

Important folders:

```text id="xnyocw"
node_modules/
dist/
coverage/
.angular/
```

should not be committed.

---

# Add Angular Build Stage

Now update pipeline.

---

# Build Angular

```yaml id="4ay8h4"
build_angular:
  stage: build
  image: node:22-alpine

  script:
    - cd angular-ui
    - npm ci
    - npm run build

  artifacts:
    paths:
      - angular-ui/dist/
```

---

# Unit Testing Stage

Angular tests by default need Chrome.

In GitLab we use:

```text id="5w2f4n"
ChromeHeadless
```

---

Install package.

```bash id="ngk8oq"
npm install --save-dev karma-junit-reporter
npm install -D @vitest/coverage-v8
```

---

### karma.conf.js

Add:

```javascript id="b3f14g"
reporters:
[
  'progress',
  'junit',
  'coverage'
]
```

Add:

```javascript id="qtk32z"
junitReporter:
{
    outputDir: 'reports'
}
```

Coverage:

```javascript id="cf3v2g"
coverageReporter:
{
    dir: 'coverage',
    reporters:
    [
      { type: 'html' },
      { type: 'cobertura' }
    ]
}
```

---

# package.json

Modify test command.

```json id="gbt7j4"
"test": "ng test --watch=false --browsers=ChromeHeadless"
```

---

# Install Chrome

Angular CI image:

```yaml id="v6vkj1"
image:
  name: cypress/browsers:node-22.19.0-chrome-139
```

---

# Unit Test Job

```yaml id="abjwm7"
unit_test_angular:
  stage: test

  image:
    name: cypress/browsers:node-22.19.0-chrome-139

  script:
    - cd angular-ui

    - npm ci

    - npm test -- --code-coverage
```

---

# Publish Reports

```yaml id="24o5kp"
artifacts:

  when: always

  paths:
    - angular-ui/coverage/
    - angular-ui/reports/

  reports:

    junit:
      - angular-ui/reports/*.xml

    coverage_report:
      coverage_format: cobertura
      path: angular-ui/coverage/**/cobertura-coverage.xml
```

---

# Final Pipeline

```yaml id="dzc5kt"
stages:
  - build
  - test

build_dotnet:
  stage: build
  image: mcr.microsoft.com/dotnet/sdk:9.0

  script:
    - dotnet restore
    - dotnet publish ProductApi/ProductApi.csproj -c Release -o publish

  artifacts:
    paths:
      - publish/

build_angular:
  stage: build
  image: node:22-alpine

  script:
    - cd angular-ui
    - npm ci
    - npm run build

  artifacts:
    paths:
      - angular-ui/dist/

unit_test_angular:
  stage: test

  image:
    name: cypress/browsers:node-22.19.0-chrome-139

  script:
    - cd angular-ui

    - npm ci

    - npm test -- --watch=false --coverage

    - echo "Coverage Files"
    - find coverage -type f

    - |
      FILE=$(find coverage -name "cobertura-coverage.xml" | head -1)

      echo "Coverage File = $FILE"

      COVERAGE=$(grep -oP 'line-rate="\K[^"]+' "$FILE")

      echo "Line Rate = $COVERAGE"

      PERCENT=$(awk -v cov="$COVERAGE" 'BEGIN { printf "%.2f", cov*100 }')

      echo "Coverage Percentage = $PERCENT %"

      if awk -v c="$PERCENT" 'BEGIN { exit !(c < 80) }'
      then
        echo "Coverage is below 80%"
        exit 1
      fi

  artifacts:
    when: always

    paths:
      - angular-ui/coverage/

    reports:
      coverage_report:
        coverage_format: cobertura
        path: angular-ui/coverage/cobertura-coverage.xml```

---

# Pipeline Flow

```text id="ekfiz6"
Developer Push
       │
       ▼
Build Stage
       │
       ├── Build .NET
       └── Build Angular
                │
                ▼
             Artifacts
                │
                ▼
Test Stage
       │
       ├── Angular Unit Tests
       ├── JUnit Reports
       └── Code Coverage
```

---

# Coverage Threshold Example

You can also fail the pipeline if coverage is below 80%.

```yaml id="hizq4e"
- |
    FILE=$(find angular-ui -name cobertura-coverage.xml | head -1)

    COVERAGE=$(grep -oP 'line-rate="\K[^"]+' $FILE)

    PERCENT=$(awk -v cov="$COVERAGE" 'BEGIN {print cov*100}')

    echo "Coverage = $PERCENT"

    if awk -v c="$PERCENT" 'BEGIN {exit !(c < 80)}'
    then
        echo "Coverage below threshold"
        exit 1
    fi
```

This gives you an Angular project with:

✅ Build

✅ Unit Testing

✅ JUnit Reports

✅ Code Coverage

✅ Coverage Threshold Validation

all inside the same GitLab repository as your ASP.NET Core project.
