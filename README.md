# setup-zine
GitHub Action to get the [Zine static site generator](https://zine-ssg.io).


# Usage

In your GitHub Actions workflow file add a step like this:

```yaml
- name: Setup Zine
  uses: kristoff-it/setup-zine@v1
  with:
    version: v0.10.0
```

The `version` field is mandatory and must be `v0.10.0` or higher (previous
versions of Zine have no artifacts associated).

The action will make `zine` available in PATH, after that it's up to you to run `zine release`.

If you are using Zine in conjunction with Zig (i.e. you integrate Zine in your
`build.zig`) then using  this action will allow you to avoid building Zine from
source by setting `.zine = .system` in `build.zig`.

That said, be aware that `mlugg/setup-zig` will save your Zig cache dir and so
building Zine will also be cached. The upside of using this action anyway is
that it will save you from "slow startups" when your cache expires.

# Full GitHub Pages workflow example.

```yaml
name: Deploy the website to Github Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Change if you need git info

      - name: Setup Zine
        uses: kristoff-it/setup-zine@v1
        with:
          version: v0.10.0-preview

      - name: Release
        run: zine release

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: 'public'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

This last example workflow will only work if you have setup GitHub Pages to deploy from GitHub Actions in your repo's settings section.
