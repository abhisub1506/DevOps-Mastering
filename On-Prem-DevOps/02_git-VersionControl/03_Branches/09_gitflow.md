# Git Flow Commands (`git-flow` Tool)

Yes. In addition to standard Git commands, there is a popular extension called **git-flow** that provides high-level commands to implement the Git Flow branching strategy automatically.

The project is available at [git-flow AVH Edition](https://github.com/petervanderdoes/gitflow-avh?utm_source=chatgpt.com).

---

# Install Git Flow

## Windows
1. Download the gizp file from https://sourceforge.net/projects/git-flow-avh-edition.mirror/files/
2. Extract the Scripts: Open your downloaded Release 1.12.3.zip folder.
3.  Find the files inside named: git-flow,git-flow-init,git-flow-feature,git-flow-release,git-flow-hotfix
4. Move to Git Folder: Open a separate File Explorer window, navigate to your computer's main Git path C:\Program Files\Git\usr\bin
5. Paste: Copy those files out of the ZIP and paste them directly into that usr\bin folder
6. Close the Terminal and Open a new terminal or command prompt
7. Run git flow version 

# Step-by-Step Example of `git flow init` and `git flow feature start`

This is the best way to understand how Git Flow works in practice using your sample project, `kmit-project`.

---

# 1. Create a New Project

```bash
mkdir kmit-project
cd kmit-project
git init
npm init -y
echo 'console.log("Welcome to KMIT Project");' > app.js
git add .
git commit -m "Initial commit"
```

At this point, the repository has only one branch:

```text
main
```

---

# 2. Install and Initialize Git Flow

After installing the `git-flow` extension, run:

```bash
git flow init
```

Git asks several questions.

```text
Branch name for production releases: [main]
Branch name for "next release" development: [develop]
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
```

Press **Enter** to accept all defaults.

---

# 3. What `git flow init` Does

Internally, Git Flow:

1. Keeps `main` as the production branch.
2. Creates a new branch named `develop`.
3. Checks out `develop`.
4. Stores Git Flow configuration in the repository.

---

# 4. Verify the Branches

```bash
git branch
```

Output:

```text
* develop
  main
```

---

# 5. Visual Diagram After Initialization

```text
main
  \
   develop
```

Both branches point to the same initial commit.

---

# 6. Start a Feature Branch

```bash
git flow feature start login
```

This creates and checks out:

```text
feature/login
```

The branch is created from `develop`.

---

# 7. Verify Branches

```bash
git branch
```

Output:

```text
develop
* feature/login
main
```

---

# 8. Visual Diagram

```text
main
  \
   develop
      \
       feature/login
```

---

# 9. Add Feature Code

```bash
echo 'console.log("Login feature added");' >> app.js
git add .
git commit -m "Add login feature"
```

Now the feature branch contains one additional commit.

---

# 10. Finish the Feature

```bash
git flow feature finish login
```

This command automatically:

1. Switches to `develop`
2. Merges `feature/login` into `develop`
3. Deletes `feature/login`
4. Leaves you on `develop`

---

# 11. Resulting Branches

```bash
git branch
```

Output:

```text
* develop
  main
```

---

# 12. Visual Diagram After Feature Completion

```text
main
  \
   develop (includes login feature)
```

---

# 13. Push Branches to [GitHub](https://github.com?utm_source=chatgpt.com)

```bash
git push -u origin main
git push -u origin develop
```

---

# 14. Publish a Feature Branch (Optional)

If you want to share a feature branch before finishing it:

```bash
git flow feature publish login
```

Equivalent to:

```bash
git push -u origin feature/login
```

---

# 15. Full Example Summary

```bash
git init
npm init -y
git add .
git commit -m "Initial commit"

git flow init
git flow feature start login

# Make changes
git add .
git commit -m "Add login feature"

git flow feature finish login
```

---

# Equivalent Standard Git Commands

## `git flow init`

```bash
git switch -c develop
```

## `git flow feature start login`

```bash
git switch develop
git switch -c feature/login
```

## `git flow feature finish login`

```bash
git switch develop
git merge feature/login
git branch -d feature/login
```

---

# Real-World Interpretation

* `main` = Production code
* `develop` = Integration branch
* `feature/login` = Temporary branch for the login feature

A developer works in `feature/login`, and when complete, the changes are merged into `develop`.

---

# Final Diagram

```text
main
  \
   develop
      \
       feature/login  (temporary)
```

After finishing the feature, `feature/login` is removed and its commits become part of `develop`.

---

# Key Takeaway

* `git flow init` prepares the repository by creating and configuring `develop`.
* `git flow feature start login` creates `feature/login` from `develop`.
* `git flow feature finish login` merges the feature into `develop` and deletes the feature branch.

This is the foundation of the Git Flow branching model used in many enterprise projects.


# Main Git Flow Commands

| Command                             | Purpose                                                       |
| ----------------------------------- | ------------------------------------------------------------- |
| `git flow init`                     | Configure Git Flow in the repository                          |
| `git flow feature start <name>`     | Create a feature branch from `develop`                        |
| `git flow feature finish <name>`    | Merge the feature into `develop` and delete the branch        |
| `git flow release start <version>`  | Create a release branch from `develop`                        |
| `git flow release finish <version>` | Merge the release into `main` and `develop`, and create a tag |
| `git flow hotfix start <version>`   | Create a hotfix branch from `main`                            |
| `git flow hotfix finish <version>`  | Merge the hotfix into `main` and `develop`, and create a tag  |

---

