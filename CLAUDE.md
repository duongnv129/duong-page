# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Contents

- [Overview](#overview)
- [Writing style](#writing-style) (applies to every post and page)
- [Every post: SEO checklist](#every-post-seo-checklist) (required whenever a post is created or edited)
- [Commands](#commands)
- [Deployment](#deployment)
- [Structure](#structure)
- [SEO and AI discoverability](#seo-and-ai-discoverability) (how the theme implements it)
- [Charts in posts](#charts-in-posts)
- [Menu and tags coupling](#menu-and-tags-coupling)

## Overview

Personal blog for https://duong.page, built with Hugo and a custom theme, `themes/duong`. Content only; there is no application code, no package.json, and no tests.

## Writing style

Apply this to every post and page, new or edited:

- **Sound like a native US English speaker.** Natural, conversational sentences, contractions ("I'm", "you'll"), US spelling ("favorite", "color").
- **Add a bit of fun.** A light joke or a relatable aside now and then so readers enjoy it; never at the cost of clarity, and never forced.
- **Keep it short and easy to read.** Short paragraphs, plain words, skimmable headings and lists. Cut anything that doesn't help the reader. A welcome or about page fits in a 2-minute read.
- **How to describe the author:** Duong, a backend engineer. Not tied to one language (don't say "a Go developer" or "mostly in Go"). Use "Duong" in visible copy; the full name "Duong Nguyen" stays in site metadata (`params.author.name`, structured data).
- **Only real facts.** Don't invent experience, employers, numbers, or projects. If a detail is unknown, leave it out or ask.

## Every post: SEO checklist

**Whenever you create or edit a post in `content/post/`, work through this list before calling it done.** The theme handles the markup; these are the per-post inputs it depends on.

1. **`title`**: specific and searchable, with the main topic near the front; about 50-60 characters (the page title becomes `<title> | Duong Nguyen`).
2. **`description`**: 120-160 characters, a plain summary of what the reader gets. It is reused for the meta description, share card text, JSON-LD and `llms.txt`, so never leave it empty.
3. **`subtitle`**: optional; shown on the share image and under the H1.
4. **`tags`**: include `tech` or `me` so the post appears under a nav menu item (see [Menu and tags coupling](#menu-and-tags-coupling)).
5. **Headings**: the title is the only H1; the body starts at `##` and does not skip levels.
6. **Links**: link to at least one related post or page on this site with descriptive anchor text, and cite external sources with real links.
7. **Images**: every image has meaningful alt text; put images next to the post (page bundle) so they get dimensions.
8. **Charts**: follow every chart group with a table of the same numbers so crawlers, agents and the Markdown copy get the data.
9. **Verify** with `docker compose run --rm build`, then check `public/post/<slug>/index.html` for the `<title>`, meta description and `application/ld+json`, and confirm the post is listed in `public/llms.txt` and `public/sitemap.xml`.
10. **No em-dashes** in visible text.
11. **Style:** the post follows [Writing style](#writing-style).

## Commands

Hugo isn't installed on the host, so use Docker Compose. `docker-compose.yml` pins `hugomods/hugo:exts-0.147.2`, the same version as CI.

```bash
docker compose up -d              # live preview at http://localhost:1313 (drafts on, live reload)
docker compose logs -f hugo       # watch rebuilds and template errors
docker compose restart hugo       # needed after switching `theme:` in config.yaml (new theme dir isn't watched)
docker compose run --rm build     # production build into ./public (CI flags: --gc --minify); safe while the server runs
docker compose down
```

With a native Hugo install, the same commands are:

```bash
hugo server -D                  # local preview with drafts at http://localhost:1313
hugo --gc --minify              # production build into ./public (same flags CI uses)
hugo new post/YYYY-MM-DD-slug.md   # new draft post from themes/duong/archetypes/default.md
```

CI pins **Hugo extended 0.147.2**. Use that version locally to avoid template drift.

## Deployment

`.github/workflows/hugo.yaml` builds and deploys to GitHub Pages on every push to `main`. There is no staging branch, so pushing to `main` publishes the site. CI builds with `--baseURL https://duong.page/`, matching `config.yaml`.

## Structure

- `config.yaml`: site config, including author/social params and the main menu.
- `content/post/`: blog posts named `YYYY-MM-DD-slug.md` with YAML front matter (`title`, `description`, `subtitle`, `date`, `tags`).
- `content/page/about.md`: About page.
- `themes/duong/`: the active theme, written for this site (Hugo 0.146+ template layout: `layouts/home.html`, `single.html`, `list.html` for sections and tag terms, `taxonomy.html` for `/tags/`, partials in `layouts/_partials/`).
  - Styles: `assets/css/main.css` (design tokens as CSS variables at the top) plus `assets/css/chroma.css`, concatenated and, in production, minified and fingerprinted in `_partials/head.html`. No JS build, no npm.
  - `chroma.css` is generated from `hugo gen chromastyles` (github / github-dark) with backgrounds stripped and dark rules scoped to the theme selectors. Regenerate it the same way if you change code-highlight styles; `config.yaml` sets `markup.highlight.noClasses: false`, so highlighting depends on this file.
  - Light/dark follows `prefers-color-scheme`; the header toggle sets `data-theme` on `<html>` and stores it in `localStorage`. Dark tokens are defined twice (media query and `[data-theme="dark"]`) and must be kept in sync.
  - Fonts are self-hosted Geist / Geist Mono woff2 subsets (latin, latin-ext, vietnamese) in `static/fonts/`.
  - Home hero text comes from `content/_index.md`. The portrait and header avatar are resized to WebP from `assets/img/duong.jpg` via Hugo image processing; `static/img/duong.jpg` (`params.logo`) is kept only for `og:image`.
  - Markdown render hooks in `layouts/_markup/`: headings get permalink anchors, fenced code gets a language label and copy button (JS in `_partials/site-script.html`), external links open in a new tab, images lazy-load, tables get a scrolling `.table-wrap`. `render-link.html` must not end with a newline or a space leaks after every link.

## SEO and AI discoverability

- Every post should set `description` (120-160 chars); it feeds the meta description, Open Graph, JSON-LD and `llms.txt` via `_partials/page-description.html`. Fallback order: description, summary, subtitle.
- `_partials/structured-data.html` emits JSON-LD: Person + WebSite (home), BlogPosting + BreadcrumbList (posts), ProfilePage (About).
- `_partials/og-image.html` draws a 1200x630 share card per page at build time (Geist TTF in `assets/fonts/`, canvas PNGs in `assets/img/og-*.png`). Front matter `image` overrides it.
- Output formats (config.yaml): each page also renders `index.md` (Markdown copy, linked via `rel=alternate`), and the home renders `llms.txt`. `robots.txt` (theme `layouts/robots.txt`) allows all crawlers, AI search bots named explicitly, and points to the sitemap.
- RSS (`layouts/home.rss.xml`) lists posts only, with full content.
- `lastmod` comes from git (`enableGitInfo`), so CI must keep `fetch-depth: 0`. The workflow pins `--baseURL https://duong.page/` because the Pages-provided base URL is `http://`.
- `content/page/_index.md` stops the `/page/` section list from rendering; the 404 page is `noindex`.

## Charts in posts

Comparison charts are build-time shortcodes (no JS): wrap `bar-chart` blocks in `charts` for a wide 3-column grid with one legend. Declare series once in front matter so each entity keeps its color across charts; `slot` is 1-3 (blue, orange, teal from the dataviz reference palette, validated in both modes; only 3 slots pass all-pairs), `texture: true` hatches a variant of the same entity.

```markdown
chart:
  series:
    - { name: "Bubble Tea", slot: 1 }
    - { name: "Ink tuned", slot: 3, texture: true }

{{</* charts */>}}
{{</* bar-chart title="Startup" unit="ms, lower is better" suffix=" ms" better="lower" note="Median of 10 runs." */>}}
Bubble Tea = 13.3 | optional tooltip detail
{{</* /bar-chart */>}}
{{</* /charts */>}}
```

Every bar shows its value (teal is under 3:1 on the light background), the best value gets a check mark, and a table of the same numbers should follow the charts. New files under `layouts/` (e.g. `_shortcodes/`) need `docker compose restart hugo` before the dev server sees them.

## Menu and tags coupling

The nav menu points to tag listing pages, not sections. **Blog** → `tags/me` and **Technology** → `tags/tech`. A post only appears under a menu item if its `tags` include `me` or `tech`.

The logo and favicon set in `config.yaml` (`img/duong.jpg`, `img/duong.ico`) live in `themes/duong/static/img/`.
