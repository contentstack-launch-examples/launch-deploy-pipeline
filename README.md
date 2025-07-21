🚀 Deploy to Contentstack Launch using GitHub Actions (File Upload Project)
This demonstrates how to deploy a file upload project to Contentstack Launch whenever there is a push to the main branch, using GitHub Actions and Contentstack CLI.



📦 Prerequisites
Before setting up this CI/CD workflow:

Your project should already be deployed once on Launch using Contentstack CLI manually.

You must have a valid .cs-launch.json configuration file in your project (typically generated during the first deploy).

Your GitHub repository must include:

Your project files

A valid package.json and build script (npm run build)

A .cs-launch.json in the root or a custom path.



🔐 Secrets Setup (Required)
To securely authenticate during deployment, store your Contentstack credentials in GitHub Secrets:

Go to your GitHub repository > Settings > Secrets and variables > Actions.

Click "New repository secret" and add:

CSDX_EMAIL → your Contentstack account email

CSDX_PASSWORD → your Contentstack account password



⚙️ GitHub Actions Workflow
Save the following code as .github/workflows/deploy.yml in your repository:

yaml
Copy
Edit
name: Deployed to Launch

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Check for .cs-launch file
        run: |
          echo "Current directory:"
          pwd
          echo "Files including dotfiles:"
          ls -la

      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install

      - name: Build the project
        run: npm run build

      - name: Show latest commit ID and message
        run: |
          echo "Latest Commit ID:"
          git log -1 --pretty=format:"%H"
          echo ""
          echo "Latest Commit Message:"
          git log -1 --pretty=format:"%s"

      - name: Install Contentstack CLI
        run: npm install -g @contentstack/cli

      - name: Verify Contentstack CLI Installation
        run: csdx --version

      - name: Set Launch Region
        run: csdx config:set:region AWS-NA

      - name: Authenticate to Contentstack
        env:
          CSDX_EMAIL: ${{ secrets.CSDX_EMAIL }}
          CSDX_PASSWORD: ${{ secrets.CSDX_PASSWORD }}
        run: csdx auth:login -u "$CSDX_EMAIL" -p "$CSDX_PASSWORD"

      - name: Deploy using Launch config
        run: csdx launch --redeploy-latest


📁 What This Does
On every push to the main branch, this workflow will:

Install dependencies and build your project.

Log the latest commit for traceability.

Use Contentstack CLI to authenticate and trigger a re-deployment of your existing Launch project.

Upload any new or modified files in your project.


💡 Why Use This?
No need to manually redeploy after every change.

Ensures consistency across environments.

Makes collaboration and CI/CD best practices easier to adopt.


✅ Important
One-time manual deploy is required.

Then, all future pushes to main auto-deploy your project to Launch.

Secure, fast, and easy automation using GitHub Actions + Contentstack CLI.

