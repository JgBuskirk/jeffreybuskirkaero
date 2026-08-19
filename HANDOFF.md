# Handoff — Jeffrey Buskirk portfolio site

Technical reference for whoever (human or Claude) picks this project up next. Written after the initial
build-and-deploy session and several rounds of content edits. `DEPLOY.md` and `GITHUB_NETLIFY_SETUP.md` in
this same folder are beginner-facing walkthroughs written for the site owner (Jeff) to follow by hand —
they're historical reference, not required reading if you're an agent operating the CLIs directly.

## What this is

A static single-page portfolio site: `index.html` + `assets/` (images, PDFs, one video). No build step, no
framework, no `package.json` for the site itself — just open `index.html` in a browser or serve the folder.
The `assets/` folder must stay next to `index.html`; every reference in the HTML is a relative path.

**Location:** `C:\Users\jgbus\Projects\Jeff_Portfolio` — must stay **outside OneDrive**. An earlier copy
lived under OneDrive and OneDrive's sync locking corrupted `.git` (stuck `HEAD.lock` / `objects/maintenance.lock`
files) badly enough that the repo had to be deleted and reinitialized. Don't move this folder back under
OneDrive.

## Live infrastructure

| What | Where |
|---|---|
| Source | `github.com/JgBuskirk/jeffreybuskirkaero` (public), branch `main` |
| Host | Netlify, site name `jeffreybuskirkaero`, site_id `4ee1fa93-6e73-4f52-9511-3f7f27240e92` |
| Netlify account | `jgbuskirk@gmail.com`, account slug `jgbuskirk` (Free plan) |
| Live URL | `https://jeffreybuskirkaero.com` (also `jeffreybuskirkaero.netlify.app`) |
| Deploy trigger | Netlify's GitHub App webhook — every push to `main` auto-deploys, build command blank, publish dir = repo root |
| DNS | Registered at Squarespace, **not** delegated to Netlify nameservers. Manual records at Squarespace: `A @ → 75.2.60.5`, `CNAME www → jeffreybuskirkaero.netlify.app`. HTTPS cert is Netlify-managed (Let's Encrypt), auto-renews. |

**Everyday publish workflow:** edit files → `git add -A && git commit -m "..." && git push` → Netlify
deploys automatically within ~30–60s. No Netlify CLI interaction needed for routine content edits.

## Windows/PowerShell environment quirks

This machine did **not** have git, Node, GitHub CLI, or Netlify CLI installed at session start — they were
installed via `winget` (git, node) and `npm install -g netlify-cli`. All are now installed, but:

- **New PowerShell sessions don't inherit the updated PATH.** Every command block needs this first:
  ```powershell
  $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
  ```
- **`netlify` CLI's `.cmd` shim mangles embedded double-quotes** in `--data '{"key":"val"}'` JSON arguments
  on Windows PowerShell 5.1 — the quotes get silently stripped before reaching Node. Workaround: call the
  underlying script directly and backslash-escape the inner quotes:
  ```powershell
  node "C:\Users\jgbus\AppData\Roaming\npm\node_modules\netlify-cli\bin\run.js" api getSite --data '{\"site_id\":\"4ee1fa93-6e73-4f52-9511-3f7f27240e92\"}'
  ```
- **`netlify init` and `netlify login`/`gh auth login` are interactive wizards.** `netlify init` uses
  inquirer prompts and crashes outright under non-interactive stdin (`ERR_USE_AFTER_CLOSE`) — don't use it.
  For anything `init` would do (linking a site to a GitHub repo for CD), use the Netlify **dashboard** UI
  instead (Add new site → Import an existing project → Deploy with GitHub), or the `netlify api <method>`
  passthrough for things that have a real API method. `netlify login` / `gh auth login --web` DO work
  non-interactively — they print a one-time code/URL and then poll, so launch in background, read the
  output file for the code/URL, relay it to the user, and let the background task finish once they
  authorize in browser.
- **`netlify api --list`** shows every available operation ID if you need to find one
  (`getSite`, `getDnsZones`, `createDnsZone`, `configureDNSForSite`, etc.). Not everything works cleanly
  from a raw API call, though — `createDnsZone`/`configureDNSForSite` both returned opaque 500s when tried
  directly; the domain ended up added via the dashboard UI instead (2 clicks), which worked fine.
- **Local DNS resolver on this machine/network sometimes returns bogus results for the domain**
  (`146.112.x.x` range, TTL of 3–5s — looks like a Cisco Umbrella/OpenDNS "newly registered domain" security
  filter). If a live-site check fails mysteriously, verify against a public resolver before assuming
  anything is actually broken: `Resolve-DnsName jeffreybuskirkaero.com -Server 1.1.1.1`.

## Local testing before pushing

No test suite — verification is "does it render correctly." Pattern used throughout this project:
```powershell
Set-Location "C:\Users\jgbus\Projects\Jeff_Portfolio"
python -m http.server 8000   # run in background
```
Then drive it with Playwright (installed on-demand via `npx playwright install chromium` +
`npm install playwright` in a scratch folder — not part of this repo). Write a small `.js` script per
check (click tabs, screenshot a region, check `page.locator(...).count()`), run with `node script.js`.
**Always stop the port-8000 listener when done** (`Get-NetTCPConnection -LocalPort 8000 -State Listen |
Stop-Process`) or the next `http.server` invocation fails.

## Site architecture: the tab navigation system

The site *looks* like a multi-page app with tabs (About / Experience / Rocketry / Research & Code / Awards
/ Contact) but is still one HTML file with everything scroll-hidden/shown by JS — no reloads.

- Every top-level section (`header#top`, `section#about`, `#experience`, `#rocketry`, `#research`,
  `#awards`, `#contact`) has `class="tab-panel"`.
- `[hidden]{display:none!important}` in the CSS guarantees the `hidden` attribute wins even on elements
  with their own `display` value (e.g. `header.hero{display:flex}`).
- JS `TABS` array whitelists valid tab ids. `showTab(id, push)` toggles `.hidden` on all `.tab-panel`s,
  toggles `.active` on any `a[data-nav]` whose `href` matches, scrolls to top, adds `.in` to every `.reveal`
  element inside the now-visible panel (this replaced the old scroll-triggered `IntersectionObserver`
  reveal-on-scroll animation, which doesn't fire on hidden elements).
- **A single delegated click listener on `document`** intercepts *any* `<a href="#id">` click site-wide —
  nav links, hero CTA buttons, inline cross-links like "see the Rocketry section" — and routes it through
  `showTab()`. This means adding a new internal link anywhere on the page just requires `href="#tabid"`;
  no per-link JS wiring needed. Only nav-bar links (and the brand logo) carry `data-nav` for active-state
  highlighting.
- Hash changes (`history.pushState`) make tabs deep-linkable and back-button-aware via `popstate`.

## Current content structure (as of this handoff)

Rocketry section, in order:
1. **Sonoran Wildcat** — team's pre-existing N₂O development engine (propulsion system predates Jeff's
   involvement). His contributions: fiberglass design/process testing, built the full feed system. Won
   1st place FAR-OUT Category A; first team ever to launch a liquid rocket twice in one competition. Runs
   on N₂O oxidizer, has flown IPA/E98/WD-40 as fuel — it's a dev/testbed engine, not a fixed-propellant
   flight vehicle. The Rocketry section's launch video (`assets/clip.mp4`) lives here, at the top.
2. **Jeb** — 1,400 lbf N₂O/ethanol engine, Jeff's Propulsion Manager–era project. Had a hard start on
   first hot-fire; root-caused via injector CFD, redesigned the injector, refurbished the engine.
3. **Jeb Lite** — Jeff's current Chief Engineer–era project. **Still in design, ahead of PDR — not yet
   built or test-fired.** Metrics (8:1 T/W, 600 lbf) are labeled "target," not achieved. Features a
   hot-swappable nozzle: a regen nozzle for ground-test-only static fires, a heat-sink nozzle for
   competition flights (better thrust-to-weight). Full-vehicle CAD renders exist for both configurations
   (`Regen_CAD.png` vs. the older `jeblite_cad.png`, which is actually the heat-sink version — reused, not
   dead weight). `Regen_Nozzle_Section_cut.png` is the cross-section render, shown in the 4-panel figtrio
   alongside the nozzle contour/heat-flux/channel-sizing analysis images.

**Career timeline** (Experience tab) has two separate Wildcat Rocket Engineering entries — do not merge
them back into one:
- Chief Engineer, Fall 2026–Present (current, orange/active styling)
- Propulsion Manager, Fall 2025–Summer 2026 (past, default styling)

**GPA is 3.91.** It appears in three places if it ever needs updating again: hero stats (`.stats`), the
About paragraph, and the Awards section's Franke Honors College card. `grep -n "3\.91"` to find all of them.

## Naming inconsistency (known, intentional)

Most assets are `lowercase_snake_case.ext`. Two newer ones the user saved himself are PascalCase
(`Regen_CAD.png`, `Regen_Nozzle_Section_cut.png`). Left as-is rather than renamed — not a bug.
