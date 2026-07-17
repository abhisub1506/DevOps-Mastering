Excellent! Since JFrog upload is working, the next topic is **GitLab Package Registry**, which is GitLab's built-in artifact/package repository similar to JFrog.

For your course, first understand the concept and manually create/use it before automating via CI/CD.

# What is GitLab Package Registry?

GitLab provides built-in repositories for storing:

* NuGet Packages
* Maven Packages
* npm Packages
* Generic Packages
* Container Images
* Terraform Modules
* Helm Charts

Architecture:

```text
Developer
     ↓
GitLab Package Registry
     ↓
Deployment Server
```

Unlike JFrog, no external server is needed.

---

# Step 1: Verify Package Registry Feature

Open your project:

```text
Project
   ↓
Deploy
   ↓
Package Registry
```

You should see:

```text
Packages and Registries
    ├── Package Registry
    └── Container Registry
```

If you don't see it:

```text
Settings
   ↓
General
   ↓
Visibility, project features, permissions
```

Ensure:

```text
Packages = Enabled
Container Registry = Enabled
```

---

# Step 2: Understand Package Types

GitLab supports:

| Type    | Extension    |
| ------- | ------------ |
| Generic | zip, tar     |
| NuGet   | .nupkg       |
| npm     | npm packages |
| Maven   | jar          |
| Docker  | images       |

For your ASP.NET Core API, we will initially use:

```text
Generic Package Registry
```

---

# Step 3: Manually Upload a Generic Package

First create package locally:

```bash
dotnet publish ProductApi/ProductApi.csproj -c Release -o publish
```

Create ZIP:

Windows:

```powershell
Compress-Archive -Path publish\* `
-DestinationPath ProductApi-1.0.0.zip
```

Linux:

```bash
zip -r ProductApi-1.0.0.zip publish/
```

---

# Step 4: Get Project ID

Open:

```text
Project
   ↓
Settings
   ↓
General
```

Copy:

```text
Project ID
```

Example:

```text
Project ID = 12345678
```

---

# Step 5: Create Access Token

Go to:

```text
User Profile
   ↓
Access Tokens
```

Create token:

Scopes:

✅ api

or:

✅ write_package_registry
✅ read_package_registry

Copy token.

---

# Step 6: Manual Upload Using CURL

Syntax:

```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
     --upload-file ProductApi-1.0.0.zip \
     "https://gitlab.com/api/v4/projects/PROJECT_ID/packages/generic/ProductApi/1.0.0/ProductApi.zip"
```

Example:

```bash
curl --header "PRIVATE-TOKEN: xxxxxxxxx" \
     --upload-file ProductApi-1.0.0.zip \
     "https://gitlab.com/api/v4/projects/12345678/packages/generic/ProductApi/1.0.0/ProductApi.zip"
```

---

# Package Structure

GitLab creates:

```text
Package Registry
   ↓
ProductApi
      ↓
1.0.0
         ↓
ProductApi.zip
```

---

# Step 7: Verify Upload

Open:

```text
Deploy
   ↓
Package Registry
```

You should see:

```text
ProductApi
Version: 1.0.0
```

Click package:

```text
ProductApi
   ↓
1.0.0
```

---

# Step 8: Download Package

Download:

```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
     -o ProductApi.zip \
"https://gitlab.com/api/v4/projects/PROJECT_ID/packages/generic/ProductApi/1.0.0/ProductApi.zip"
```

---

# Package Registry vs Artifacts

### Artifacts

```text
Temporary
Linked to Pipeline
Can expire
```

### Package Registry

```text
Permanent
Versioned
Acts like repository
```

---

# Architecture

```text
Build
   ↓
ProductApi.zip
   ↓
Package Registry
   ↓
Deploy
```

---

# Step 9: NuGet Package Registry (Later)

For reusable libraries:

```bash
dotnet pack
```

Produces:

```text
CommonLibrary.1.0.0.nupkg
```

Upload to:

```text
GitLab NuGet Registry
```

---

# Recommended Learning Order

### Phase 1

Create Generic Package manually.

### Phase 2

Upload manually using CURL.

### Phase 3

Download manually.

### Phase 4

Automate through GitLab CI/CD.

### Phase 5

Create NuGet packages.

### Phase 6

Publish NuGet packages to GitLab.

---

# End Goal Architecture

```text
Git Push
    ↓
Build
    ↓
ProductApi.zip
    ↓
GitLab Package Registry
    ↓
Deployment
```

Now that you understand manual creation and upload, the next step will be:

```text
GitLab CI/CD → Package Registry Upload Stage
```

which is much simpler than JFrog because GitLab automatically provides:

```text
CI_API_V4_URL
CI_PROJECT_ID
CI_JOB_TOKEN
```

and no external credentials are required.
