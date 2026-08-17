# Deploy your portfolio: GitHub + Netlify — complete beginner guide

Goal: get `jeffreybuskirkaero.com` live, and set up a workflow where you **edit a file, click a
couple buttons, and the change is live in under a minute.**

This guide uses **GitHub Desktop** (a free app with buttons) instead of typing git commands, because
it handles the confusing parts (login, authentication) for you. Command-line steps are included at
the end as an optional alternative.

Estimated time: ~30 minutes the first time. After that, publishing a change takes ~20 seconds.

> Terms, in plain English:
> - **Git** = a tool that tracks versions of your files.
> - **GitHub** = a website that stores your files in the cloud (a "repository" / "repo").
> - **GitHub Desktop** = an app that does git for you with buttons.
> - **Netlify** = a free host that watches your GitHub repo and publishes the site automatically.
> - **Commit** = "save a snapshot of my changes." **Push** = "upload those snapshots to GitHub."

---

## PART 0 — Set up a clean project folder (do this first)

Your site currently lives inside **OneDrive**, and OneDrive interferes with git. So we'll work from
a copy **outside** OneDrive.

1. Open **File Explorer**.
2. Go to `C:\Users\jgbus\` and create a new folder called **`Projects`**
   (full path: `C:\Users\jgbus\Projects`). This folder is NOT synced by OneDrive — that's what we want.
3. Copy the entire **`Jeff_Portfolio`** folder from
   `C:\Users\jgbus\OneDrive\Desktop\Internship Documents\` into `C:\Users\jgbus\Projects\`.
4. If you see a hidden `.git` folder inside the copy, delete it — we'll make a fresh one.
   (To see hidden folders: in File Explorer, **View → Show → Hidden items**.)

You should now have: `C:\Users\jgbus\Projects\Jeff_Portfolio\` containing `index.html`, the
`assets` folder, and the markdown guides. **This copy is the one you'll edit from now on.**

---

## PART 1 — Create a GitHub account

1. Go to **https://github.com** and click **Sign up**.
2. Use your email (`jgbuskirk@gmail.com`), pick a username (e.g. `jbuskirk` or `jeffbuskirk` — this
   can appear in URLs, so keep it clean/professional), and a password.
3. Verify your email when GitHub asks.

✅ Checkpoint: you can log in at github.com and see your (empty) dashboard.

---

## PART 2 — Install GitHub Desktop

1. Go to **https://desktop.github.com** and click **Download for Windows**.
2. Run the installer. When it opens, click **Sign in to GitHub.com** and log in with the account
   you just made. A browser window will pop up to authorize it — click **Authorize**.
3. When it asks about "Git config," just click **Continue** / **Finish** (defaults are fine).

✅ Checkpoint: GitHub Desktop is open and shows your username in the top-right.

---

## PART 3 — Turn your folder into a repository and publish it

1. In GitHub Desktop: **File → Add local repository**.
2. Click **Choose…** and select `C:\Users\jgbus\Projects\Jeff_Portfolio`.
3. If it says *"This directory does not appear to be a Git repository,"* click the blue
   **"create a repository"** link it offers.
   - **Name:** `jeffreybuskirkaero`
   - **Description:** (optional) "Personal portfolio site"
   - Leave everything else default. Click **Create repository**.
4. GitHub Desktop now shows your files as changes. At the bottom-left, type a **Summary** like
   `Initial portfolio site`, then click **Commit to main**.
   *(Commit = save a snapshot.)*
5. Click the big **Publish repository** button at the top.
   - **IMPORTANT: uncheck "Keep this code private"** so Netlify (and recruiters, if you want) can
     see it. Actually — private is fine too, Netlify can access private repos after you authorize
     it. If you're unsure, leave it public; it's a portfolio.
   - Click **Publish repository**. This uploads everything to GitHub.

✅ Checkpoint: On **github.com**, your `jeffreybuskirkaero` repo now shows `index.html` and the
`assets` folder.

---

## PART 4 — Connect Netlify (this makes it live)

1. Go to **https://app.netlify.com** and click **Sign up** → **Sign up with GitHub** (easiest —
   reuses your GitHub login). Authorize it.
2. Click **Add new site → Import an existing project**.
3. Choose **Deploy with GitHub**. Authorize Netlify to access your repositories if asked
   (you can allow just the one repo).
4. Select your **`jeffreybuskirkaero`** repository.
5. On the build settings screen:
   - **Branch to deploy:** `main`
   - **Build command:** *leave completely blank*
   - **Publish directory:** type a single dot `.` (or leave blank)
   - Click **Deploy site** (or **Deploy jeffreybuskirkaero**).
6. Wait ~30–60 seconds. Netlify shows a green **"Published"** and a temporary URL like
   `https://cheerful-otter-1234.netlify.app`. Click it — **your site is live!**
