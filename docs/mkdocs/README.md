# MkDocs

How this notes site is set up, and how to repeat it in another repo.

## 1. Install

MkDocs is only needed to build the site, so add it as a dev dependency:

```bash
uv init            # skip if the repo already has a pyproject.toml
uv add --dev mkdocs-material
```

## 2. Folder layout

MkDocs builds from a `docs/` folder. It can't be the repo root itself, so the notes live one level down:

```
repo/
├── mkdocs.yml        # site config
├── pyproject.toml
├── README.md         # repo landing page on GitHub (not part of the site)
└── docs/
    ├── index.md      # site home page
    ├── git/
    │   ├── README.md           # section page for "Git"
    │   └── fixing-mistakes.md  # extra page in the same section
    └── python/
        └── pydantic/
            └── README.md
```

Conventions that make this work:

- **Lowercase kebab-case** folder and file names (`project-structure/`), which become clean URLs.
- **`README.md` in every folder** is that folder's main page. GitHub also shows it automatically when you open the folder.
- **One `#` heading per file.** MkDocs uses it as the page title in the sidebar and browser tab. Use `##` for sections and `###` for subsections, and don't skip levels.

## 3. mkdocs.yml

```yaml
site_name: My Notes

theme:
  name: material
  features:
    - navigation.indexes   # a folder's README.md becomes the section page
    - content.code.copy    # copy button on code blocks
    - search.highlight

markdown_extensions:
  - admonition             # !!! note boxes
  - pymdownx.highlight     # syntax highlighting
  - pymdownx.superfences   # code blocks inside lists
  - toc:
      permalink: true      # link icon next to each heading

nav:
  - Home: index.md
  - Git:                       # a topic with several pages
      - git/README.md
      - git/fixing-mistakes.md
  - Pydantic: python/pydantic/README.md
```

- **`nav` is optional.** Without it, MkDocs builds the sidebar from the folder names, but it capitalises them oddly ("Ssh", "Apis"). Listing pages by hand keeps proper names and controls the order.
- **A bare path under a section** (`- git/README.md`) uses the page's own `#` heading as its title. `Title: path` overrides it.
- **Dark mode** is the `palette` block in this repo's `mkdocs.yml`. Copy it as is.

## 4. Ignore the build output

Add this to `.gitignore`:

```
site/
```

## 5. Run it

```bash
uv run mkdocs serve           # live preview at http://127.0.0.1:8000
uv run mkdocs build --strict  # fails on broken links or pages missing from nav
```

Run `build --strict` before committing. It catches pages you forgot to add to `nav`.

## 6. Publish (optional)

```bash
uv run mkdocs gh-deploy
```

This builds the site and pushes it to a `gh-pages` branch, which GitHub Pages serves at `https://<user>.github.io/<repo>/`. **The site is public**, so remove emails, keys and other personal details first.

**First time only:** on GitHub, open the repo's Settings → Pages and check the source is *Deploy from a branch*, branch `gh-pages`, folder `/ (root)`.

**Updating the site:** `git push` does **not** update the site. It only updates `main`. The site comes from the `gh-pages` branch, which only `gh-deploy` writes to. After every change:

```bash
git push                  # 1. save your notes to main
uv run mkdocs gh-deploy   # 2. rebuild and publish the site
```

`gh-deploy` builds from your **local files**, so commit and push first. Otherwise the site can show changes that aren't on GitHub yet.

To see what's live, run `git log -1 origin/gh-pages`. The message says which commit of `main` was deployed (e.g. `Deployed 8c02c98 with MkDocs version: 1.6.1`).

## Adding a note

1. Create the `.md` file under `docs/`, starting with a `# Title`.
2. Add it to `nav` in `mkdocs.yml`.
3. Run `uv run mkdocs build --strict` to check it.
4. Commit and push, then run `uv run mkdocs gh-deploy` to update the live site.

!!! note "Material for MkDocs end of life"
    Material for MkDocs stops receiving fixes on May 5, 2027. Its successor, [Zensical](https://zensical.org), reads the same `mkdocs.yml`: `uv add --dev zensical`, then `uv run zensical serve`.
