#  GitLab?

GitLab is a web-based platform that provides:

* Source Code Management (Git Repositories)
* CI/CD Pipelines
* Security Scanning (SAST, DAST, Container Security, Secrets Detection)
* Issue Tracking
* Project Management
* Package Management
* Kubernetes Integration
* Infrastructure as Code
* Monitoring & Observability
* Compliance and Governance

### Traditional Toolchain

Many companies use separate tools:

| Purpose            | Tool       |
| ------------------ | ---------- |
| Source Code        | GitHub     |
| CI/CD              | Jenkins    |
| Project Tracking   | Jira       |
| Security Scan      | SonarQube  |
| Container Registry | Harbor     |
| Monitoring         | Prometheus |

### GitLab Approach

GitLab combines everything into one platform:

```
Plan
  ↓
Code
  ↓
Build
  ↓
Test
  ↓
Secure
  ↓
Deploy
  ↓
Monitor
```

This is why GitLab is called a **Single Application for DevSecOps**.

---

# GitLab Architecture Overview

```text
Developer
    ↓
GitLab Repository
    ↓
GitLab CI Pipeline
    ↓
GitLab Runner
    ↓
Build/Test/Security Scan
    ↓
Deploy
    ↓
Production
```

### Main Components

#### 1. GitLab Server

Stores:

* Repositories
* Issues
* Merge Requests
* Pipelines
* Security Reports

#### 2. GitLab Runner

Agent that executes jobs.

Examples:

```yaml
build:
  script:
    - mvn clean package
```

Runner executes the above commands.

---

# GitLab DevSecOps Lifecycle

## 1. Plan

Project management features:

* Issues
* Epics
* Milestones
* Roadmaps
* Agile Boards

Example:

```text
Issue #101
Create Login API
```

---

## 2. Create

Developers:

* Clone repository
* Create branches
* Commit code
* Push code

Git workflow:

```bash
git clone
git checkout -b feature-login
git add .
git commit
git push
```

---

## 3. Verify

Automated testing:

* Unit Tests
* Integration Tests
* Code Quality

Pipeline runs automatically.

Example:

```yaml
test:
  script:
    - npm test
```

---

## 4. Package

Create artifacts:

```yaml
build:
  script:
    - mvn package
```

Outputs:

```text
application.jar
docker image
zip package
```

---

## 5. Secure (DevSecOps)

This is where GitLab becomes very powerful.

### SAST (Static Application Security Testing)

Analyzes source code without running it.

Detects:

* SQL Injection
* Hardcoded Passwords
* XSS Vulnerabilities

Flow:

```text
Source Code
      ↓
SAST Scan
      ↓
Security Report
```

---

### DAST (Dynamic Application Security Testing)

Tests running applications.

Detects:

* Authentication Issues
* Open Ports
* Runtime Vulnerabilities

Flow:

```text
Running Web App
      ↓
DAST Scan
      ↓
Security Report
```

---

### Secret Detection

Detects:

* AWS Keys
* Azure Credentials
* API Tokens

Example:

```python
AWS_KEY=AKIA....
```

GitLab can block the commit.

---

### Dependency Scanning

Checks:

```xml
log4j
spring
npm
pip
```

for known CVEs.

---

### Container Scanning

Scans Docker images.

```docker
FROM ubuntu:18.04
```

Finds vulnerable packages.

---

## 6. Release

Automated releases:

```yaml
release:
  script:
    - create-release
```

Creates:

* Tags
* Release Notes
* Packages

---

## 7. Deploy

### Deployment Targets

* VM
* Kubernetes
* AWS
* Azure
* GCP
* On-Prem

Example:

```yaml
deploy:
  script:
    - kubectl apply -f deployment.yaml
```

---

## 8. Monitor

GitLab can integrate with:

* Prometheus
* Grafana
* Kubernetes

Monitor:

* CPU
* Memory
* Application Health
* Response Time

---

# GitLab CI/CD Pipeline

This is the heart of GitLab.

Configuration file:

```text
.gitlab-ci.yml
```

Example:

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - mvn package

test:
  stage: test
  script:
    - mvn test

deploy:
  stage: deploy
  script:
    - kubectl apply -f deployment.yaml
```

Pipeline Flow:

```text
Developer Push
      ↓
Build
      ↓
Test
      ↓
Security Scan
      ↓
Deploy
```

---

# GitLab Runners

Types:

### Shared Runner

Used by multiple projects.

### Specific Runner

Dedicated to one project.

### Group Runner

Used by projects within a group.

Runner Executors:

* Shell
* Docker
* Kubernetes
* Virtual Machine

Most common:

```text
GitLab Runner + Docker
```

---

# GitLab Security Dashboard

GitLab provides centralized visibility.

Shows:

* SAST Findings
* DAST Findings
* Vulnerabilities
* Secrets
* Container Risks

Security Team can:

* Review
* Assign
* Track
* Fix

This is where **SecOps** comes into the picture.

---

# What is SecOps?

### Traditional Model

```text
Developer
     ↓
Build App
     ↓
Security Team checks later
```

Problems:

* Vulnerabilities found late
* Delayed releases

### DevSecOps Model

```text
Developer
     ↓
Code
     ↓
SAST
     ↓
DAST
     ↓
Container Scan
     ↓
Deploy
```

Security becomes part of the CI/CD pipeline.

---

# GitLab Learning Roadmap (Recommended)

### Module 1: Git Fundamentals

* Git Basics
* Branching
* Merging
* Rebasing
* Tags

### Module 2: GitLab Basics

* Projects
* Groups
* Users
* Access Control
* Merge Requests

### Module 3: GitLab CI/CD

* Pipelines
* Stages
* Jobs
* Artifacts
* Variables
* Runners

### Module 4: Advanced CI/CD

* Parent/Child Pipelines
* Multi-project Pipelines
* Dynamic Pipelines
* Templates

### Module 5: Docker & GitLab

* Build Docker Images
* Container Registry
* Docker Runner

### Module 6: Kubernetes & GitLab

* Deployments
* Helm
* GitOps
* Environments

### Module 7: GitLab Security

* SAST
* DAST
* Dependency Scanning
* Secret Detection
* Container Scanning

### Module 8: GitLab SecOps

* Vulnerability Management
* Security Dashboard
* Compliance
* Policies

### Module 9: GitLab Administration

* Install GitLab CE
* Backup & Restore
* Runner Management
* LDAP/AD Integration
* High Availability

---

# Final Big Picture

```text
Developer
    ↓
GitLab Repository
    ↓
Merge Request
    ↓
CI Pipeline
    ↓
Unit Test
    ↓
SAST
    ↓
Dependency Scan
    ↓
Container Scan
    ↓
DAST
    ↓
Approval
    ↓
Deploy to Kubernetes
    ↓
Monitor
    ↓
Security Dashboard
```

If you're learning GitLab for DevOps/Cloud/Platform Engineering roles, focus first on:

1. Git Fundamentals
2. GitLab Projects & Merge Requests
3. GitLab CI/CD Pipelines
4. GitLab Runners
5. Docker Integration
6. Kubernetes Deployment
7. SAST, DAST, Dependency Scanning
8. SecOps & Security Dashboard

These 8 topics cover roughly 80-90% of what most companies use GitLab for in real-world DevSecOps environments.
