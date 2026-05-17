# Git Branching Strategy Explained

A **Git branching strategy** is a set of rules that defines:

* Which branches should exist
* What each branch is used for
* How code moves between branches
* When branches are merged or deleted

It helps teams collaborate in a structured and predictable way.

---

# Why Branching Strategies Matter

Without a strategy:

* Developers may commit directly to `main`
* Releases become harder to manage
* Hotfixes can disrupt ongoing development

With a strategy:

* Development is organized
* Releases are controlled
* Production remains stable
* CI/CD pipelines become easier to automate

---

# Common Branching Strategies

1. Git Flow
2. GitHub Flow
3. GitLab Flow
4. Trunk-Based Development
5. Release Branch Strategy

---

# 1. Git Flow (Most Structured)

Popularized by [Atlassian](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow?utm_source=chatgpt.com).

## Branches

* `main` → Production-ready code
* `develop` → Integration branch for ongoing work
* `feature/*` → New features
* `release/*` → Release preparation
* `hotfix/*` → Emergency production fixes

## Example

```text
main
 ├── hotfix/payment-bug
develop
 ├── feature/login
 ├── feature/dashboard
 └── release/v1.0
```

## Workflow

1. Create `feature/login` from `develop`
2. Merge into `develop`
3. Create `release/v1.0`
4. Merge release into `main`
5. Tag `v1.0`
6. Merge back into `develop`

## Best For

* Large teams
* Scheduled releases
* Enterprise environments

---

# 2. GitHub Flow (Simple and Popular)

Recommended by [GitHub](https://docs.github.com/get-started/using-github/github-flow?utm_source=chatgpt.com).

## Branches

* `main`
* Short-lived feature branches

## Workflow

1. Create `feature/login` from `main`
2. Open a pull request
3. Review and test
4. Merge into `main`
5. Deploy

## Best For

* Continuous deployment
* Small to medium teams

---

# 3. Trunk-Based Development

Used widely in high-velocity engineering organizations.

## Branches

* `main` (the trunk)
* Very short-lived branches, often lasting less than a day

## Workflow

* Commit frequently to `main`
* Use feature flags to hide incomplete work

## Best For

* DevOps and CI/CD-focused teams
* Rapid delivery

---

# 4. Release Branch Strategy

## Branches

* `main`
* `develop`
* `release/x.y`

Used when releases must be stabilized while new development continues.

---

# Real-World Example for a Banking Project

```text
main                 -> Production
develop              -> Integration
feature/upi-payment  -> New payment feature
feature/kyc-module   -> KYC enhancements
release/v2.0         -> Pre-production testing
hotfix/otp-fix       -> Urgent production issue
```

---

# Typical Commands

```bash
git switch develop
git switch -c feature/login

# Work and commit
git add .
git commit -m "Add login module"

git switch develop
git merge feature/login
git branch -d feature/login
```

---

# Which Strategy to Choose?

| Team Type                       | Recommended Strategy                |
| ------------------------------- | ----------------------------------- |
| Individual developer            | GitHub Flow                         |
| Small startup                   | GitHub Flow                         |
| Enterprise with formal releases | Git Flow                            |
| Strong CI/CD culture            | Trunk-Based Development             |
| Regulated environments          | Git Flow or Release Branch Strategy |

---

# Recommended Strategy for DevOps Training

For your training projects such as `kmit-project`, a practical and easy-to-understand approach is:

```text
main
dev
feature/*
```

Workflow:

1. `main` = stable production code
2. `dev` = integration and testing
3. `feature/*` = individual enhancements
4. Merge feature branches into `dev`
5. Merge `dev` into `main` after validation

This closely matches real-world enterprise development.

---

# Visual Diagram

```text
main
  ↑
  └── dev
        ├── feature/login
        ├── feature/dashboard
        └── feature/api
```

---

# Summary

A Git branching strategy defines how branches are created, used, and merged.

The most common strategies are:

* Git Flow
* GitHub Flow
* Trunk-Based Development

For enterprise DevOps training, a `main → dev → feature/*` strategy provides an excellent balance between simplicity and real-world relevance.
