# GitHub Actions Workflows-CI/CD


GitHub Pages:
🌐 https://shahab1729.github.io/practice-repo/


## 📌 Overview

GitHub Actions is a CI/CD and automation platform built into GitHub. It allows you to automatically build, test, deploy, and automate tasks whenever specific events occur in a repository.

A GitHub Actions workflow is defined using a YAML file inside:

```text
.github/
└── workflows/
    └── workflow.yml
```

---

## 🔄 How GitHub Actions Works

A typical workflow follows this process:

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    │ Trigger
    ▼
GitHub Actions Workflow
    │
    ├── Build
    ├── Test
    ├── Security Checks
    └── Deploy
```

For example:

```text
git push
   ↓
Workflow triggered
   ↓
Checkout source code
   ↓
Install dependencies
   ↓
Run tests
   ↓
Build application
   ↓
Deploy application
```

---

# 🧩 Important Concepts

## 1. Workflow

A **workflow** is an automated process defined in a YAML file.

Example:

```yaml
name: CI

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run build
        run: npm run build
```

---

## 2. Events

The `on` section defines **when the workflow should run**.

### Run when code is pushed

```yaml
on:
  push:
    branches:
      - main
```

### Run when a Pull Request is created

```yaml
on:
  pull_request:
    branches:
      - main
```

### Run manually

```yaml
on:
  workflow_dispatch:
```

### Run on a schedule

GitHub Actions also supports scheduled workflows using cron.

```yaml
on:
  schedule:
    - cron: "0 0 * * *"
```

The above example runs once per day at 00:00 UTC.

Multiple events can also be configured:

```yaml
on:
  push:
    branches:
      - main

  pull_request:

  workflow_dispatch:
```

---

# ⚙️ Jobs

A workflow contains one or more **jobs**.

```yaml
jobs:

  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
```

Each job runs independently unless dependencies are specified.

---

# 🖥️ Runners

A **runner** is the machine that executes your workflow.

Common GitHub-hosted runners include:

```yaml
runs-on: ubuntu-latest
```

Other examples:

```yaml
runs-on: windows-latest
```

```yaml
runs-on: macos-latest
```

For Linux-based DevOps workflows, `ubuntu-latest` is commonly used.

---

# 🪜 Steps

A job consists of multiple steps.

```yaml
steps:

  - name: Checkout code
    uses: actions/checkout@v4

  - name: Install dependencies
    run: npm install

  - name: Run tests
    run: npm test

  - name: Build application
    run: npm run build
```

Steps execute sequentially by default.

---

# 🧰 Actions

An **Action** is a reusable piece of automation.

For example:

```yaml
uses: actions/checkout@v4
```

This action checks out your repository's source code onto the runner.

Another example:

```yaml
uses: actions/setup-node@v4
```

This sets up Node.js on the runner.

---

# 💻 `run` vs `uses`

### `run`

Use `run` to execute shell commands.

```yaml
- name: Install dependencies
  run: npm install
```

### `uses`

Use `uses` to execute an existing GitHub Action.

```yaml
- name: Checkout repository
  uses: actions/checkout@v4
```

---

# 🔐 Permissions

GitHub Actions uses the `GITHUB_TOKEN` to interact with your repository.

Permissions can be explicitly defined:

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

For example:

```yaml
permissions:
  contents: read
```

allows the workflow to read repository contents.

For GitHub Pages deployment:

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

The `pages: write` permission allows the workflow to deploy to GitHub Pages.

---

# 🔑 Secrets

Never hard-code sensitive credentials inside workflow files.

❌ Don't do this:

```yaml
run: npm login --password mypassword
```

Instead, store sensitive values in:

```text
Repository
→ Settings
→ Secrets and variables
→ Actions
```

Then access them using:

```yaml
${{ secrets.MY_SECRET }}
```

Example:

```yaml
- name: Deploy
  run: ./deploy.sh
  env:
    API_KEY: ${{ secrets.API_KEY }}
```

---

# 🌎 Environment Variables

Environment variables can be defined at different levels.

### Workflow level

```yaml
env:
  NODE_ENV: production
```

### Job level

```yaml
jobs:
  build:
    env:
      NODE_ENV: production
```

### Step level

```yaml
- name: Build
  env:
    NODE_ENV: production
  run: npm run build
```

---

# 🔗 Job Dependencies

Jobs can depend on other jobs using `needs`.

```yaml
jobs:

  test:
    runs-on: ubuntu-latest

    steps:
      - run: npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - run: echo "Deploying..."
