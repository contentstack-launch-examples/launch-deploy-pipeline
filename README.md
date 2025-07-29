# Deploy to Contentstack Launch using GitHub Actions

This example demonstrates how to **deploy a file upload project to [Contentstack Launch](https://www.contentstack.com/launch/)** whenever there is a push to the `main` branch — using **GitHub Actions** and the **Contentstack CLI**.

---

## Prerequisites

Before setting up this CI/CD workflow:

- Your project should already be deployed **once manually** using Contentstack UI.
- You must have a valid **`.cs-launch.json`** configuration file in your project using cli, or you can refer to cs-launch.json in this repo, and replace the IDs as per your launch project.
- Your GitHub repository must include:
  - Project source code
  - A valid `package.json` with a `build` script (`npm run build`)
  - `.cs-launch.json` (in the root or a custom path)
  - The `otplib` package installed (used to generate TOTP tokens for two-factor authentication):
    ```bash
    npm install otplib
    ```

---

## Secrets Setup (Required)

To securely authenticate during deployment, add these secrets to your GitHub repository:

1. Navigate to: `GitHub > Your Repo > Settings > Secrets and variables > Actions`
2. Click **"New repository secret"** and add the following:

| Secret Name          | Value                                              |
|----------------------|----------------------------------------------------|
| `CSDX_EMAIL`         | Your Contentstack account email                    |
| `CSDX_PASSWORD`      | Your Contentstack account password                 |
| `CSDX_TOTP_SECRET`   | Your TOTP secret key for two-factor authentication |

---

### How to Get Your `CSDX_TOTP_SECRET`

To use TOTP-based authentication in your CI/CD pipeline:

1. **Enable Multi-Factor Authentication (MFA)** on your Contentstack account via the website.
2. **Log out** of your account.
3. When logging in again, enter your email and password.
4. You'll be shown a **QR code** and a **secret key**.
5. **Save the secret key** — this is your `CSDX_TOTP_SECRET`.
6. Use this secret to generate TOTP codes programmatically.

>  Do **not** share this key. Store it securely as a GitHub secret.

---

## How the Workflow Works

Every time you push changes to the `main` branch:

1. The workflow installs project dependencies.
2. It builds your project using `npm run build`.
3. It logs the most recent commit ID and message for traceability.
4. Then, it authenticates using Contentstack CLI.
5. Finally, it triggers a re-deployment to your existing Contentstack Launch project, uploading any changed files.

---

## Important

- Make sure you **manually deploy once** using `csdx launch` to set up your project on Launch before relying on this workflow.
- Your `.cs-launch.json` file must exist in the root (or use `--config` to point to it).
- Make sure to store all required secrets (`CSDX_EMAIL`, `CSDX_PASSWORD`, `CSDX_TOTP_SECRET`) in GitHub.

After this one-time setup, every push to `main` will **update your Launch project** — no extra steps needed.

---

## Happy Launching! 
   
