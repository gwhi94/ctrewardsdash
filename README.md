# CT Rewards Studio

A dashboard for managing Charles Tyrwhitt on-site rewards and offers.
Hosted on GitHub Pages. Offer config stored in this repo. Bloomreach tag fetches config live at runtime.

---

## Setup (one time, ~10 minutes)

### 1. Create the repo

1. Go to [github.com/new](https://github.com/new)
2. Owner: `gwhi94`
3. Repository name: `ct-rewards`
4. Set to **Public** (required — Bloomreach fetches config from the raw URL without auth)
5. Click **Create repository**

---

### 2. Upload the files

In your new empty repo, upload these three files in this structure:

```
ct-rewards/
├── index.html          ← the dashboard (renamed from ct-rewards-admin.html)
├── data/
│   └── offers.json     ← the config data file
└── README.md           ← this file
```

**How to upload:**

1. On the repo home page, click **Add file → Upload files**
2. Upload `ct-rewards-admin.html` — then rename it to `index.html` (or rename before uploading)
3. To create `data/offers.json`: click **Add file → Create new file**, type `data/offers.json` as the filename, paste the contents of `offers.json`, commit it

---

### 3. Enable GitHub Pages

1. In your repo, go to **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: `main` / `/ (root)`
4. Click **Save**

After ~60 seconds your dashboard is live at:
**`https://gwhi94.github.io/ct-rewards/`**

---

### 4. Create a Personal Access Token (PAT)

The dashboard needs write access to commit offer changes back to the repo.

1. Go to [github.com/settings/personal-access-tokens/new](https://github.com/settings/personal-access-tokens/new)
2. Token name: `CT Rewards Studio`
3. Expiration: set to whatever suits your team (1 year is fine)
4. Repository access: **Only select repositories** → select `ct-rewards`
5. Permissions → **Contents**: set to **Read and write**
6. Click **Generate token**
7. **Copy the token now** — you won't see it again

Then open the dashboard, click **⚙ Settings**, paste the token, click **Save & Connect**.

The token is stored only in that browser's `localStorage`. Each team member who needs to save changes pastes their own token the first time they use the dashboard.

---

### 5. Add the Bloomreach tag

1. Open `ct-rewards-bloomreach-template.html`
2. Copy the entire contents
3. In Bloomreach, create a new tag (type: Custom HTML)
4. Paste the contents
5. Set it to fire on all pages
6. Publish

That's it. The Bloomreach tag now fetches live config from:
`https://raw.githubusercontent.com/gwhi94/ct-rewards/main/data/offers.json`

---

## Day-to-day workflow

```
1. Go to https://gwhi94.github.io/ct-rewards/
2. Add / edit / delete offers using the dashboard
3. Click Save — changes commit to GitHub automatically
4. Bloomreach picks up the new config on the next page load (1-min cache bust)
```

No more copy/paste into Bloomreach. No more manual template updates.

---

## How the Bloomreach tag works

```
Page loads
    │
    ├─► Suppression script runs synchronously (top-of-file)
    │       Uses fallback suppression rules to fire the UI kill-switch
    │       immediately if needed, before the fetch completes
    │
    └─► Main script fetches offers.json from GitHub (~50ms on a good connection)
            │
            ├─ Success → boots with live config
            └─ Failure → boots with fallback config (stale but functional)
```

The fallback config in the Bloomreach tag should be updated roughly every quarter.
It only activates if the GitHub fetch fails (network error, GitHub outage etc.).

---

## Files in this repo

| File | Purpose |
|------|---------|
| `index.html` | CT Rewards Studio dashboard |
| `data/offers.json` | Live offer config — edited by the dashboard |
| `ct-rewards-bloomreach-template.html` | The Bloomreach tag — paste once, never change |
| `README.md` | This file |

---

## Security note

The PAT stored in your browser's localStorage has write access to this repo's `contents` only.
It cannot access any other repo or any other GitHub feature.
Anyone with access to the GitHub Pages URL can **read** offers (the JSON is public).
Only people with a valid PAT can **write** (save changes).
