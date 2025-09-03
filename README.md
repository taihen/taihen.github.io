# taihen.org

This is the raw content of [taihen.org](https://taihen.org)

Site is generated using [Hugo](https://gohugo.io/) and GitHub Actions.

## Development

**Local development:**

```bash
hugo server -D    # Run with drafts
hugo server       # Run published content only
```

**Content creation:**

```bash
hugo new posts/post-name.md    # Create new blog post
```

**Building:**

```bash
hugo --minify     # Build production site
```

Site automatically deploys via GitHub Actions on push to main branch.
