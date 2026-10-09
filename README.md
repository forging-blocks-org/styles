# Forging Blocks shared visual styles

A small, framework-neutral warm-dark CSS system for documentation sites. The canonical
source files live under `src/`; copy them from a pinned release tag (or link an immutable
CDN URL) into a consuming site. There is no build step.

## Using this repository

This repository is a source-only CSS library. There is no package manager or build step.
No generated build output is committed.

To preview the styles locally:

```sh
cd /path/to/styles
python3 -m http.server --directory .
```

Python uses port `8000` by default. Open `http://127.0.0.1:8000/demo/index.html` for
the gallery landing page or `http://127.0.0.1:8000/demo/mkdocs.html` for the MkDocs
adapter fixture. If port 8000 is busy, pass another port before `--directory`, for
example `python3 -m http.server 4173 --directory .`, and use that port in the URL.
Stop the server with `Ctrl+C`.

When consuming the library, copy the required pinned files from `src/` into the
consumer's asset directory. Load the core file first, then any adapter files required
by the page.

## Layers

| File | Role | Load order |
| --- | --- | --- |
| `src/forging-blocks-core.css` | Public `--fb-*` tokens and opt-in content hooks | 1 |
| `src/forging-blocks-mkdocs.css` | MkDocs Material and mkdocstrings adapter | 2, after core |
| `src/forging-blocks-mermaid.css` | Optional styles for rendered Mermaid markup | 3, after core |
| `src/forging-blocks-floating-menu.css` | Floating-menu adapter | 4 |

The core namespace is intentionally `--fb-*`. It does not define Material `--md-*`
aliases, request fonts, load JavaScript, or include site branding.

## Plain HTML

Add `fb-theme` to the page root and `fb-prose` to the content container. The core
selectors are opt-in so they do not change unrelated documents.

```html
<!doctype html>
<html class="fb-theme" lang="en">
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="assets/css/forging-blocks-core.css">
    <link rel="stylesheet" href="assets/css/forging-blocks-mermaid.css">
    <link rel="stylesheet" href="assets/css/forging-blocks-floating-menu.css">
  </head>
  <body>
    <main class="fb-prose">
      <h1>Documentation</h1>
      <p>Use <code>fb-prose</code> around rendered content.</p>
    </main>
  </body>
</html>
```

The `assets/css/` paths in this example are the consuming site's copies of the pinned
files from this repository's `src/` directory.

The core provides `.fb-note`, `.fb-details`, `.fb-details--source`, and
`.fb-highlighttable` hooks for consumers that own their markup. The recommended pairing
is Roboto with Roboto Mono when the host already provides those families; keep a
host-selected fallback stack when it does not. No font files or external font requests
are shipped.

## MkDocs Material

Copy the pinned source files from `src/` into your site's `docs/assets/css/`
directory, then list them in this order:

```yaml
extra_css:
  - assets/css/forging-blocks-core.css
  - assets/css/forging-blocks-mkdocs.css
  - assets/css/forging-blocks-mermaid.css       # optional
  - assets/css/forging-blocks-floating-menu.css # optional
```

The Material adapter owns `.md-*` and `.md-typeset` selectors while the core remains
framework-neutral. Material's built-in version selector remains available by default.
Consumers replacing it with `.fb-floating-menu` can add `fb-hide-md-version` to the
page root. The CSS styles appearance and accessibility state, not open/close behavior.

Mermaid CSS styles already-rendered `.mermaid` markup. It does not load Mermaid or
initialize a renderer; the MkDocs/site integration remains responsible for rendering
and any initialization colors.

Pin consumers to a release tag or immutable commit. Do not use an unversioned `main`
branch or raw URL, because those can change without a site release.

## Source mapping

The reusable styles were extracted from the visual portions of:

- `forging-blocks/docs/assets/css/theme.css` -> `forging-blocks-core.css`,
  `forging-blocks-mkdocs.css`, and `forging-blocks-floating-menu.css`
- `forging-blocks/docs/assets/css/mermaid-theme.css` -> `forging-blocks-mermaid.css`

Brand artwork, repository/site copy, logos, favicons, runtime labels, and source-specific
URLs are deliberately excluded. The source repository at
`/home/gbrennon/repos/forging-blocks-org/forging-blocks` was not changed.

## Local fixtures

Use the local server command in “Using this repository” above. It serves on port
`8000` by default; pass a different port if 8000 is busy.

The component gallery is available at:

- `demo/index.html` — gallery landing page
- `demo/content.html` — generic prose, code, tables, callouts, and details
- `demo/navigation.html` — tabs, sidebars, active states, and floating menus
- `demo/diagrams.html` — Mermaid-rendered nodes, paths, and clusters
- `demo/mkdocs.html` — Material and mkdocstrings-shaped fixture

Open any page after starting the server above. All pages use local CSS from `src/` and
contain no JavaScript or external assets.
