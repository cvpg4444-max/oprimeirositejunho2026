# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The marketing site for Cátia Effertz (AI business consultant), hosted on **GitHub Pages** at `catiaeffertz.com` (see `CNAME`). It is a **static site with no build step, no package manager, and no framework** — every page is a single self-contained `.html` file with inline `<style>` and `<script>`. There is nothing to install, compile, lint, or test; editing the HTML files directly *is* the development workflow. Preview changes by opening the file in a browser or serving the directory with any static file server (e.g. `python3 -m http.server`).

## Site structure

- `index.html` — the one-page site (nav, hero, challenges, about, services, portfolio, blog preview, newsletter, contact). This is the primary file.
- `magazine/index.html` — magazine/blog listing page (cards linking to individual articles).
- `magazine/*.html` — individual long-form articles (currently `agente-ia-whatsapp.html`, `seguranca-dados-ia-rgpd.html`).
- `politica-privacidade.html` — privacy policy page (RGPD/GDPR).
- `sitemap.xml`, `robots.txt` — SEO plumbing; every page added must be added to `sitemap.xml`.
- `CNAME` — GitHub Pages custom domain config (`catiaeffertz.com`). Do not remove.
- `catia.png`, `n8n.png` — images referenced by `index.html`; `catia.png` also doubles as the Open Graph/social share image site-wide.
- `google2f2adb9e243e3365.html` — Google Search Console verification file; do not delete or modify.

## Key conventions

### Bilingual content (PT default / EN toggle)

The whole site is bilingual (Portuguese default, English alternate) using a **CSS-driven toggle**, not a router or i18n library:

- `<html lang="pt" data-lang="pt">` — `data-lang` on the root element controls which language is visible.
- Elements tagged `class="lp"` (Portuguese) are shown when `data-lang="pt"`; elements tagged `class="le"` (English) are shown when `data-lang="en"`. The CSS rules live in each page's `<style>` block (search for `[data-lang="pt"]`).
- The `toggleLang()` JS function (in `index.html`'s inline `<script>`) flips `data-lang` and toggles the EN/PT label state.
- **Every user-facing string needs both an `.le` and `.lp` version placed side by side** (see hero, nav, sections in `index.html`). Article pages under `magazine/` are PT-only (no toggle), matching their `lang="pt"` with no `data-lang`/`.le`/`.lp` machinery.
- When adding new content to `index.html`, always add both language variants — never leave an English- or Portuguese-only string in a bilingual section, it will show in both language modes.

### Per-page design system (no shared CSS file)

Each HTML file repeats its own `:root` CSS custom properties block (colors, spacing, fonts) rather than importing a shared stylesheet. Key tokens used consistently across pages: `--tiffany` (#00A8A4, brand accent), `--headline` (#003D39), `--ink`, `--muted`, `--faint`, `--border`, `--bg`/`--bg-alt`, `--dark`. Fonts are `Cormorant Garamond` (serif, headings) and `Inter` (sans, body), loaded from Google Fonts. When editing styles, keep changes consistent with these existing tokens rather than introducing new ad hoc colors, and expect to update the same rule in more than one file if the change should apply site-wide (there's no single source of truth for CSS).

### Adding a new magazine article

Adding an article touches **multiple files** — there's no template engine, so each step is manual:
1. Create `magazine/<slug>.html`, copying the structure/CSS of an existing article (see `agente-ia-whatsapp.html`) — includes its own `<script type="application/ld+json">` `Article` schema block (headline, description, dates, canonical URL) and OG/Twitter meta tags.
2. Add a new card at the **top** of `magazine/index.html`'s card list (a comment there marks the insertion point: "NOVO ARTIGO? Cola o bloco do card AQUI").
3. Optionally feature it in `index.html`'s `#blog` section (`.art-card` blocks with expandable `.art-content` via `toggleArticle()`), which shows only the latest ~2 articles.
4. Add a `<url>` entry to `sitemap.xml` (a comment there marks the pattern to copy: "NOVO ARTIGO? Copia um bloco `<url>` destes, muda o loc e a data").

### SEO / structured data

Pages carry JSON-LD (`application/ld+json`) structured data: `index.html` has a `@graph` with `ProfessionalService`, `Person`, and `FAQPage` types; article pages have `Article` type. Keep `canonical`, `og:*`, and `twitter:*` meta tags in sync with the JSON-LD when editing titles/descriptions.

### Newsletter

The newsletter form (`#nl-form` in `index.html` and the equivalent block in article pages) submits to a **MailerLite** hosted form endpoint via `fetch()` with `FormData`, intercepting the default submit to show inline success/error messages (`.nl-msg.ok` / `.nl-msg.err`) instead of navigating away.

### Contact / CTAs

Primary CTA everywhere is a WhatsApp deep link (`https://wa.me/<number>?text=...`) with a pre-filled message — used in the nav, hero, floating `.wa-float` button, and contact section. Keep the phone number and pre-filled message text consistent if changed.
