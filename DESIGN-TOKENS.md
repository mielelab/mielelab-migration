# MieleLab Design Tokens (reverse-engineered from live site, 2026-08-17)

Extracted via computed-style inspection of https://mielelab.com (theme: WordPress core
**Twenty Twenty-Four** + custom child theme **twenty-twenty-four-child**). Source CSS
saved at `assets/theme/twentytwentyfour-style.css` (parent) and
`assets/theme/twentytwentyfour-child-style.css` (child, has the real custom rules).

> **Status: historical.** This document is the original reverse-engineering pass, done
> by sampling computed styles from the rendered live site before a full WordPress
> backup was available. On 2026-08-18 a full site backup (WordPress core, plugins,
> themes, and a database dump) was obtained, which is authoritative where it
> disagrees with this doc. **See "Corrections from the full backup audit (2026-08-18)"
> below** for exactly what changed and why. Everything else in this document that
> isn't listed there was independently re-verified against the backup and confirmed
> correct — it's kept as-is rather than rewritten, so this remains a record of what
> was knowable from the live site alone versus what needed the real source to get
> right. Full audit findings (content parity, not just design tokens) are logged in
> `MIGRATION-ROADMAP.md`.

## Corrections from the full backup audit (2026-08-18)

Two values in this document, sampled from rendered output, turned out to be wrong
once the real source (child theme CSS + DB-stored global styles) was available:

| Token | This doc said | Real value | Source | Fixed in |
|---|---|---|---|---|
| Content max width | not documented here (Jekyll build had guessed `1200px`) | `1280px` | `theme.json` `settings.layout.wideSize` and the DB-stored `wp_global_styles` override for the child theme both agree | `_sass/_tokens.scss` `$content-max-width` |
| Contact form submit button radius | `10px` (line 50 below) | `50px` (full pill) | child theme CSS: `.gform-theme--framework input.gform_button.button { border-radius: 50px !important; }` | `_sass/_tokens.scss` `$radius-button` |

Everything else below — the color palette, font family/weights, input border-radius
(`3px`), and the contrast/accessibility notes — was cross-checked against the real
DB-stored styles and confirmed accurate as originally recorded.

## Color palette

| Token | Hex | Used for |
|---|---|---|
| `--custom-navy-blue` / `--contrast` | `#1c2157` | Primary text, headings, dark backgrounds (form fields, submit button) |
| `--custom-cornflower-blue` / `--contrast-2` | `#0079b5` | Primary button background (e.g. "Contact Josh Miele") |
| `--custom-cyan-blue` / `--contrast-3` | `#5cc9de` | Form labels/input text on dark backgrounds, accents |
| `--custom-yellow` / `--accent` | `#f7d13b` | Hover/focus states (button hover, focus outlines) |
| `--custom-baby-blue` / `--base` | `#ebf5fc` | Light section backgrounds |
| `--base-2` | `#ffffff` | Page background, white text on dark |
| `--custom-text` | `#2e2e2e` | Fallback body text |

Full WP preset palette (includes unused Gutenberg defaults) is in `assets/theme/` CSS files.

## Typography

- **Font family:** Montserrat (self-hosted, not Google Fonts CDN) — files in `assets/fonts/`:
  weights 400, 500, 700, 800, all `normal` style. `@font-face` needs to be declared in the
  Jekyll build pointing at these local files.
- **Base font size:** 16px (`--wp--preset--font-size--normal`)
- **Fluid/clamp sizes** (responsive — scale with viewport width):
  - small: `0.9rem`
  - medium: `1.05rem`
  - large: `clamp(1.39rem, 1.39rem + ((1vw - 0.2rem) * 0.767), 1.85rem)`
  - x-large: `clamp(1.85rem, 1.85rem + ((1vw - 0.2rem) * 1.083), 2.5rem)`
  - xx-large: `clamp(2.5rem, 2.5rem + ((1vw - 0.2rem) * 1.283), 3.27rem)`
  - huge: `42px`

### Observed rendered sizes (homepage, desktop viewport)

| Element | Font size | Weight | Color |
|---|---|---|---|
| H1 (site title) | ~29.6px | 600 | `#1c2157` |
| H2 (section heading) | ~40px | 700 | `#1c2157` |
| H3 (on dark bg, e.g. "Projects and Collaborations") | 24px | 700 | `#ffffff` |
| Body paragraph | ~16px | 400 | `#1c2157` |
| Nav link | ~16px | 400 | `#1c2157` |
| Primary button (`.wp-block-button__link`) | 14.4px | 500 | white text on `#0079b5` bg |

## Contact form field styling (Gravity Forms → will be rebuilt for Formspree)

- Input background: `#1c2157` (dark navy), text/label color: `#5cc9de` (cyan), border-radius `3px`
- Submit button: `#1c2157` bg, white text, `10px` border-radius, `16px` font
- Focus/hover state (from child theme CSS): outline `2px solid #f7d13b`, background flips to
  `#f7d13b` with dark text on hover/focus for buttons and links
- Fields present on the live form: Name (First/Last), Email (required), "What are you reaching
  out about?" (required — appears to be a select/dropdown), Subject (required), Message (required)

## Accessibility notes (contrast, checked against WCAG 2.1 AA)

- Body text `#1c2157` on white `#ffffff`: contrast ratio ≈ **14.9:1** — passes AAA
- Primary button white text on `#0079b5`: contrast ratio ≈ **4.8:1** — passes AA for normal text
  (barely — keep this exact color pairing, don't lighten the blue)
- Visible focus outlines are used site-wide (`outline: 2px solid` on focus, not just default
  browser outline removed) — **must preserve this in the Jekyll rebuild**, it's a real
  accessibility feature already in place, not incidental styling
- When porting, re-verify every text/background pairing with a contrast checker rather than
  eyeballing — this list covers the elements sampled, not the full page inventory

## Assets downloaded (public, no login required)

- `assets/uploads/` — all 36 media files referenced in the WXR export (images, SVG icons)
- `assets/fonts/` — 4 Montserrat `.woff2` files (400/500/700/800)
- `assets/theme/twentytwentyfour-style.css` — parent theme stylesheet
- `assets/theme/twentytwentyfour-child-style.css` — child theme custom CSS (the real
  customizations: button/focus states, header behavior, blog/archive layout, form styling)

## Known gaps (as of 2026-08-17 — see resolution status below)

- Parent theme's full block templates/PHP (Twenty Twenty-Four is open source — can pull the
  reference version straight from wordpress.org if useful, since the site doesn't appear to
  override the base template structure heavily)
- Any images not linked from the crawled pages (only checked Home + Contact so far)
- Favicon / site icon — not spotted in the requests sampled, needs a dedicated check
- Mobile breakpoint behavior (nav collapse, etc.) — not yet sampled at mobile viewport widths

### Resolution status (2026-08-18, from the full backup audit)

- **Theme PHP/templates** — obtained. The child theme has no template overrides at all
  (structure lives in Gutenberg block markup in the DB, not PHP); its CSS and the
  `miele-projects`/`miele-events`/`miele-blurbs` plugin source are now in the backup.
- **Linked images** — diffed the full `wp-content/uploads/` listing against
  `assets/uploads/`; every real content image is accounted for.
- **Favicon / site icon** — confirmed there wasn't one. `wp_options.site_icon = '0'` in the
  DB dump; this gap can be closed as "nothing to port," not "still needs checking."
- **Mobile breakpoint behavior** — now fully known from the real child theme CSS (768px and
  1200px breakpoints throughout, including project/event/blurb grid column counts and the
  home hero's mobile crop/overlap treatment) and ported into `_sass/_components.scss`.
