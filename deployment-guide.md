# Deploying TalkifAI Docs to `docs.talkifai.dev`

**Setup:** `talkifai-docs` GitHub repo → Mintlify → `docs.talkifai.dev`
**DNS managed through:** Vercel

---

## Part 1 — Mintlify Dashboard Setup

### Step 1: Create Mintlify Account & Connect Repo

1. Go to **[dashboard.mintlify.com](https://dashboard.mintlify.com)** → Sign up / Log in
2. Go to **Settings → Deployment → Git Settings**
3. Click **Connect Repository** → choose GitHub
4. Select your `talkifai-docs` repo
5. Set branch to `main`

### Step 2: Install the Mintlify GitHub App

1. Dashboard → **Settings → GitHub App → Install**
2. Select **"Only select repositories"**
3. Choose only `talkifai-docs`
4. Approve all permissions → confirm

Once installed, every push to `main` will automatically trigger a new deployment.

### Step 3: Add Custom Domain in Mintlify

1. Dashboard → **Settings → Custom Domain**
2. Enter: `docs.talkifai.dev`
3. Mintlify will give you a **CNAME value** (e.g. `mintlify.app` or similar)
4. **Copy that CNAME value** — needed for the next step

---

## Part 2 — Vercel DNS Setup

Since your domain DNS is managed in Vercel:

1. Go to **[vercel.com/dashboard](https://vercel.com/dashboard)**
2. Click your **talkifai.dev** domain → go to the **DNS** tab
3. Click **Add Record** and fill in:

| Field | Value |
|-------|-------|
| **Type** | `CNAME` |
| **Name** | `docs` |
| **Value** | *(the CNAME value Mintlify gave you)* |
| **TTL** | 60 |

4. Click **Save**

> **Note:** Do NOT enable Vercel proxy/routing for this record. It should be a plain DNS pointer to Mintlify's servers.

---

## Part 3 — Verify & Go Live

1. Wait **5–10 minutes** for DNS to propagate
2. Go back to Mintlify dashboard → **Settings → Custom Domain**
3. Click **Verify Domain**
4. Mintlify auto-provisions the SSL certificate (HTTPS) once verified

Your docs are now live at `docs.talkifai.dev`.

---

## After Setup — How Updates Work

```
Edit docs locally
       ↓
git add . && git commit -m "update docs"
       ↓
git push origin main
       ↓
Mintlify auto-deploys (~2 minutes)
       ↓
docs.talkifai.dev updates automatically
```

No manual deployment steps needed after the initial setup.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Deployment not triggering after push | Settings → GitHub App → confirm `talkifai-docs` is in active installations |
| Wrong branch | Settings → Git Settings → verify branch is set to `main` |
| Domain not resolving | Wait 30 min for DNS propagation, verify CNAME with `dig docs.talkifai.dev CNAME` |
| SSL certificate not provisioning | Temporarily disable Vercel proxy on the DNS record, let Mintlify verify, then re-enable |
| Build failing | Run `mintlify dev` locally first to catch errors in `mint.json` or broken page paths |

---

## Vercel Proxy Paths (if needed)

If you ever enable Vercel as a proxy in front of Mintlify, these paths **must pass through without blocking or caching**:

```
/.well-known/acme-challenge/*   ← SSL certificate verification
/.well-known/vercel/*           ← domain verification
/mintlify-assets/_next/static/* ← static assets
```
