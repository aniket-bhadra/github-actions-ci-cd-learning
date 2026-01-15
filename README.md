## introduction

**CI (Continuous Integration):**
Automatically **tests**, **builds** and **merges**,  code whenever a developer pushes changes to the repository.

**CD (Continuous Deployment):**
Automatically **deploys** the tested code to **production** (or staging) after CI is successful.

ex- for react project

[Developer pushes code or opens pull request]
       ↓
[CI runs]
 - Runs tests (e.g., unit tests with Jest)
 - Builds the React app (e.g., using Webpack or Vite)
       ↓
[All tests pass ✅]
       ↓
[Code is merged into main branch]
       ↓
[Triggers CD pipeline]
 - Watches the `main` branch for changes
       ↓
[Optional: Re-run tests or rebuild for production]
       ↓
[Deploy to server/hosting platform]
 - Uploads build folder to:
     - Vercel / Netlify (automatic for React)
     - S3 + CloudFront / Firebase Hosting
     - Or any custom server
       ↓
[Website or app goes live ✅]

### What is GitHub Actions?

GitHub Actions is a platform to **automate developer workflows**.

One common example is **CI/CD**, but GitHub Actions can automate many other workflows, such as:

* **Testing** – Automatically run tests on code.
* **Scheduled Tasks** – Run scripts at set times (e.g., daily backups).
* **Code Formatting** – Auto-fix code style issues.

---

### How does GitHub Actions automate these workflows?

GitHub Actions works by responding to **GitHub events** — things that happen in or to your repository (e.g., a pull request is created, a contributor joins, a PR is merged).

You can:

* **Listen to specific events**.
* **Trigger workflows** automatically based on those events.

Each workflow is a series of actions (like sorting, labeling, assigning, etc.) that are executed **automatically** after a specific event.

This entire process is called a **workflow**.

---
To create a workflow, we need to create a .yml file. Most common workflow templates are available under the Actions tab on GitHub. We can select any of them to get started, which helps us avoid writing the workflow logic from scratch.

name: Node.js CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x, 20.x, 22.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
    - uses: actions/checkout@v4
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: npm run build --if-present
    - run: npm test

name: Node.js CI-- name of the workflow

on: events we are listening

jobs: this part executes whenever those events triggered,group set of actions that going to executed,Defines one or more jobs to run

jobs:
  build:

It’s just a name for the job.You could name it anything. 


runs-on: ubuntu-latest

> GitHub spins up a clean Linux computer (VM) in the cloud → clones your repo → installs Node → installs dependencies → builds/tests your code → deletes the VM after.

✅ Yes — this is **temporary**, and **not for hosting**.

---

### 💡 Why do all this?

> This temporary setup is used to **check if the code is correct** — build works, tests pass, no errors.

If **everything succeeds**, then:

1. ✅ You **merge the code** to the main/master branch.
2. ✅ Optionally, another workflow can **deploy the app to a real hosting platform** (like Vercel, Netlify, AWS, etc).

---

### 🧠 Purpose of GitHub Actions CI:

* Test your code before merging
* Make sure nothing breaks
* Build confidence that the code is ready for production
* **Then** you can deploy it

Why not test everything locally?
not reliable when working with team projects &
CI (like GitHub Actions) ensures same environment, auto tests, consistency every time.

If tests pass, is merge to main automatic?
No,Usually, developers review & manually merge after CI passes.


so,every time a developer pushes code (or creates a pull request), GitHub spins up a fresh **Ubuntu VM** and runs all the steps. Here's a **step-by-step breakdown** of your workflow:

---

### 🧱 Step-by-step Explanation:

#### **1. `runs-on: ubuntu-latest`**

👉 **GitHub creates a new virtual machine** running the latest Ubuntu OS.
This is where all steps will run — clean, isolated VM.

---

#### **2. `strategy: matrix: node-version: [...]`**

👉 Tells GitHub to **repeat this job 3 times** — once with **Node.js 18.x**, once with **20.x**, once with **22.x**.
This is to **test your app in multiple Node versions**.
for each Node.js version in the matrix, GitHub spins up a separate VM.

---

### Inside `steps:` — runs inside each VM (per version):

#### **3. `- uses: actions/checkout@v4`**

👉 **Clones your GitHub repo** (i.e., your project code) into the VM.

---

#### **4. `- uses: actions/setup-node@v4`**

👉 **Installs the correct Node.js version** in the VM using the matrix (18, 20, or 22).
Also enables **npm caching** to speed up installs.

---

#### **5. `- run: npm ci`**

👉 Installs your project dependencies (from `package-lock.json`).
Faster and stricter than `npm install`.
Fails if lock file and package.json are out of sync.

---

#### **6. `- run: npm run build --if-present`**

👉 Runs your build script (if defined in `package.json`).
If there's no build script, it **skips silently**.

---

#### **7. `- run: npm test`**

👉 Runs your tests (whatever is defined in `npm test`).
This is the key step that tells you if the code is working.

---

### ✅ Summary:

> For each Node version (18, 20, 22):
> → Create Ubuntu VM → clone code → install Node → install deps → build → test → done ✅

---

### ❓ Bonus question:

Testing the application or testing the changes — **you need to write those tests yourself**.
CI will **only run the tests that you have written**; it will **not create or write tests automatically**.

certain common Actions like cloning the GitHub repo into the VM and installing the correct Node.js version using the matrix are already created and available on github.com/actions.

We can use these pre-built actions directly in our workflow.

Additionally, we can create custom actions by making a separate repository with an action.yml file inside it. Then, we can use that custom action inside our workflow .yml file to achieve specific tasks.

To use any action, we specify it inside the workflow with the uses attribute.

### imp tricky parts
CI focuses on automatically building and testing code on every change.
GitHub Actions can automate many tasks beyond CI, like sending emails or notifications.
These extra tasks are part of automation workflows, not strictly CI.
You can combine CI and other automations in one or multiple GitHub workflows.

So here, “build” means running your project’s build scripts (e.g., webpack, babel, etc.) to make the code ready for testing or deployment.

Whenever a developer pushes changes to the repo:
CI = Build the new code + Run tests
CD = If CI is successful, deploy the tested code to the hosting environment







