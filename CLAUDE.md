# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

MyStory is a Hugo theme: a fork of `hugo-story` (itself a port of HTML5UP's
"Story"), made **front matter-driven** instead of data-file-driven. Every
homepage section is a markdown content fragment whose layout knobs (style
modifiers, images, buttons, id) live in that fragment's front matter, and
`layouts/index.html` composes the homepage by iterating a `sections` list
from the homepage front matter. There is no application code — this repo
*is* the theme (`layouts/`, `assets/`, `static/`) plus `exampleSite/`, a
working Hugo site used to exercise and preview it.

## Commands

Requires Hugo Extended >= 0.67.3 (needed for the SCSS pipeline in
`layouts/partials/head.html`).

```sh
# serve the example site with live reload, from the repo root
hugo server -s exampleSite --themesDir ../..

# production build of the example site
hugo -s exampleSite --themesDir ../.. --minify
```

There is no test suite, linter, or package manager in this repo — verification
is building/serving `exampleSite` and checking the rendered output.

## Architecture

**Composition model**: `layouts/index.html` reads `.Params.sections` from
the homepage content file. Each entry is `{ partial, page }`: if `page` is
set, it's resolved via `site.GetPage` and passed as context to the named
partial in `layouts/partials/`; entries without `page` (e.g. the static
`elements`/`elements_reference` showcases) get the homepage itself as
context. Missing pages are skipped silently — this is deliberate, not a bug
to "fix" defensively. A project can override the whole homepage or any
single partial by dropping a same-named file under its own `layouts/`,
since Hugo's layout lookup prefers project files over theme files.

**Section partials** (`layouts/partials/`: `banner`, `spotlight`, `gallery`,
`items`, `section`, plus the static `elements`/`elements_reference`) all
follow the same contract: they read a `style` modifier string and optional
`id` from front matter, apply them as CSS classes on the wrapping
`<section>`, and render `.Content` (markdown body) or structured front
matter (e.g. `gallery`'s `pictures` list, `items`'s `items` list). The style
strings (`styleN`, `orient-*`, `content-align-*`, `image-position-*`,
`onload-*`, `onscroll-*`, `invert`, `color1`–`color7`, `fullscreen`, …) map
directly to SCSS modifier classes in `assets/sass/` — the full matrix is
demoed in the exampleSite's elements-reference section. Content fragments
that back these sections must set `headless: true` so Hugo doesn't also
render them as standalone pages.

**Asset pipeline**: CSS is Hugo Pipes SCSS (`assets/sass/main.scss` and
`noscript.scss`, compiled/minified in `layouts/partials/head.html`), not a
separate build step — editing `assets/sass/**/*.scss` and running `hugo
server` is enough to see changes. FontAwesome and other static assets under
`static/` (css/js/webfonts) are copied through Hugo's static pipeline;
opting in happens per-project via `params.asset.fontawesome_css` /
`fontawesome_js`, not automatically. Favicon files are intentionally *not*
shipped in the theme's `static/` — a project sets `params.favicon = true`
and supplies its own icon set.

**exampleSite** (`exampleSite/`) is a standalone Hugo site (its own
`hugo.toml`, `content/`, `static/`) that references this theme via
`--themesDir ../..`. Its images/favicons/`params.social` values are carried
over from upstream `hugo-story` purely so the demo looks complete — treat
them as placeholders, not as the theme's actual content, when editing
`exampleSite/content/*.md` to demonstrate a partial's front matter shape.

## Front matter contracts

When adding or modifying a section partial, keep its front matter keys
consistent with the documented shapes in `README.md` (banner: `title`,
`subtitle`, `image`, `image_credit`, `button`; spotlight: `title`,
`image`, `image_credit`, `button`;
gallery: `pictures: [{image, thumb, title, content, button}]`; items:
`items: [{name, content, style}]`). These are the theme's public API for
adopting projects — changing a key name is a breaking change and should be
called out as such, not made silently.
