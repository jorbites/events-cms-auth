# Jorbites CMS Auth (Cloudflare Worker) 🔐

A lightweight, secure Cloudflare Worker implementing the GitHub OAuth 2.0 exchange protocol for **Sveltia CMS** on [Jorbites Events](https://jorbites.github.io/events/admin/).

---

## 🚀 2-Minute Deployment Guide

### Step 1: Create a GitHub OAuth Application

1. Open **GitHub** &rarr; navigate to your organization **`jorbites`** (or personal profile) &rarr; **Settings** &rarr; **Developer Settings** &rarr; **OAuth Apps** &rarr; **New OAuth App**.
2. Fill in the fields:
   - **Application name**: `Jorbites Events CMS`
   - **Homepage URL**: `https://jorbites.github.io/events/admin/`
   - **Authorization callback URL**:
     `https://jorbites-cms-auth.<your-cloudflare-subdomain>.workers.dev/callback`
3. Click **Register application**.
4. Copy the **Client ID**.
5. Click **Generate a new client secret** and copy the **Client Secret**.

---

### Step 2: Deploy to Cloudflare Workers

#### Option A: Using Wrangler CLI (Recommended)

1. Navigate to this directory:
   ```bash
   cd events-cms-auth
   npm install
   ```

2. Login to Cloudflare:
   ```bash
   npx wrangler login
   ```

3. Deploy the worker:
   ```bash
   npx wrangler deploy
   ```
   *(Take note of your deployed Worker URL: `https://jorbites-cms-auth.<subdomain>.workers.dev`)*

4. Set the GitHub Client Secret securely:
   ```bash
   npx wrangler secret put GITHUB_CLIENT_SECRET
   # Paste your GitHub Client Secret when prompted
   ```

5. Set the GitHub Client ID:
   ```bash
   npx wrangler secret put GITHUB_CLIENT_ID
   # Paste your GitHub Client ID when prompted
   ```

---

#### Option B: Deploying via Cloudflare Dashboard (GUI)

1. Log in to [Cloudflare Dashboard](https://dash.cloudflare.com/) &rarr; **Workers & Pages** &rarr; **Create application** &rarr; **Create Worker**.
2. Name it `jorbites-cms-auth` and click **Deploy**.
3. Click **Quick Edit** &rarr; paste the contents of `src/index.ts` &rarr; click **Save and Deploy**.
4. Go to **Settings** &rarr; **Variables and Secrets**:
   - Add Plaintext or Secret variable `GITHUB_CLIENT_ID` = your GitHub Client ID
   - Add Secret variable `GITHUB_CLIENT_SECRET` = your GitHub Client Secret
   - Add Variable `ALLOWED_ORIGIN` = `https://jorbites.github.io`
5. Ensure your GitHub OAuth App Authorization callback URL matches:
   `https://jorbites-cms-auth.<your-subdomain>.workers.dev/callback`

---

### Step 3: Link Worker URL in `jorbites/events`

In `jorbites/events` repository, open `admin/config.yml` and set:
```yaml
backend:
  name: github
  repo: jorbites/events
  branch: main
  base_url: https://jorbites-cms-auth.<your-subdomain>.workers.dev
  auth_endpoint: auth
```

That's it! When you visit `https://jorbites.github.io/events/admin/` and click **Login with GitHub**, Sveltia CMS will authenticate through your Cloudflare Worker cleanly.
