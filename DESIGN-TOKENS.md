# MieleLab Design Tokens (reverse-engineered from live site, 2026-08-17)

Extracted via computed-style inspection of https://mielelab.com (theme: WordPress core
**Twenty Twenty-Four** + custom child theme **twenty-twenty-four-child**). Source CSS
saved at `assets/theme/twentytwentyfour-style.css` (parent) and
`assets/theme/twentytwentyfour-child-style.css` (child, has the real custom rules).

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

## Known gaps (still need from Josh, or to reverse-engineer further)

- Parent theme's full block templates/PHP (Twenty Twenty-Four is open source — can pull the
  reference version straight from wordpress.org if useful, since the site doesn't appear to
  override the base template structure heavily)
- Any images not linked from the crawled pages (only checked Home + Contact so far)
- Favicon / site icon — not spotted in the requests sampled, needs a dedicated check
- Mobile breakpoint behavior (nav collapse, etc.) — not yet sampled at mobile viewport widths
