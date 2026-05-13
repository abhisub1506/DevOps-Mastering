# Creating a Local Repo and Connect to remote Repo

# Step 1: Create the Project Directory

```bash
mkdir kmit-project
cd kmit-project

Optionally Open this project in visual studio code
```

---

# Step 2: Initialize Git Repository

```bash
git init
```

This creates a hidden `.git` directory, which turns the folder into a Git repository.

---

# Step 3: Create a Simple Node.js Application

Initialize a Node.js project:

```bash
npm init -y
```

This generates a `package.json` file automatically.

---

# Step 4: Create Application File

```bash
touch app.js
```

Add the following code to `app.js`:

```javascript
console.log("Welcome to KMIT Project!");
```

You can create the file directly from the command line as well:

```bash
echo 'console.log("Welcome to KMIT Project!");' > app.js
```

---

# Step 5: Test the Application

```bash
node app.js
```

Output:

```text
Welcome to KMIT Project!
```

---

# Step 6: Check Git Status

```bash
git status
```

You will see:

```text
Untracked files:
  app.js
  package.json
```

---

# Step 7: Add Files to Git

```bash
git add .
```

---

# Step 8: Create Initial Commit

```bash
git commit -m "Initial commit with Node.js application"
```

---

# Step 9: Create `.gitignore`

```bash
echo "node_modules/" > .gitignore
```

Commit the `.gitignore` file:

```bash
git add .gitignore
git commit -m "Add gitignore for node_modules"
```

---

# Step 10: Create Repository on [GitHub](https://github.com?utm_source=chatgpt.com)

Create a new repository named:

```text
kmit-project
```

Do **not** initialize it with README, `.gitignore`, or license.

---

# Step 11: Link Local Repository to GitHub

Replace `YOUR_USERNAME` with your GitHub username.

```bash
git remote add origin https://github.com/YOUR_USERNAME/kmit-project.git
```

Verify the remote:

```bash
git remote -v
```

---

# Step 12: Push Code to GitHub

```bash
git branch -M main
git push -u origin main
```

---

# Final Project Structure

```text
kmit-project/
├── .git/
├── .gitignore
├── app.js
└── package.json
```

---

# Complete Command Summary

```bash
mkdir kmit-project
cd kmit-project
git init
npm init -y
echo 'console.log("Welcome to KMIT Project!");' > app.js
node app.js
git status
git add .
git commit -m "Initial commit with Node.js application"
echo "node_modules/" > .gitignore
git add .gitignore
git commit -m "Add gitignore for node_modules"
git remote add origin https://github.com/YOUR_USERNAME/kmit-project.git
git branch -M main
git push -u origin main
```

---

# Optional Improvements for Your Demo

You can also add:

* `README.md`
* `LICENSE`
* Express.js example
* Git branching demonstration
* Pull request workflow

---

# Recommended Demo Flow

1. Create project folder.
2. Initialize Git.
3. Initialize Node.js.
4. Write simple JavaScript.
5. Commit to Git.
6. Create GitHub repository.
7. Link remote.
8. Push to GitHub.
9. Verify files on GitHub.

