# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this is

Daniel Martinez Amigo's personal portfolio & blog website — a **Jekyll** static site
deployed to **GitHub Pages** at `danimtz.github.io`. Built on the
[al-folio](https://github.com/alshedivat/al-folio) theme. Daniel is a Computer Systems
Engineering graduate (University of Bath) focused on computer graphics, rendering, and
game development; the site showcases his projects and CV.

## Build & run

```bash
bundle install          # first-time setup (Ruby/Jekyll deps in Gemfile)
bundle exec jekyll serve # local dev server with live reload (http://localhost:4000)
bundle exec jekyll build # output to _site/
```

- `_site/` is generated output — **never edit it by hand**; it's overwritten on build.
- `.jekyll-cache/` and `.tweet-cache/` are caches, not source.
- Deployment is automatic via GitHub Pages on push to `master`.

## Repo layout

Jekyll `_`-prefixed directories are content collections / theme internals:

| Path | Purpose |
|------|---------|
| `_config.yml` | Site config: collections, plugins, theme options, social links |
| `_pages/about.md` | **Homepage** (`permalink: /`) — bio + profile; uses `about` layout |
| `_pages/projects.md` | Portfolio grid at `/portfolio/`; renders `_portfolio` cards by category |
| `_portfolio/` | Project cards. Categories: `unreal engine`, `graphics`, `university`. Ordered by `importance` |
| `_devpost/` | Long-form technical dev-blog articles about the **Density UE5** project. Rendered as cards on the Density portfolio page |
| `_shaders/` | Shadertoy shader collection |
| `_posts/` | al-folio demo posts, **intentionally kept as syntax/formatting reference** (not real content, not for deletion) |
| `_news/` | al-folio demo announcements, **kept as reference**; news is commented out in `_layouts/about.html` |
| `_layouts/` | Page templates: `about`, `page`, `post`, `distill`, `default`, `none`, `bib` |
| `_includes/` | Reusable Liquid partials: `figure.html`, `video.html`, `devpost.html`, `projects.html`, social icons, head/header/footer |
| `_sass/` + `assets/css/` | Styling (SCSS) |
| `assets/` | `img/`, `video/`, `pdf/` (CV), `js/` — media and scripts |
| `UnusedProjectTemplates/` | Parked templates, not part of the built site |

## Content authoring conventions

- Content files are **Markdown with YAML front matter**. Copy front matter from an
  existing sibling file when adding a new one.
- **New project** → add a `_portfolio/<name>.markdown` with `layout: page`, `title`,
  `description`, `img`, `importance`, `category`. Add a `redirect:` field to link the
  card straight to an external URL (e.g. a GitHub repo) instead of a detail page.
- **New Density dev article** → add a `_devpost/<name>.md` with `layout: distill`,
  `category: density`, and an `importance` (controls card order). It auto-appears on the
  Density page via the loop in `_portfolio/density.markdown`.
- Use the `figure.html` and `video.html` includes for media (they add zoom, rounding,
  responsive sizing) rather than raw `<img>`/`<video>` tags. Distill articles use
  `l-body` / `l-page` / `l-page-outset` wrapper divs for width.
- Images live in `assets/img/`; devpost article images under `assets/img/<Article>Devpost/`
  or `assets/img/devpost_covers/`.

## The Density UE5 project (current focus)

`_portfolio/density.markdown` is the hub for Daniel's Destiny 2 recreation in Unreal
Engine 5. It embeds YouTube demos, then loops over the `_devpost` collection to show a
grid of technical write-ups (Mantle system, Damage pipeline, Health bar, Weapon HUD,
Vortex grenade). Recent commits are additions to this collection.

## Notes / cleanup opportunities

These are leftover al-folio placeholders, not intentional content — flag before relying on them:
- `_config.yml` `scholar:` block still says "Einstein"; `blog_description` is the theme default.
- Email differs across the site (`danimtz@outlook.com` in config; `dmtz@hotmail.es` elsewhere).
- Analytics keys in config are placeholders (`UA-XXXXXXXXX`) and disabled.

**Do NOT treat these as cleanup** — they are deliberate:
- `_posts/` and `_news/` demo content is kept **on purpose** as a Markdown/Liquid formatting
  reference for the owner. Do not suggest removing them.