7. (Optional) **Site configuration → Change site name** → set it to `jeff-buskirk` so the temp URL
   is `jeff-buskirk.netlify.app`.

✅ Checkpoint: your portfolio loads at the Netlify URL, with all images showing.

---

## PART 5 — Point your domain (jeffreybuskirkaero.com) at Netlify

Do this after you've bought the domain (Squarespace or anywhere).

1. In Netlify: **Domain management → Add a domain** → type `jeffreybuskirkaero.com` → **Verify** →
   **Add domain**.
2. Netlify asks how to set up DNS. Choose **"Use Netlify DNS"** (simplest). It will display **4
   nameservers**, e.g.:
   ```
   dns1.p03.nsone.net
   dns2.p03.nsone.net
   dns3.p03.nsone.net
   dns4.p03.nsone.net
   ```
   Keep this tab open.
3. In a new tab, go to your domain registrar (**Squarespace → Settings → Domains →
   jeffreybuskirkaero.com → Nameservers**). Switch from "Squarespace nameservers" to
   **custom nameservers**, and paste in Netlify's 4 nameservers. **Save.**
4. Wait for DNS to propagate — usually 15 minutes to a few hours (can be up to 48h). Netlify
   automatically issues a free **HTTPS certificate** once it detects the domain.

✅ Checkpoint: `https://jeffreybuskirkaero.com` loads your site with a padlock (secure).

> Prefer NOT to change nameservers? Keep DNS at Squarespace and instead add:
> - **A record**: Host `@` → `75.2.60.5`
> - **CNAME record**: Host `www` → `<your-netlify-site-name>.netlify.app`

---

## PART 6 — Your everyday workflow (the whole point)

Whenever you want to change the site:

1. Edit files in `C:\Users\jgbus\Projects\Jeff_Portfolio\` (e.g. open `index.html` in a text editor
   or VS Code, make a change, save).
2. Open **GitHub Desktop** — it automatically lists what you changed.
3. Bottom-left: type a short **Summary** (e.g. `Update hero photo`) → click **Commit to main**.
4. Click **Push origin** (top bar).
5. Netlify sees the push and redeploys automatically. In ~30–60 seconds your live site is updated.

That's it — edit, commit, push, done.

### See changes INSTANTLY while editing (before you push)
You don't want to push just to check a tweak. Preview locally:

**Easiest — VS Code Live Server:**
1. Install **VS Code** (https://code.visualstudio.com).
2. Install the **Live Server** extension (Extensions panel → search "Live Server" → Install).
3. Open the `Jeff_Portfolio` folder in VS Code, right-click `index.html` → **Open with Live Server**.
4. Your browser opens the site and **auto-refreshes every time you save**. Sub-second feedback.

**Or, no install — Python:**
```
cd C:\Users\jgbus\Projects\Jeff_Portfolio
python -m http.server 8000
```
Open **http://localhost:8000**, edit, save, refresh.

---

## Troubleshooting

- **Images/PDF don't show on the live site:** the `assets` folder must sit right next to
  `index.html` in the repo. Check on github.com that `assets/` uploaded. Re-commit if missing.
- **"Authentication failed" / can't push:** you're signed into the wrong GitHub account in GitHub
  Desktop — **File → Options → Accounts** → sign out and back in.
- **Change didn't show up live:** confirm you clicked **Push origin** (not just Commit). Then check
  Netlify → **Deploys** for a running/failed build. Hard-refresh your browser with **Ctrl+Shift+R**
  to bypass cache.
- **Netlify build "failed":** make sure Build command is blank and Publish directory is `.`. This is
  a plain static site — there's nothing to "build."
- **OneDrive weirdness:** only ever edit the copy in `C:\Users\jgbus\Projects\` (outside OneDrive),
  never the one in OneDrive. Delete or ignore the OneDrive copy to avoid confusion.

---

## Optional — command-line version (if you'd rather not use the app)

From `C:\Users\jgbus\Projects\Jeff_Portfolio` in a terminal (Git for Windows installed):
```
git init
git add -A
git commit -m "Initial portfolio site"
git branch -M main
git remote add origin https://github.com/<your-username>/jeffreybuskirkaero.git
git push -u origin main
```
GitHub will pop a browser window to log in (or ask for a Personal Access Token instead of a
password — create one at github.com → Settings → Developer settings → Personal access tokens).
Then connect Netlify exactly as in Part 4. Daily updates:
```
git add -A
git commit -m "what changed"
git push
```
