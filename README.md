# brodsbytes blog

Source for my personal tech blog — homelab, Linux, and self-hosted
infrastructure. Live at https://brodsbytes.github.io/blog/ (custom domain
coming).

Built with [Hugo](https://gohugo.io/) and
[PaperMod](https://github.com/adityatelange/hugo-PaperMod), deployed to
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
