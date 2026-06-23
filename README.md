# KUAKUA 夸夸

A self-love lifestyle brand · 2026 ·
Founded by **陈宏强 Richard Chen × 刘婷 Tina Liu**

> *Romanticize yourself. Pretty mind. Pretty life.*
> *把自己活成诗。漂亮的心，漂亮的生活。*

This repository is the **public-facing brand website** for KUAKUA — a single, dependency-free `index.html` designed for GitHub Pages and permanent international maintenance.

**Current version:** `v1.1.0` (2026-06-23)

---

## What's inside

| File | Purpose |
| --- | --- |
| `index.html` | The public brand site (single self-contained HTML) |
| `admin.html` | Browser-based CMS to publish/edit/delete journal posts |
| `data/posts.json` | Source of truth for all journal posts |
| `assets/posts/` | Uploaded images (organized by `YYYY-MM/`) |
| `CHANGELOG.md` | Version history |
| `LICENSE` | Bilingual proprietary license — All Rights Reserved |
| `.nojekyll` | Tells GitHub Pages to skip Jekyll processing |
| `.gitignore` | OS/editor noise |

## What v1.1 ships

**Public site** (`index.html`)

- Premium single-page design inspired by Lululemon · Alo · Rhode · Skims
- **Bilingual** EN / 中 — one-click toggle, persisted in `localStorage`
- **Light / Dark mode** — auto-detects system preference on first visit
- **Sections**: Hero · Manifesto · Four Pillars · Affirmations · **Journal** · Products · Community · Founders · Footer
- **Daily Affirmations carousel** — Mirror · Bedtime · Abundance
- **Journal** with full-screen reader, deep-linkable posts (`#journal/<slug>`), minimal inline Markdown renderer
- **Member auth (local v1)** — Sign Up / Sign In modal with salted SHA-256
- **Newsletter** signup
- Mobile-responsive, scroll-reveal animations, WCAG-aware

**Admin CMS** (`admin.html`) — *new in v1.1*

- PAT-gated entry (GitHub Personal Access Token, stored in browser localStorage)
- Markdown editor with toolbar (H2/H3, bold, italic, link, image, list, quote, hr) + keyboard shortcuts
- Drag-drop cover image + inline image upload via GitHub Contents API
- Live preview pane
- One-click publish (commits `data/posts.json` to `main`, GitHub Pages rebuilds in ~1 min)
- Edit existing posts, delete with confirm
- Unsaved-changes guard, status pill, toast notifications

Everything ships as **plain HTML/CSS/vanilla JS** so it runs anywhere static — GitHub Pages, Vercel, Netlify, Cloudflare Pages, S3 — with zero build step and zero npm dependencies.

---

## Local preview

Just open `index.html` in a browser. Or, for nicer routing during development:

```bash
# Python (any version)
python -m http.server 8000

# Or Node, if you prefer
npx serve .
```

Then visit <http://localhost:8000>.

---

## Deploy to GitHub Pages

The first-time setup (mirrors the 3RBeauty workflow):

```bash
cd "/c/Users/HongQiangChen/Projects/Hong Qiang Chen/【项目】/【KuaKua】"

git init
git add .
git commit -m "release: v1.0.0 — KUAKUA brand site launch"
git branch -M main
git remote add origin git@github.com:chenhongqiang2025/kuakua.git
git push -u origin main
```

Then in the GitHub repo: **Settings → Pages → Source: `main` / root**.
Site goes live at `https://chenhongqiang2025.github.io/kuakua/` within ~1 minute.

> **Why SSH (not HTTPS):** HTTPS 443 to github.com is unreliable from China mainland. SSH (port 22) is stable. Already configured for the chenhongqiang2025 identity. See `~/.ssh/config`.

### Every future update

```bash
cd "/c/Users/HongQiangChen/Projects/Hong Qiang Chen/【项目】/【KuaKua】"
git add .
git status
git commit -m "改了什么写在这里"
git push
```

GitHub Pages auto-rebuilds. Wait ~1 min, hard refresh, change is live.