```

The deployment job will execute only after the test job succeeds.

This creates a basic CI/CD pipeline:

```text
        ┌──────────┐
        │   Test   │
        └────┬─────┘
             │
             ▼
        ┌──────────┐
        │  Deploy  │
        └──────────┘
```

---

# 🧪 CI Example

A simple Node.js CI workflow:

```yaml
name: Node.js CI

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:

  test:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build application
        run: npm run build
```

---

# 🚀 GitHub Pages Deployment

Static websites can be deployed using GitHub Pages and GitHub Actions.

Example:

```yaml
name: Deploy Website

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

jobs:

  deploy:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Configure GitHub Pages
        uses: actions/configure-pages@v5

      - name: Upload website
        uses: actions/upload-pages-artifact@v3
        with:
          path: .

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

The deployment flow is:

```text
git push
    ↓
GitHub Actions
    ↓
Checkout repository
    ↓
Configure Pages
    ↓
Upload website
    ↓
Deploy
    ↓
GitHub Pages
```

---

# 📦 Artifacts

Artifacts allow files generated during a workflow to be stored and passed between jobs.

For example:

```text
Source Code
     ↓
Build
     ↓
dist/
     ↓
Upload Artifact
     ↓
Deploy Job
```

This is particularly useful for frontend applications.

For example, a Vite application produces:

```text
dist/
├── index.html
├── assets/
│   ├── index.js
│   └── index.css
└── ...
```

The `dist` directory can then be deployed.

---

# 🔄 CI/CD Pipeline Example

A more realistic DevOps workflow might look like:

```text
Developer
    │
    │ git push
    ▼
GitHub
    │
    ▼
GitHub Actions
    │
    ├── Checkout
    │
    ├── Install dependencies
    │
    ├── Lint
    │
    ├── Test
    │
    ├── Build
    │
    └── Security checks
    │
    ▼
Artifact
    │
    ▼
Deployment
    │
    ▼
Production
```

---

# 🏗️ Recommended Repository Structure

```text
project/
│
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── cd.yml
│       └── deploy-pages.yml
│
├── src/
├── public/
├── package.json
├── package-lock.json
└── README.md
```

---

# 🛠️ Useful GitHub Actions

Some commonly used official actions are:

| Action                          | Purpose                   |
| ------------------------------- | ------------------------- |
| `actions/checkout`              | Checkout repository       |
| `actions/setup-node`            | Install/configure Node.js |
| `actions/setup-python`          | Install/configure Python  |
| `actions/upload-artifact`       | Upload workflow artifacts |
| `actions/download-artifact`     | Download artifacts        |
| `actions/configure-pages`       | Configure GitHub Pages    |
| `actions/upload-pages-artifact` | Upload Pages artifact     |
| `actions/deploy-pages`          | Deploy to GitHub Pages    |

---

# 🧠 Key Takeaways

### Workflow

The complete automation process.

### Event

Defines when the workflow starts.

### Job

A group of steps executed on a runner.

### Runner

The machine that executes the job.

### Step

An individual operation inside a job.

### Action

Reusable automation created by GitHub or the community.

### Artifact

Files produced by a workflow that can be stored or passed between jobs.

### Secret

Encrypted sensitive information such as API keys and credentials.

### Permissions

Controls what the workflow's `GITHUB_TOKEN` is allowed to access.

---

# 🎯 DevOps Learning Path

After understanding basic workflows, practice these progressively:

```text
1. Basic workflow
       ↓
2. Events & triggers
       ↓
3. Jobs & steps
       ↓
4. Runners
       ↓
5. Environment variables
       ↓
6. Secrets
       ↓
7. Job dependencies
       ↓
8. Artifacts
       ↓
9. Matrix builds
       ↓
10. CI pipelines
       ↓
11. CD pipelines
       ↓
12. Docker + GitHub Actions
       ↓
13. Docker Registry
       ↓
14. AWS deployment
       ↓
15. GitHub Actions + Kubernetes
```

---

## 🚀 Goal

The ultimate objective is to build a complete pipeline:

```text
Developer
    ↓
Git
    ↓
GitHub
    ↓
GitHub Actions
    ↓
Lint
    ↓
Test
    ↓
Build
    ↓
Docker Image
    ↓
Container Registry
    ↓
Cloud Infrastructure
    ↓
Production
```

This is the foundation of **CI/CD automation in modern DevOps**.

