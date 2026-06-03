# Sanni's Job Search Dashboard — Deploy to GitHub Pages

This folder contains the public, encrypted version of the dashboard, ready to host on GitHub Pages.

## What's in here

- `index.html` — the dashboard (97 KB, self-contained). PII (name, email, phone, full resume) is AES-256-GCM encrypted; password unlocks on load.
- `README.md` — this file.

## The password

**`kale-jobs-2026`** — share with Sanni via DM. The dashboard auto-caches it in her browser's sessionStorage for the session, so she only enters it on first visit per session.

To change the password later: re-run the build script with a different password and redeploy. (Ask me in a new session.)

## Deploy in 6 steps (~5 minutes)

### 1. Create an empty repo on GitHub

Go to https://github.com/new and create a new **public** repo:
- Name suggestion: `sanni-jobs` (or anything you want — it becomes part of the URL)
- Public (required for free GitHub Pages)
- Do **not** add a README, .gitignore, or license (leave it empty)

### 2. From a terminal, push this folder

```bash
cd "/Users/samarthkhandelwal/Documents/Claude/Projects/2 Year Anniversary/job-search/hosted"

# Clear the partial .git left over from sandbox (one-time)
rm -rf .git

# Standard git setup
git init -b main
git add .
git commit -m "Initial dashboard deploy"
git remote add origin https://github.com/YOUR_USERNAME/sanni-jobs.git
git push -u origin main
```

Replace `YOUR_USERNAME` with your GitHub username.

> **Note:** The first `rm -rf .git` clears a half-initialized `.git` folder left by the build sandbox (which couldn't set git identity). Your local terminal will run `git init` cleanly.

### 3. Enable GitHub Pages

In the new repo on GitHub:
- Settings → Pages (left sidebar)
- Source: **Deploy from a branch**
- Branch: **`main`** · folder: **`/ (root)`**
- Save

### 4. Wait ~30–60 seconds

GitHub builds and publishes the page. The Pages settings panel will show a green "Your site is live at …" box when ready.

### 5. Test the URL

The URL will be: `https://YOUR_USERNAME.github.io/sanni-jobs/`

Open it. You should see the password gate. Enter `kale-jobs-2026`. The dashboard should unlock and start fetching jobs.

### 6. Send Sanni the link

A message like:

> Hey, built you something. Here's a live US-jobs dashboard tailored to your background — auto-refreshes each time you open it, and there's an "Apply Pack" generator (cover letter, tailored resume, recruiter outreach) on each job.
> URL: https://YOUR_USERNAME.github.io/sanni-jobs/
> Password: kale-jobs-2026
> Star ⭐ anything that looks interesting. Once you actually apply, hit "Mark as applied" so the Tracker tab tracks it.

## Updating the dashboard later

If the dashboard changes (new companies, scoring tweaks, new features), regenerate `index.html` and:

```bash
cd "/Users/samarthkhandelwal/Documents/Claude/Projects/2 Year Anniversary/job-search/hosted"
git add index.html
git commit -m "Update dashboard"
git push
```

GitHub Pages will rebuild within a minute.

## Architecture notes

- **No backend.** The dashboard fetches Greenhouse + Lever public APIs directly from Sanni's browser. Both APIs are CORS-enabled. GitHub Pages serves only the static HTML.
- **Privacy.** Without the password, the source HTML shows only a generic gate UI + an opaque base64 ciphertext blob. Sanni's name appears in the page title (not sensitive); her contact info and full resume are AES-encrypted and unreadable without the password.
- **Per-device state.** The Tracker, starred jobs, and password cache live in Sanni's browser localStorage. If she uses both laptop and phone, the trackers are independent. Move to a real backend later if she wants device sync.
- **No build step needed for updates** unless the password or PII changes. For pure dashboard logic changes (filters, scoring, UI), just regenerate and push.