### Releasing a new version

For PATCH (1.0.X) / MINOR (1.X.0) / MAJOR (X.0.0) bumps, also update:

1. `CHANGELOG.md` — add `## [x.y.z] — YYYY-MM-DD` block at top
2. `index.html` — bump `<span class="footer__version">vX.Y.Z</span>` in the footer
3. `README.md` — bump the **Current version** line above

Then:

```bash
git add CHANGELOG.md index.html README.md
git commit -m "release: vX.Y.Z"
git tag -a vX.Y.Z -m "vX.Y.Z — short description"
git push
git push --tags
```

---

## Publishing posts via the Admin CMS

Live URL: <https://chenhongqiang2025.github.io/kuakua/admin.html>
(Local: open `admin.html` directly in your browser, or via `python -m http.server`.)

### First-time setup — generate a GitHub PAT

The admin commits directly to this repo from your browser, so it needs a token with write access to `chenhongqiang2025/kuakua`.

1. Sign in to GitHub as **chenhongqiang2025** (not the IBM account).
2. Go to <https://github.com/settings/personal-access-tokens/new> — this is the **fine-grained** PAT page, not the classic one.
3. Fill in:
   - **Token name** → `KUAKUA admin`
   - **Expiration** → 1 year (or whatever you're comfortable rotating)
   - **Repository access** → *Only select repositories* → pick `chenhongqiang2025/kuakua`
   - **Repository permissions** → expand the list, set **Contents: Read and write**.
     (Metadata: Read-only will be auto-checked.)
4. Click **Generate token**, copy it once (GitHub will only show it this one time).
5. Open the admin page, paste the token, hit **Verify & continue**.
6. The token is stored in your browser's `localStorage` for this device only. Sign out wipes it.

> 🔒 **Why this is reasonably safe**: the token is scoped to exactly one repo with no admin or workflow permissions. Worst-case leak = an attacker can edit your posts; they can't change repo settings, delete the repo, or touch other repos. Rotate yearly.

### Daily workflow

1. Open admin → click **+ New post**.
2. Write title (中) + optional English title, pick language, add tags, drag in a cover image, write the body in Markdown.
3. (Optional) Toggle **Preview** to see exactly how it'll render.
4. Click **Publish**. The admin:
   - Uploads any new images to `assets/posts/YYYY-MM/...` (one commit each)
   - Updates `data/posts.json` with your new post (one more commit)
5. GitHub Pages auto-rebuilds in ~1 minute. Hard-refresh the journal section — your post is live.

### Editing or deleting an existing post

- Click any post in the left sidebar → it loads into the editor.
- Make changes → **Publish** (it updates in place, keeping the same ID).
- **Delete** removes the post from `posts.json`. Note: uploaded images are not auto-deleted (they remain in the repo as orphan assets — easy to clean up by hand later).

### When something goes wrong

- **"Token rejected"** → token expired, was revoked, or doesn't have Contents: Write. Regenerate.
- **"GitHub 409 conflict"** → someone else (or another tab) edited posts.json simultaneously. Reload the page and try again.
- **"Network error"** → check your VPN / GFW situation. The admin uses `api.github.com` over HTTPS — same path as the API in your IDE.
- **Image too large** → 5 MB cap per upload. Resize before dropping in.
- **Lost the PAT?** → no recovery. Generate a fresh one.

### Cross-posting to Xiaohongshu (the workflow)

The website is the source of truth — Xiaohongshu is a downstream distribution channel:

1. Write and publish on the KUAKUA admin first.
2. Open the published post in the reader, copy the rendered text + save the cover image.
3. Paste into Xiaohongshu, post.
4. (Optional) Same content can flow to Instagram, 微博, etc.

This way your content lives on infrastructure you own. Platforms come and go; the journal stays.

---

## Architecture & how to maintain

```text
【KuaKua】/
├── index.html          ← public site (single self-contained HTML)
├── admin.html          ← CMS for posting to the Journal (PAT-gated)
├── data/
│   └── posts.json      ← source of truth for all journal posts
├── assets/
│   └── posts/          ← uploaded post images, organized by YYYY-MM/
├── CHANGELOG.md
├── LICENSE             ← bilingual proprietary, All Rights Reserved
├── README.md
├── .nojekyll
└── .gitignore
```

The public site is **one file**, with this internal order:

```text
index.html
├── <head>
│   ├── meta · OG · favicon · Google Fonts
│   └── <style> ─ KUAKUA token system + components (CSS custom properties)
├── <body>
│   ├── <nav> + mobile menu
│   ├── <main>
│   │    ├── Hero
│   │    ├── Manifesto
│   │    ├── Pillars (4 cards)
│   │    ├── Affirmations carousel
│   │    ├── Products preview (3 cards)
│   │    ├── Community / Newsletter
│   │    └── Founders
│   ├── <footer>
│   ├── Auth modal
│   ├── Toast
│   └── <script>
│        1. I18N dictionary  ← edit copy here
│        2. State persistence
│        3-9. Subsystems (nav, theme, locale, carousel, auth, newsletter, reveal)
│        10. init()
```

### To change copy / translations

All text lives in two places only:

- `I18N.en` and `I18N.zh` JS dictionaries at the top of `<script>`
- Default English strings inline in the HTML (used as the fallback before JS runs / for SEO)

Update **both** for any copy change. Keep keys identical between `en` and `zh`.

### To change colors / typography

All design tokens are CSS custom properties in `:root` (light) and `[data-theme="dark"]`. Edit them once — every component picks up the change.

### To add a new section

Use an existing section as a template (e.g. `.pillars`). New copy goes into `I18N`. Add `class="reveal"` on the elements you want to fade in on scroll.

### To replace the placeholder visuals with real photography

Each product card currently renders a typographic placeholder (`<div class="product__visual">`). Swap with `<img>` tags inside `.product__media`. Same for founder avatars — replace the initial-letter `.founder__avatar` div with a circular `<img>`.

### To upgrade auth from localStorage → real backend (v2 path)

The auth UI is intentionally backend-agnostic. The functions to swap are:

- `handleSignup()` — replace `store.set(STORAGE.users, ...)` with a `fetch('/api/signup', ...)`
- `handleSignin()` — replace the hash compare with a `fetch('/api/signin', ...)`
- `refreshAuthUi()` — read session from a JWT cookie or `/api/me` instead of `localStorage`

Everything else (form validation, modal, error/toast UX) stays.

---

## Brand quick reference

| Field | Value |
| --- | --- |
| **Brand name** | KUAKUA 夸夸 |
| **Tagline (EN)** | Romanticize yourself. Pretty mind. Pretty life. |
| **Tagline (中)** | 把自己活成诗。 |
| **Positioning** | Self-love lifestyle brand · 18+ high-quality, high-knowledge fashionable middle-class women |
| **Reference brands** | Rhode, Alo, Skims, Lululemon |
| **Brand identity sold** | Not affirmations — the *identity* of the woman who already believes she is enough |
| **Four pillars** | Self-Love · Soft Life · High Vibration · Feminine Energy |
| **Product roadmap** | Phase 1: digital (e-book, wallpapers, app) → Phase 2: physical (cups, cases, notebooks) → Phase 3: AI-driven women's lifestyle brand + membership |

---

## Stack

- HTML5 · CSS3 (custom properties, `clamp()`, `color-mix()`, `backdrop-filter`)
- Vanilla JavaScript (no framework, no build step, no dependencies)
- Google Fonts: Cormorant Garamond · Inter · Noto Serif SC · Noto Sans SC
- Web Crypto API for password hashing (`crypto.subtle.digest`)
- `IntersectionObserver` for scroll reveals
- `localStorage` for state persistence

Chosen for **permanent maintainability**: no build pipeline to bit-rot, no npm install to break in 2030, no framework to migrate off.

---

## License & ownership

© 2026 Chenhongqiang Richard Chen. All rights reserved.
KUAKUA 夸夸 is a brand co-created by Richard Chen × Tina Liu.
