# duong.page

Source for **[duong.page](https://duong.page/)**, the personal blog of Duong, a backend engineer who likes technology, simplicity and lifelong learning. Posts are about software, developer tools and what I'm building, like [why I built DuoHive](https://duong.page/post/2026-11-13-why-i-built-duohive/).

The site is built with [Hugo](https://gohugo.io/) and a custom theme (`themes/duong`), and deploys to GitHub Pages on every push to `main`.

## Run it locally

Hugo isn't needed on the host; Docker Compose runs it.

```bash
docker compose up -d           # live preview at http://localhost:1313
docker compose run --rm build  # production build into ./public
```

See [AGENTS.md](AGENTS.md) for the writing style, the per-post SEO checklist and how the theme works.
