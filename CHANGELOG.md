<!-- markdownlint-disable MD024 -->
# Changelog

All notable changes to the KUAKUA brand site are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/), versions follow [Semantic Versioning](https://semver.org/).

## [1.1.2] — 2026-06-23

### Fixed · catch PAT permission errors at login, not at publish time

A Read-only fine-grained PAT used to pass admin's gate (because the gate only did `GET /user` + `GET /repos/...`, both readable), then fail every Publish with a cryptic GitHub 403 ("Resource not accessible by personal access token"). This patch closes that gap.

- **Gate now demands write capability.** `ghVerifyPat()` reads the `permissions.push` field from `GET /repos/{owner}/{repo}` (which, for fine-grained PATs, reflects the token's effective permission on this repo). If `push !== true`, the gate refuses to let you in and explains exactly what to fix.
- **Specific error codes on every API failure.** The shared `gh()` helper now tags each non-OK response with `e.code` ∈ `{ WRITE_DENIED, NOT_FOUND, BAD_TOKEN }` so callers can branch on cause rather than parsing strings.
- **Actionable error messages everywhere.**
  - Gate: distinguishes "token rejected (401)" from "repo not in scope (404)" from "no write permission (403)" — each with a sentence on what to change in the GitHub token UI and a link to the regenerate page.
  - Publish / Delete / Image upload: when a token gets revoked or downgraded mid-session, the toast now says "Sign out, regenerate with Contents: Read & write" instead of dumping the raw GitHub response.
- **README clarified.** The PAT setup step now calls out that `Contents` must be `Read and write`, not the misleadingly-named `Access: read-only`. This is the single most common mistake.

### Why this matters

The point of the admin is to be a real, durable tool — not a "click around and find out" experiment. Every error you can hit during normal use should tell you what to do, not what HTTP status was returned.

[1.1.2]: https://github.com/chenhongqiang2025/kuakua/releases/tag/v1.1.2

## [1.1.1] — 2026-06-23

### Added · iOS / mobile polish + PWA

- **PWA manifest** (`manifest.json`) — both the public site and `/admin.html` can now be installed to the iPhone / Android home screen as a standalone app. App shortcuts for Journal and Admin included.
- **SVG icon set** at `assets/icon.svg`, `assets/icon-maskable.svg`, `assets/apple-touch-icon.svg` — KUAKUA wordmark on a warm rose-gradient backdrop. Replace with PNG equivalents in v1.2 for ideal iOS rendering.
- **`<meta name="apple-mobile-web-app-*">`** on both pages for proper iOS install behaviour (status bar, title).

### Fixed · iOS Safari pain points

- **Auto-zoom on form focus** — all form inputs bumped to ≥16px on touch / small-screen breakpoints to defeat Safari's "input < 16px → zoom in" behavior. Affects admin form fields, the gate PAT input, newsletter email, sign-in / sign-up modals.
- **Touch target sizes** — all buttons, icon-buttons, language toggle, theme toggle, mobile menu button, auth tabs, and the Markdown toolbar now meet the Apple HIG 44×44 minimum on touch devices.
- **100vh URL-bar collapse** — replaced with `100dvh` (with `100vh` fallback) on hero, reader, sidebar, layout, and gate so the bottom never gets clipped when Safari's URL bar shrinks.
- **Notch / home indicator** — `env(safe-area-inset-*)` applied to nav, topbar, sidebar, editor, reader close button, footer, toast, and gate. iPhone 14 Pro etc. no longer have content under the camera cutout.
- **Standalone PWA mode** — extra top padding when launched from the home screen so the dynamic island / notch doesn't overlap the nav.
- **Mobile keyboard hints** — added `autocapitalize`, `autocorrect`, `spellcheck`, `inputmode` on every input so iOS shows the right keyboard (email keyboard for `@`, URL keyboard for slugs, no auto-capitalize for tokens/slugs/tags/emails).
- **Sticky editor action bar** on small screens so Publish / Preview / Delete are always reachable while writing a long post.
- **`format-detection telephone=no`** so iOS doesn't auto-linkify numbers as tap-to-call.

### Changed

- Public site `<meta viewport>` now includes `maximum-scale=5` so users can still pinch-zoom (accessibility), but auto-zoom is disabled.
- Footer version pill bumped to `v1.1.1`.

### How to install to iPhone home screen

1. Open <https://chenhongqiang2025.github.io/kuakua/> in Safari.
2. Tap the share icon → **Add to Home Screen**.
3. Same for `admin.html` — install once, launch like an app.

[1.1.1]: https://github.com/chenhongqiang2025/kuakua/releases/tag/v1.1.1

## [1.1.0] — 2026-06-23

### Added · Journal + Admin

- **Journal section** on the main site — between Affirmations and Products. Three-column card grid, sortable by date, language badges (中 / EN / Bilingual), tag chips, lazy-loaded covers.
- **Full-screen reader** for individual posts, opens via card click or deep link (`#journal/<slug>`), with markdown rendering, cover image, share-friendly URL, ESC to close.
- **Minimal inline Markdown renderer** — supports H2/H3, bold, italic, links, images, ordered/unordered lists, blockquotes, horizontal rules, paragraphs, soft line breaks. XSS-safe (escapes HTML before parsing). Zero CDN dependency for long-term maintainability.
- **i18n keys** for Journal + reader (`journal.eyebrow`, `journal.title`, `journal.sub`, `journal.empty`, `reader.back`, `nav.journal`) in EN/中. Cards re-render on locale switch.
- **`data/posts.json`** seeded with a bilingual welcome post.

### Added · Admin CMS (`/admin.html`)

- Standalone admin page, brand-styled, light/dark theme synced with main site.
- **PAT-gated entry** — paste a GitHub fine-grained Personal Access Token, verified via `/user` + repo access check, stored only in this browser's localStorage. In-page step-by-step token-creation guide.
- **Editor**: title (中) + optional title (EN) + slug + datetime + language radios + tag chips + cover + excerpt + Markdown body. Auto-slug from title.
- **Markdown toolbar**: H2, H3, Bold, Italic, Link, inline image upload, bullet list, blockquote, horizontal rule. Ctrl+B / Ctrl+I shortcuts.
- **Image upload** — drag-drop the cover, or inline-upload from the toolbar. Files go to `assets/posts/YYYY-MM/<timestamp>-<safe-name>.<ext>` via GitHub Contents API. Markdown gets a relative path so the link works on GH Pages and locally.
- **Live preview pane** — toggle on/off, renders the same markdown the public site will render.
- **One-click publish** — commits `data/posts.json` (with SHA conflict-resolution: fetches fresh SHA before write) via GitHub API. Commit messages auto-formatted: `post: add "<title>" (<id>)`.
- **Edit existing posts** — sidebar lists all posts, click to load into editor; same Publish button updates in place.
- **Delete post** — soft-confirm dialog, removes post from `posts.json`. Image assets are kept (no orphan cleanup in v1).
- **Status pill** (saved · dirty · saving · error) + toast notifications + unsaved-changes guard before leaving the page.
- **Sign out** — wipes PAT from localStorage.

### Changed

- Nav, mobile menu, footer now include the Journal link.
- `applyI18n()` now also re-renders the Journal grid on language switch.
- IntersectionObserver for scroll reveals exposed as `window.__revealIO` so Journal cards (rendered after init) can register for the animation too.
- Footer version pill bumped to `v1.1.0`.

### Architecture notes

- `admin.html` is intentionally a standalone file — separate concern, separate page. Brand tokens are mirrored (duplicated) from `index.html` for self-containment. A future v1.2 may refactor shared tokens into `assets/css/brand.css`.
- Markdown renderer is duplicated between `index.html` (read) and `admin.html` (write+preview) so both pages render identically. Keep them in sync on edits.
- No backend. No server. Auth = GitHub PAT in browser localStorage. Single-user model (Richard). For multi-user editorial workflow, v2.0 will introduce a real auth backend.

[1.1.0]: https://github.com/chenhongqiang2025/kuakua/releases/tag/v1.1.0

## [1.0.0] — 2026-06-23

### Launch · Brand site v1

- Premium single-page brand site with Rhode / Alo / Skims / Lululemon-inspired aesthetic
- Custom KUAKUA brand token system (palette, typography, motion) via CSS custom properties
- Bilingual EN / 中 with one-click toggle, persisted in `localStorage`
- Light / Dark theme with system-preference detection on first visit
- Sections: Hero · Manifesto · Four Pillars · Daily Affirmations carousel · Products preview · Community · Founders · Footer
- Affirmations carousel with three categories (Mirror · Bedtime · Abundance), auto-rotating every 5.4s, pauses on hover, full bilingual copy
- Member auth modal — Sign Up / Sign In with form validation, salted SHA-256 password hashing via Web Crypto API, localStorage-backed for v1
- Newsletter signup with email validation and local capture
- Mobile-responsive with hamburger menu
- Scroll-reveal animations using `IntersectionObserver`, respects `prefers-reduced-motion`
- Inline SVG favicon, no external image dependencies
- Open Graph metadata for social sharing
- Keyboard accessible: `focus-visible` styles, ARIA roles on nav/modal/carousel, Escape closes modal
- Toast notification system for user feedback
- Footer with build-version pill (auto-updates on each release)
- Zero build step, zero dependencies — single `index.html` ready for GitHub Pages

[1.0.0]: https://github.com/chenhongqiang2025/kuakua/releases/tag/v1.0.0
