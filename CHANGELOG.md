# Changelog

All notable changes to the KUAKUA brand site are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/), versions follow [Semantic Versioning](https://semver.org/).

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
