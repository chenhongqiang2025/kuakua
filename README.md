# KUAKUA 夸夸

A self-love lifestyle brand · 2026 ·
Founded by **陈宏强 Richard Chen × 刘婷 Tina Liu**

> *Romanticize yourself. Pretty mind. Pretty life.*
> *把自己活成诗。漂亮的心，漂亮的生活。*

This repository is the **public-facing brand website** for KUAKUA — a single, dependency-free `index.html` designed for GitHub Pages and permanent international maintenance.

**Current version:** `v1.0.0` (2026-06-23)

---

## What's in v1.0

- Premium single-page brand site inspired by Lululemon · Alo · Rhode · Skims
- **Bilingual** EN / 中 — one-click toggle, persisted in `localStorage`
- **Light / Dark mode** — auto-detects system preference on first visit, persisted thereafter
- **Member auth (local v1)** — Sign Up / Sign In modal, stored in `localStorage` with salted SHA-256 hashing. v2 will move to a real backend.
- **Newsletter signup** — emails captured in `localStorage` for now
- **Daily Affirmations carousel** — Mirror · Bedtime · Abundance, auto-rotating
- **Four Pillars** section — Self-Love · Soft Life · High Vibration · Feminine Energy
- **Founders** section
- Fully responsive (mobile menu included)
- Scroll-reveal animations, respects `prefers-reduced-motion`
- Inline SVG favicon, no external assets required
- Open Graph metadata for social sharing
- WCAG-aware: keyboard-navigable, `focus-visible`, ARIA roles on nav/modal/carousel

Everything ships as **one `index.html`** so it can be hosted anywhere static — GitHub Pages, Vercel, Netlify, Cloudflare Pages, even an S3 bucket — with zero build step.

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

## Architecture & how to maintain

The whole site is **one file**, with this internal order:

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
