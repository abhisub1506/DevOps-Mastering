# Git Tags and Pushing Tags

Git tags are used to mark specific commits, typically for releases such as `v1.0`, `v2.0`, or `production-2026-05-16`.

---

# Why Use Tags?

Common use cases:

* Mark software releases
* Identify deployment points
* Save important milestones
* Roll back to known-good versions

Example tags:

* `v1.0.0`
* `v2.1.3`
* `release-2026-05`

---

# Create a Lightweight Tag

```bash
git tag v1.0
```

This creates a simple pointer to the current commit.

---

# Create an Annotated Tag (Recommended)

```bash
git tag -a v1.0 -m "First stable release"
```

Annotated tags store:

* Tag name
* Message
* Creator
* Date

---

# List Tags

```bash
git tag
```

---

# View Tag Details

```bash
git show v1.0
```

---

# Tag a Specific Commit

```bash
git tag -a v1.1 a1b2c3d -m "Bug fix release"
```

---

# Push a Single Tag to [GitHub](https://github.com?utm_source=chatgpt.com)

```bash
git push origin v1.0
```

---

# Push All Tags

```bash
git push origin --tags
```

---

# Clone Tags

Tags are usually fetched automatically when you clone a repository.

To fetch tags later:

```bash
git fetch --tags
```

---

# Delete a Local Tag

```bash
git tag -d v1.0
```

---

# Delete a Remote Tag

```bash
git push origin --delete v1.0
```

(Equivalent alternative: `git push origin :refs/tags/v1.0`.)

---

# Checkout a Tag

```bash
git switch --detach v1.0
```

This places you in a detached HEAD state at the tagged commit.

---

# Real-World Release Example

```bash
git add .
git commit -m "Prepare release 1.0"
git tag -a v1.0 -m "Production release"
git push origin main
git push origin v1.0
```

---

# Typical CI/CD Usage

Tools such as [Jenkins](https://www.jenkins.io?utm_source=chatgpt.com), [GitHub Actions](https://github.com/features/actions?utm_source=chatgpt.com), and [Azure DevOps](https://azure.microsoft.com/products/devops?utm_source=chatgpt.com) can trigger pipelines whenever a tag such as `v1.0.0` is pushed.

---

# Difference Between Branches and Tags

| Branch                         | Tag                                      |
| ------------------------------ | ---------------------------------------- |
| Moves as new commits are added | Permanently references a specific commit |
| Used for active development    | Used for milestones and releases         |
| Mutable pointer                | Intended to be stable                    |

---

# Summary

| Command                         | Purpose                  |
| ------------------------------- | ------------------------ |
| `git tag v1.0`                  | Create a lightweight tag |
| `git tag -a v1.0 -m "Release"`  | Create an annotated tag  |
| `git tag`                       | List tags                |
| `git show v1.0`                 | Display tag details      |
| `git push origin v1.0`          | Push one tag             |
| `git push origin --tags`        | Push all tags            |
| `git tag -d v1.0`               | Delete a local tag       |
| `git push origin --delete v1.0` | Delete a remote tag      |

---

# Most Common Release Commands

```bash
git tag -a v1.0 -m "First release"
git push origin main
git push origin v1.0
```

These commands mark the current commit as version `v1.0` and publish the tag to GitHub so it can be used for releases, deployments, and CI/CD automation.
