# Deploying jeffreybuskirkaero.com

Your portfolio is a **static site** (just `index.html` + the `assets/` folder), so it can be
hosted for **free** on Netlify, GitHub Pages, or Cloudflare Pages. Below is the fastest path
(Netlify drag-and-drop) plus how to connect your Squarespace domain.

---

## Option A — Netlify drag-and-drop (recommended, ~5 minutes, no coding)

1. Go to **https://app.netlify.com/drop**
2. Create a free account (sign in with Google/GitHub/email).
3. Drag the entire **`Jeff_Portfolio`** folder onto the drop zone.
   - Make sure you drag the *folder* (which contains `index.html` at its top level), not just
     the individual files.
4. Netlify uploads it and gives you a live URL like `random-name-123.netlify.app`. Your site is
   already live — click it to check.
5. (Optional) In **Site configuration → Change site name**, rename it to something like
   `jeff-buskirk` so the temp URL is `jeff-buskirk.netlify.app`.

### Connect your custom domain (jeffreybuskirkaero.com)
1. In Netlify: **Domain management → Add a domain** → type `jeffreybuskirkaero.com` → Verify → Add.
2. Netlify will ask how you want to handle DNS. Easiest is **"Use Netlify DNS"**:
   - It shows you **4 nameservers** (e.g. `dns1.p01.nsone.net`, …).
   - Copy them.
3. In **Squarespace → Domains → jeffreybuskirkaero.com → DNS / Nameservers**, switch to
   **custom nameservers** and paste Netlify's 4 nameservers. Save.
4. Wait for DNS to propagate (usually 15 min–a few hours, up to 48h).
5. Netlify automatically provisions a free **HTTPS certificate** — your site will be live at
   `https://jeffreybuskirkaero.com` and `https://www.jeffreybuskirkaero.com`.

**Prefer to keep DNS at Squarespace instead of switching nameservers?** In Squarespace DNS add:
- an **A record**: Host `@` → value `75.2.60.5`
- a **CNAME record**: Host `www` → value `your-site-name.netlify.app`

### Updating the site later
Just drag the updated `Jeff_Portfolio` folder onto the same Netlify site (Deploys → drag to
re-deploy), or set up the GitHub method below for auto-updates.

---

## Option B — GitHub Pages (best if you want it in version control)

1. Create a free GitHub account (you already have a WREC repo, so you likely have one).
2. Make a new **public** repository named `jeffreybuskirkaero` (or anything).
3. Upload the contents of `Jeff_Portfolio` (the `index.html` and `assets/` folder) to the repo
   root — you can drag files right into GitHub's web uploader.
4. Repo **Settings → Pages → Build and deployment → Source: Deploy from a branch**, pick
   `main` / `/root`, Save. Your site goes live at `https://<username>.github.io/jeffreybuskirkaero/`.
5. To use the custom domain: in **Settings → Pages → Custom domain**, enter
   `jeffreybuskirkaero.com`, then in Squarespace DNS add:
   - four **A records** for `@` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - a **CNAME** for `www` → `<username>.github.io`
6. Check **"Enforce HTTPS"** once the certificate provisions.

---

## Option C — GitHub → Netlify continuous deploy (BEST for rapid iteration)

This gives you: push a change → live site updates in ~30–60 seconds, plus full version history.
A git repo is already initialized in this folder with a first commit.

### One-time setup
1. On **github.com**, create a new **empty** repo named `jeffreybuskirkaero`
   (no README, no .gitignore — keep it empty).
2. In a terminal in this folder, connect it and push:
   ```
   git remote add origin https://github.com/<your-username>/jeffreybuskirkaero.git
   git branch -M main
   git push -u origin main
   ```
3. On **netlify.com → Add new site → Import an existing project → GitHub**, pick the repo.
   - Build command: **(leave blank)**
   - Publish directory: **`.`** (the repo root)
   - Deploy.
4. Add your custom domain in Netlify (same as Option A's domain steps).

### Day-to-day: make a change → it's live
```
git add -A
git commit -m "describe the change"
git push
```
Netlify auto-detects the push and redeploys in under a minute.

### See changes INSTANTLY while editing (local preview)
Before pushing, preview locally with sub-second refresh:
```
cd Jeff_Portfolio
python -m http.server 8000
```
Then open **http://localhost:8000** in your browser. Edit `index.html`, save, refresh — instant.
(Or use VS Code's **Live Server** extension for auto-refresh on save.)

### ⚠️ OneDrive + git caveat
Running a git repo *inside* a OneDrive-synced folder can corrupt `.git` (OneDrive fights git for
file locks). Cleanest fix: after the first push, **clone the repo to a non-synced folder** and work
there:
```
cd C:\Users\jgbus\Projects        # any folder NOT inside OneDrive
git clone https://github.com/<your-username>/jeffreybuskirkaero.git
```
Edit → preview → commit → push from that clone. Netlify deploys from GitHub, so OneDrive is out of
the loop entirely.

---

## Which option should I use?
- **Just get it online once, no fuss:** Option A (Netlify drag-and-drop).
- **Rapid changes, want them live immediately + version history:** Option C (GitHub → Netlify). ← recommended
- Option B (GitHub Pages) also works but deploys a bit slower and can serve stale cached files.

---

## Notes
- Everything uses **relative paths** (`assets/...`), so the site works on any host with no changes.
- Keep the `assets/` folder next to `index.html` — that's where all images, the résumé PDF,
  the paper, the presentation, and the video live.
- You do **not** need a Squarespace website subscription — buying the domain alone is enough.
