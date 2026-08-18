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
| Theme PHP/templates, `functions.php` | **Not obtainable** — server-executed, not publicly downloadable, and admin/host access hasn't panned out. Working from rendered HTML + CSS instead (see `DESIGN-TOKENS.md`) |
| Gravity Forms config | N/A — form is being rebuilt from scratch against Formspree |

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
2. **"What are you reaching out about?" dropdown options** on the Contact form — need the exact
   list of choices to replicate in Formspree
3. **URL/permalink structure** — do you want to preserve exact URLs (e.g. `/about-josh-miele/`)
   for SEO/link continuity, or is a fresh URL structure fine?
4. **The private pages/posts** ("Nonsense", "Zibby and Connecting Dots") — keep them
   private/omitted, or should they become public content on the new site?
5. If you do manage to get theme/plugin file access later (Duplicator export, host file
   manager, etc.), it's still useful as a cross-check even though we're proceeding without it
   now — no urgency, just send it over if it becomes easy to get
