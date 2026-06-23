# Changelog

All notable changes to the KUAKUA brand site are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/), versions follow [Semantic Versioning](https://semver.org/).

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
