# MieleLab (mielelab.com)

Jekyll rebuild of the MieleLab site, migrated from WordPress. Deploys to GitHub Pages.

## Local development

```
bundle install
bundle exec jekyll serve
```

Open http://localhost:4000.

## Site structure

- `_layouts/` — page shells: `default` (site chrome), `page` (title + content), `post` (blog posts), `project` (project detail pages)
- `_includes/` — reusable snippets: `header.html`, `footer.html`, `social-icons.html`, `book-cta.html`
- `_sass/` — stylesheet source, compiled by `assets/css/main.scss`:
  - `_tokens.scss` — colors, type scale, spacing (the design system in one place)
  - `_base.scss` — global element styles, focus states, fonts
  - `_header-footer.scss`, `_components.scss` — component-level styles
- `_data/social.yml` — social media links (footer + Contact page pull from here)
- `_data/book_links.yml` — book retailer links (Connecting Dots page pulls from here)
- `assets/uploads/` — images migrated from WordPress
- `assets/fonts/` — self-hosted Montserrat font files

### Content collections

- `_projects/` — Projects & Collaborations entries. Each has its own page at `/projects/<slug>/`.
- `_events/` — speaking events (data only, no individual pages — listed on Home, Connecting Dots, and the `/events/` archive). Front matter: `date`, `time`, `timezone`, `location`, `event_type`, `link`.
- `_blurbs/` — book endorsement quotes (data only). Front matter: `background`, `order`. Body is the quote text.
- `_posts/` — blog posts, standard Jekyll format (`YYYY-MM-DD-title.md`).

## Adding content

**New project:** add a file to `_projects/`, e.g. `_projects/my-project.md`:

```yaml
---
title: "My Project"
order: 11
external_link: "https://example.com"
image: "/assets/uploads/my-project.png"
---
Description text here.
```

**New event:** add a file to `_events/`. Events on the Connecting Dots page are filtered to `event_type: "Book Related Event"`; the Home page "Upcoming Events" section shows any event with a future `date`.

**New blog post:** add a file to `_posts/` named `YYYY-MM-DD-title.md` with `title`, `date`, and optionally `image` in front matter.

**Updating social or book links:** edit `_data/social.yml` or `_data/book_links.yml` directly — every page that shows these links pulls from the same file.

## Accessibility

This site follows WCAG 2.2 AA practices established during the migration:

- Semantic landmarks (`header`, `nav`, `main`, `footer`), one `<h1>` per page, skip link to `#main-content`
- Visible focus indicators on every interactive element (`_sass/_base.scss`), including a dual-color ring so focus is visible against both light and dark backgrounds
- All meaningful images carry descriptive `alt` text; purely decorative images use `alt=""`
- `prefers-reduced-motion` is respected (disables smooth scrolling and transitions)
- Form fields all have associated `<label>` elements

When adding new content or components, keep these conventions:

- Every `<img>` needs `alt` text describing what it shows, or `alt=""` if it's purely decorative
- Don't skip heading levels
- New interactive elements must be keyboard-reachable and show a visible focus state (inherited automatically if you reuse existing button/link classes)
- Avoid inline `style=""` attributes — add a class in `_sass/_components.scss` instead, so styling stays in one place

### Testing

```
bundle exec htmlproofer ./_site --checks Images,Links --disable-external --swap-urls "^/mielelab-migration:"
```

Run this after `jekyll build` to catch missing alt text and broken internal links before deploying. Add `--allow-hash-href` if it flags the skip link.

## Redirects from old WordPress URLs

`jekyll-redirect-from` (bundled with the `github-pages` gem) generates meta-refresh
stub pages for every old WordPress permalink that doesn't match its new Jekyll URL,
via `redirect_from:` front matter on the target page. Current redirect sources, pulled
from the real WordPress export (`mielelab.WordPress.2026-08-17.xml`):

| Old WordPress URL(s) | New URL | Where |
|---|---|---|
| `/projects-collaborations/` | `/projects/` | `projects.html` |
| `/home-page/` | `/the-basics/` | `the-basics.html` |
| `/project/<slug>/` (×10) | `/projects/<slug>/` | each `_projects/*.md` |
| `/YYYY/MM/DD/<slug>/` (×2) | `/blog/<slug>/` | each `_posts/*.md` |
| `/events/<slug>/` (×13) | `/events/` | `events.html` — WordPress had an individual page per event; this site only lists events, so all 13 old event URLs collapse onto the archive rather than a 1:1 page |

`/`, `/about/`, `/contact/`, `/connecting-dots/`, `/blog/`, `/privacy-policy/`, and
`/accessibility-statement/` already matched the old WordPress slugs exactly and need
no redirect.

Adding a new redirect: add a `redirect_from:` array (one or more old paths) to the
front matter of whichever page now serves that content.

## Deployment

Push to the branch GitHub Pages is configured to build from. No build step is needed beyond what GitHub Pages runs automatically (it uses the gems pinned in `Gemfile`).
