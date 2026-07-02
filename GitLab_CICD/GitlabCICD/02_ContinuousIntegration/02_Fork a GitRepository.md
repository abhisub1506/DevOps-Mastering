

### Step 1: Open the Repository

Open the repository:

`https://gitlab.com/gitlab-course-public/learn-gitlab-app`

---

### Step 2: Fork the Repository

1. Sign in to GitLab.
2. Open the repository.
3. Click **Fork** (top-right).
4. Select your personal namespace or the group where you want to create the fork.
5. Click **Fork project**.

GitLab will create a copy of the repository under your account.

---

### Step 3: Make the Fork Private

After the fork is created:

1. Open your forked repository.
2. Go to **Settings → General**.
3. Expand **Visibility, project features, permissions**.
4. Change **Project visibility** from **Public** to **Private**.
5. Click **Save changes**.

Your project is now private.

---

### Step 4: Verify

You should now have something like:

```
Original Repository
gitlab-course-public/learn-gitlab-app (Public)

        ↓ Fork

your-username/learn-gitlab-app (Private)
```

---

## If "Private" Option Is Disabled

Some GitLab instances or organizations restrict changing the visibility of forked projects. If you cannot make the fork private, you have two alternatives:

### Option 1 (Recommended): Import the Repository

1. Go to **New Project**.
2. Select **Import project**.
3. Choose **Repository by URL**.
4. Repository URL:

   ```
   https://gitlab.com/gitlab-course-public/learn-gitlab-app.git
   ```
5. Set **Visibility** to **Private**.
6. Click **Create project**.

### Option 2: Push to a New Private Repository

```bash
git clone https://gitlab.com/gitlab-course-public/learn-gitlab-app.git
cd learn-gitlab-app

git remote remove origin

git remote add origin https://gitlab.com/<your-username>/learn-gitlab-app.git

git push -u origin main
```

This creates your own private copy without using GitLab's fork feature.

---

For your GitLab CI/CD course, I recommend **importing or creating a new private repository** instead of forking. This gives you full control over branches, runners, CI/CD pipelines, and project settings without any restrictions from the original project.
