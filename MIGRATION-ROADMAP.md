# MieleLab WordPress → Jekyll Migration Roadmap

Draft as of 2026-08-17. Target: static Jekyll site on GitHub Pages, replicating the
look, feel, and accessibility of https://mielelab.com as closely as possible.

## Status of source material

| Source | Status |
|---|---|
| Content export (`mielelab.WordPress.2026-08-17.xml`) | Have it — 10 pages, 3 posts, 10 projects, 13 events, 7 blurbs |
| Media (`wp-content/uploads/`) | Downloaded directly (public URLs) — see `assets/uploads/` |
| Fonts (self-hosted Montserrat) | Downloaded directly — see `assets/fonts/` |
| Theme CSS (parent Twenty Twenty-Four + child theme) | Downloaded directly — see `assets/theme/` |
| Theme PHP/templates, `functions.php` | **Obtained 2026-08-18** via full BlogVault site backup (`mielelab.com full backup 08-18-26.zip`) — includes the real child theme CSS, `miele-projects`/`miele-events`/`miele-blurbs` CPT plugins, and a full `bvfulldump.sql` database dump (real ACF field values, Gravity Forms config, `wp_global_styles`). Used to audit and correct the migration — see parity audit notes below. |
| Gravity Forms config | Real field config recovered from `wp_gf_form_meta` in the backup dump; form is still rebuilt against Formspree, but field labels/options/button text now match the source exactly |

## Phase 1 — Jekyll scaffold

- Set up base Jekyll structure (`_config.yml`, `_layouts/`, `_includes/`, `_sass/`, `assets/`)
- Port `DESIGN-TOKENS.md` palette/type scale into `_sass` variables and a base stylesheet
- Set up `@font-face` for the 4 self-hosted Montserrat weights
- Build base layout: header/nav, footer, focus-visible states matching the child theme's
  accessibility-forward CSS (visible 2px focus outlines, hover/focus color swaps)

## Phase 2 — Content migration

- Convert the 10 WP pages to Jekyll pages (Home, About Josh Miele, Projects & Collaborations,
  Contact, Connecting Dots - A Blind Life, The Basics, Blog index, Privacy Policy,
  Accessibility Statement). Skip "Nonsense" (private/test page) unless Josh wants it kept.
- Convert 3 posts to `_posts/` (skip the private "Zibby and Connecting Dots" unless Josh wants
  it public)
- Model the 3 custom post types as Jekyll collections:
  - `_projects/` (10 entries — CAOS, Blind Arduino Project, UW CREATE, etc.)
  - `_events/` (13 entries — book tour stops, CSUN, Moth Mainstage, etc.)
  - `_blurbs/` (7 entries — book endorsements)
- Rebuild the Home page's dynamic sections (latest blog posts, upcoming events query) as
  Liquid templates over the new collections

## Phase 3 — Contact form → Formspree

- Recreate the field set observed on the live form: Name (First/Last), Email, "What are you
  reaching out about?" (dropdown — need Josh to confirm the exact options, they weren't fully
  visible in a quick scrape), Subject, Message
- Match the dark-navy/cyan field styling and yellow focus states from `DESIGN-TOKENS.md`
- Wire submit action to a Formspree endpoint (Josh to create the Formspree form and share the
  endpoint ID)

## Phase 4 — Accessibility pass

- Re-verify every text/background color pairing against WCAG AA with an actual contrast
  checker (not just the sampled pairs in `DESIGN-TOKENS.md`)
- Confirm focus-visible styling survives the port (this is a real, deliberate accessibility
  feature on the current site — not incidental)
- Full keyboard-navigation pass and screen-reader spot check once pages are live
- Check heading hierarchy on every page (don't skip levels)

## Phase 5 — QA / cutover

- Side-by-side visual comparison against live site (desktop + mobile breakpoints)
- Verify all internal links, external links (Amazon/Bookshop/etc. book-buy links), and social
  icons
- Confirm redirects/URL structure so old WordPress URLs don't 404 after cutover (WordPress
  pretty permalinks vs. Jekyll's default — may need a redirect map)
- DNS/hosting cutover to GitHub Pages once approved

## Open questions / what would help most from here

1. **Mobile nav behavior** — haven't sampled the live site at mobile widths yet; if you can
   describe how the nav collapses (hamburger menu, etc.) or I'll check it myself next session
2. ~~"What are you reaching out about?" dropdown options~~ — **resolved 2026-08-18**: recovered
   the real Gravity Forms field config from the full backup; `contact.html` now matches exactly
   (including the "Send Message" button text).
3. **URL/permalink structure** — do you want to preserve exact URLs? Confirmed gap: the real
   WordPress Projects page is `/projects-collaborations/`, but Jekyll uses `/projects/`. Nav
   label/order match; only the URL differs. Worth a redirect if anything external links to the
   old slug.
4. **The private pages/posts** ("Nonsense", "Zibby and Connecting Dots") — keep them
   private/omitted, or should they become public content on the new site?

## Parity audit (2026-08-18)

Ran a full audit against the real WordPress backup (theme CSS, CPT plugin source, and a full
database dump). Full findings in the audit report; fixes already applied to this repo:

- Fixed project/blurb manual ordering (`_projects/connecting-dots-a-blind-life-with-wendell-jamieson.md`
  and the five blurb files under `_blurbs/`) to match the real `menu_order` values.
- Added `/events/` — the live site has an events archive (`has_archive: true` on the `events`
  CPT); two past non-book events (CSUN Conference, Bill Gerrey talk) had no page they rendered
  on before this.
- Ported the real 4-color alternating card treatment (`.project-list`, `.event-list`,
  `.blurb-list` in `_sass/_components.scss`) and the 3-col/2-col/1-col responsive grid, pulled
  from the real child theme CSS — this was the largest visual gap found.
- Fixed the home hero's mobile crop/overlap behavior (`.home-hero` in `_sass/_components.scss`
  + `index.html`).
- Corrected `$content-max-width` (1200px → 1280px) and `$radius-button` (10px → 50px pill) in
  `_sass/_tokens.scss` against the real DB-stored global styles and child theme CSS.
- Color palette, fonts, all project/event/blurb content, nav structure, and uploaded images were
  independently re-verified against the DB dump and confirmed already correct.
