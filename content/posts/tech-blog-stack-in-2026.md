---
title: "Tech Blog Stack in 2026"
date: 2026-07-07T17:01:32+10:00
draft: false
tags: [blog,tech,stack,hugo,papermod,github]
summary: "The stack I used to setup this tech blog in one afternoon"
---

## The stack at a glance
- Static site generator: [Hugo](https://gohugo.io/) (extended)
- Theme: [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
- Host: GitHub Pages, live at `brodsbytes.github.io/blog/` (optionally buy your own domain, i.e. `brodsbytes.blog`)
- CI/CD: GitHub Actions, build + deploy on every git push 

## How does it work?
**Hugo** is a tool that turns Markdown files (.md) into a folder of HTML/CSS/JS files. This is a static state instead of a live web server or CMS rendering and serving the content (e.g. WordPress). Then, in our case, we add **PaperMod** on top as an optional theme for a better look and additional personalisation options. **GitHub Actions** is the automation in the middle - everytime we push changes to the GitHub repo, it automatically runs Hugo to Build the site again and hands over the output folder `./public` to GitHub Pages. **GitHub Pages** is the free web hosting that is serving those outputted files. Finally, adding your own **Custom Domain** on top, is essentially just pointing a friendlier name to where the files are on GitHub Pages.

Note, this exact stack isn't required. You don't need to use GitHub Pages for hosting or GitHub Actions for CI/CD, this is just the easiest and cheapest option in my scenario. There's also no lock in since it's easy to change hosting later - run Hugo on a new host, point your domain to the new site.

## Why this stack

For a basic tech blog, this makes sense. First, it's free to run - GitHub Pages hosting + GitHub Actions cost $0. The only optional spend is a domain which you can do after a test run. It's also simple, content is just .md files in git; the whole site is a folder of static files. Hugo takes your Markdown, outputs it into plain HTML/CSS/JS. No server, no database, no CMS. Iny opinion, the best benefit of this is security. There's nothing to patch with this approach - no server-side code, no plugins, no admin login to protect. Having ran a WordPress site previously, these were all things I needed to mitigate with hardening the admin login, automatic plugin updates and even additional security contorls. Less to tinker with, but also much more peace of mind. I'd recommend grabbing a domain - `brodsbytes.blog` costs $20 a year and helps with SEO rankings. Having all the files of the site yourself makes it easy, no webhosting / vendor specific migrations when it comes time to change hosting.

### Privacy by default

Another big plus for me - privacy. No Google AdSense, ad plugins or no third-party ad/tracking scripts. No visitor to my site will ever have advertising tracking as a result for visiting my site. I will setup analytics with Cloudflare as that's where my domain is bought from. However this is cookieless - no cross-site tracking and even better, no cookie consent banner needed!

Honourable mention goes to GoatCounter as an open-source, privacy-friendly fallback option. Cloudflare was just the easiest for me and saved me having another account.

## Examples of other live sites built on Hugo

Ok cool so the tech checks out, but I've never heard of this, are people actually using this? Or is it only used by a few nerds running blogs?

I'm glad you asked! The stack is pretty versatile for most websites - it's not limited to blogs only. Here are some different examples:

- [TestingWithMarie](https://www.testingwithmarie.com/posts/20241126-create-a-static-blog-with-hugo/) - Actual installation guide I used as a reference, alongside seeing another solo indie style bloger has their site setup
- [Let's Encrypt](https://letsencrypt.org) - The non-profit certificate authority that issues free TLS certificate, which could be credited to running much of the internets HTTPS (the S stands for Secure!). Their entire site source is also available on their [GitHub](https://github.com/letsencrypt/website), including their Docs. Very solid example of a highly credible and heavily used public site running Hugo.
- [kubernetes](https://kubernetes.io/) - The industry standard, open source container orchestration platform for automating deployment, scaling, and management of containerized applications. More proof Hugo is scalable.


## The technical bit (set up in an afternoon)

For the installation, I'm not going to go into too much detail here. The documentation for both [Hugo](https://gohugo.io/installation/) and [PaperMod](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation#installingupdating-papermod) are excellent. I just wanted to give the high level steps on what's involed, to show you it can be done in an afternoon.

1. Install Hugo extended (extended needed for the PaperMod theme)
2. Add PaperMod (I did as a git submodule, the recommended method)
3. Push repo to GitHub, enable Pages, set the build source to GitHub Actions
4. Create Workflow file `.github/workflows/hugo.yaml` from Hugo's own official **GitHub Pages** template
5. End result: git push to `main` > GitHub Actions builds > deploys to GitHub Pages.

## Caveat with GitHub Pages & public repos

Free GitHub Pages requires a **public** repo, this means whatever you commit is world-readable. I work directly out of the repo (single source of truth) instead of a separate "clean" publish directory. This raises a potential issue for publicly broadcasting my personal files by accident. `.gitignore` and a git pre-push hook mitigates most of the user error risk. My .gitignore prevents my drafts and internal notes pushing, which works alongside my pre-push script that checls only required files for the site are being pushed, and will error before pushing. This needs maintenance if you scale the site later, but im happy with the trade off in my case.

Why not make 2 directories? I dont wanan deal with copy/sync step, no "which copy is current?", version control, etc. Also my drafts + research + published posts are all in one place which makes navigation easier when writing. 


#### .gitignore snippet: ####
```
# Internal notes — local only, never published
docs/

# Work-in-progress posts — local only until moved up to content/posts/
content/posts/drafts/

# Hugo build output
public/
resources/_gen/
.hugo_build.lock
```

#### git pre-push hook snippet: ####
```
#!/usr/bin/env bash
# Pre-push allowlist guard: blocks pushing any file outside the paths a
# public Hugo blog repo strictly needs. Master copy lives in docs/hooks/
# (docs/ is gitignored); install by copying to .git/hooks/pre-push (+x).
# Bypass deliberately (only if you're sure): git push --no-verify
set -euo pipefail

ALLOW_REGEX='^(content/|archetypes/|layouts/|static/|assets/|data/|i18n/|\.github/|themes/PaperMod$|hugo\.yaml$|\.gitignore$|\.gitmodules$|README\.md$)'

ZERO=0000000000000000000000000000000000000000
violations=""

while read -r _local_ref local_sha _remote_ref remote_sha; do
  [ "$local_sha" = "$ZERO" ] && continue   # branch deletion — nothing to check
  if [ "$remote_sha" = "$ZERO" ]; then
    # New branch: remote has nothing, so audit every file in the tree
    files=$(git ls-tree -r --name-only "$local_sha")
  else
    files=$(git diff --name-only "$remote_sha" "$local_sha")
  fi
  bad=$(echo "$files" | grep -Ev "$ALLOW_REGEX" || true)
  [ -n "$bad" ] && violations="$violations$bad"$'\n'
done

if [ -n "${violations%$'\n'}" ]; then
  echo "PUSH BLOCKED — files outside the public allowlist:" >&2
  echo "$violations" | sed '/^$/d; s/^/  /' >&2
  echo "If intentional, update ALLOW_REGEX in .git/hooks/pre-push" >&2
  echo "(master copy: docs/hooks/pre-push), or bypass with --no-verify." >&2
  exit 1
fi
```


## My actual writing workflow (so far)

Ok cool, what's it like actually writing blog posts? Here's my rough workflow - which i've just used for the first time on this first blog post.

1. Run `hugo new posts/drafts/my-post-title.md` to create new post from template in ./archetypes/. Adds front matter (draft: true, empty tags/summary) stamped automatically
*(Filename becomes the URL slug, try to aim for descriptive and searchable. e.g. `nas-backups-with-borg.md`, not `backup-post.md`)*
2. I use VSCode when writing in the .md file: split screen editor window with one in Preview mode to see how it's being formatted.
3. Final formatting and structure check in browser, by running the Hugo server locally `hugo server -D`. This updates previewing after saving the document - VSCode is instant, which is why i'm using that.
4. Fill `summary:` and `tags:` for SEO at the top of the .md file, set `draft: false`
5. Publish: move the file out of the gitignored drafts folder into ./posts/, then commit + push.
6. GitHub Actions redeploys the new site in ~1 minute.
