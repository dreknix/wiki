# AGENTS.md

Personal wiki built with [Material for MkDocs](
https://squidfunk.github.io/mkdocs-material/). All content is Markdown under `docs/`.

## Dev commands

```bash
# Setup: direnv creates a Python venv automatically (.envrc: `layout python3`)
task update        # install/upgrade all Python deps into the venv
task serve         # mkdocs serve --livereload (local preview)
task lint          # yamllint . (YAML only, no markdown linter in tasks)
task offline       # build offline site to site_offline/
```

There are no tests. Verification is `task serve` and checking the output visually.

## Environment

- `direnv` + `.envrc` loads a Python virtualenv and sources `.env` (gitignored)
- `.env` must contain `MKDOCS_GIT_COMMITTERS_APIKEY` (GitHub PAT) for the git-committers plugin — only used in CI and optional locally
- Several plugins are CI-only (`enabled: !ENV [CI, false]`): `file-filter`, `git-committers`, `git-revision-date-localized`, `privacy`, `rss`, `tags`

## Content structure

- `docs/` — wiki pages organized by topic (unix/, tools/, writing/, hardware/, ha/, macos/, windows/)
- `docs/blog/posts/` — blog entries
- `snippets/` — files included via `pymdownx.snippets` (base_path is `snippets/`)
- Navigation is controlled by `.pages` files (awesome-pages plugin), **not** by `mkdocs.yml` nav. Most `.pages` files use `- ...` for auto-discovery.

## Conventions an agent would miss

### Markdown style
- External links use `{:target="_blank"}` attribute suffix: `[text](url){:target="_blank"}`
- `use_directory_urls: false` — internal links point to `.md` files directly (e.g., `../debugging.md`), not directory paths
- Nested list indentation is **2 spaces** (enforced by `mdx_truly_sane_lists` with `nested_indent: 2`)
- Line length limit: 120 chars (`.markdownlint.yaml` MD013). Code blocks and tables are exempt.
- Inline HTML is allowed (MD033 disabled)
- Console commands use ` ```console ` fenced blocks with `$ ` prompt prefix

### Blog posts
- Front matter: `date` (YYYY-MM-DD) and `categories` list — no `title` field (title comes from the `# Heading`)
- Must include `<!-- more -->` break to separate excerpt from full content

### Tags and drafts
- Wiki pages (not blog posts) can have `tags:` in front matter; valid tag values are defined in `mkdocs.yml` under `extra.tags`
- Pages tagged `draft` or `braindump` are excluded from the published site via `file-filter` plugin (CI only — they appear locally)

### Linting
- YAML: `yamllint` with 120 char line limit (`.yamllint`), `.direnv/` ignored
- Markdown: `.markdownlint.yaml` config exists but no lint task is wired up — run `markdownlint` manually if needed
