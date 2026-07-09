<!--
DOC MAP: README (public) = what this repo is · docs/WORKFLOW = every post ·
docs/MAINTENANCE = occasional upkeep · docs/TODO = pending one-offs ·
docs/DECISIONS = why things are this way · docs/GOALS = purpose & rules.
THIS FILE: the only public doc. What the site is, how to build/preview it.
NOT here: personal workflow, tooling notes, plans, anything internal —
docs/ is gitignored; keep it that way.
-->

# brodsbytes blog

Source for my personal tech blog — homelab, Linux, and self-hosted
infrastructure. Live at https://brodsbytes.blog 

Built with [Hugo](https://gohugo.io/) and
[PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod), deployed to
GitHub Pages by GitHub Actions on every push to `main`.

## Local preview

```bash
git clone --recurse-submodules https://github.com/brodsbytes/blog.git
hugo server -D        # http://localhost:1313
```

Requires Hugo extended ≥ 0.146.

## Layout

- `content/posts/` — posts (Markdown, front matter per `archetypes/posts.md`)
- `hugo.yaml` — all site configuration
- `themes/PaperMod/` — theme, as a git submodule
- `.github/workflows/hugo.yaml` — build + deploy pipeline
