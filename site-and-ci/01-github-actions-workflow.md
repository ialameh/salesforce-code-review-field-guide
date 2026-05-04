# CI1. GitHub Actions Workflow

This chapter explains the GitHub Actions workflow that deploys this field guide to GitHub Pages. The workflow is in `.github/workflows/docs.yml` at the root of this repository.

The workflow builds the mkdocs site and publishes it to the `gh-pages` branch. GitHub Pages serves the site from that branch.

## How the workflow works

```yaml
name: Docs

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Build docs
        run: |
          mkdir -p docs
          cp *.md docs/ 2>/dev/null || true
          cp -r diagrams cookbook templates case-studies docs/ 2>/dev/null || true
          mkdocs build

      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
          publish_branch: gh-pages
```

**Trigger:** The workflow runs on every push to `main` and can be triggered manually via `workflow_dispatch`.

**Permissions:** `permissions: contents: write` is required because the `peaceiris/actions-gh-pages` action writes the built site files to the `gh-pages` branch.

**Build step:** The `mkdir -p docs` and `cp` commands stage the source files in the `docs/` directory. mkdocs requires `docs_dir: docs` in `mkdocs.yml`, which means the source files must be in a `docs/` directory. The workflow creates that directory at build time.

**Publish step:** The `peaceiris/actions-gh-pages@v3` action pushes the contents of `./site` to the `gh-pages` branch. GitHub Pages is configured to serve that branch.

## GitHub Pages configuration

The repository must have GitHub Pages enabled and pointed to the `gh-pages` branch.

To configure via the GitHub API:

```bash
curl -X PUT https://api.github.com/repos/<owner>/<repo>/pages \
  -H "Authorization: Bearer <token>" \
  -d '{"build_type":"workflow","source":{"branch":"gh-pages","path":"/"}}'
```

The workflow runs first, then you configure Pages. Allow 2 minutes after the first workflow run for the site to become available.

## Running the workflow locally

To test the build locally:

```bash
pip install -r requirements.txt
mkdocs build
```

The output goes to `site/`. Open `site/index.html` in a browser to preview.

To serve locally with live reload:

```bash
mkdocs serve
```

## Troubleshooting

**Site returns 404 after first deploy.** Wait 2 minutes. GitHub Pages takes time to propagate. Also check that the Pages source is set to `gh-pages` branch, not `main`.

**Workflow fails with permissions error.** Ensure `permissions: contents: write` is in the workflow YAML. The default GitHub Actions token does not have write permission to contents without this.

**mkdocs build fails.** Check that all markdown files are in the repository root and that `mkdocs.yml` has `docs_dir: docs`. The build command copies `*.md` to `docs/` before running mkdocs.

## What this chapter covered

- The GitHub Actions workflow structure and what each step does
- GitHub Pages configuration via API
- Local build and serve commands
- Common failure modes and how to fix them

## References

- [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)
- [mkdocs-material](https://squidfunk.github.io/mkdocs-material/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)