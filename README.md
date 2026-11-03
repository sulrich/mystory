# MyStory

A modular, responsive one-page theme for Hugo, ported from
[Story by HTML5UP](https://html5up.net/story) (via
[hugo-story](https://github.com/caressofsteel/hugo-story)).

Unlike hugo-story, MyStory is **front matter-driven**: every homepage
section is a markdown content fragment whose layout knobs live in its front
matter. Nothing is read from `data/`.

## Quick start

```toml
# hugo.toml
theme = "mystory"

[params]
  description = "..."
  author = "..."            # <meta name="author">
  copyright = "..."         # footer line above the attribution
  favicon = true            # render favicon links (ship your icons in static/)

  [params.social]           # footer icons; all optional
    linkedin = "..."
    github = "..."
    twitter = "..."
    bluesky = "..."
    instagram = "..."
    email = "you@example.com"

  [params.asset]
    fontawesome_css = ["css/fontawesome-all.min.css"]  # needed for icons
    # fontawesome_js = ["js/fontawesome-all.min.js"]   # usually unnecessary

[markup.goldmark.renderer]
  unsafe = true             # content fragments use inline HTML
```

## Composing the homepage

The theme's `layouts/index.html` renders the `sections` list from the
homepage's front matter, in order, then the footer:

```yaml
# content/_index.md
---
title: "My Site"
sections:
  - partial: "banner"
    page: "/intro"
  - partial: "spotlight"
    page: "/feature"
  - partial: "gallery"
    page: "/gallery"
  - partial: "items"
    page: "/items"
  - partial: "section"
    page: "/plain-markdown-block"
  - partial: "elements"            # static showcase; no page needed
  - partial: "elements_reference"  # static modifier reference
---
```

Each `page` is resolved with `site.GetPage`; the named partial receives that
Page. Entries without `page` get the homepage as context. Missing pages are
skipped silently.

To take over composition entirely, drop a `layouts/index.html` in your
project — Hugo's lookup order prefers it over the theme's. The same applies
to any partial (`layouts/partials/spotlight.html`, …).

## Section partials

All section partials share these front-matter knobs (all optional unless
noted):

- `style` — modifier classes (see the elements-reference section of the
  exampleSite for the full matrix: `styleN`, `orient-*`,
  `content-align-*`, `image-position-*`, `onload-*`, `onscroll-*`,
  `invert`, `color1`–`color7`, `fullscreen`)
- `id` — anchor id
- the markdown body is rendered via `.Content`

Section content fragments should set `headless: true` so Hugo doesn't try
to render them as standalone pages.

### `banner`

```yaml
title: "..."        # <h1>
style: "style1 orient-left content-align-left image-position-right fullscreen ..."
subtitle: "..."     # HTML allowed
image: "images/....jpg"
image_credit: 'Photo: <a href="...">Name</a>'  # optional caption on the image; HTML allowed
button: { label: "...", link: "#first" }
```

### `spotlight`

```yaml
title: "..."        # <h2>
style: "style1 orient-right ..."
id: "first"
image: "images/....jpg"
image_credit: "Photo: Name"   # optional caption on the image; HTML allowed
button: { label: "...", link: "#" }
```

### `gallery`

```yaml
title: "..."
style: "style2 medium lightbox onscroll-fade-in"   # modifiers on the gallery itself
pictures:
  - { image: "images/gallery/fulls/01.jpg",
      thumb: "images/gallery/thumbs/01.jpg",
      title: "...", content: "...", button: "Details" }
```

### `items`

```yaml
title: "..."
style: "style1 medium onscroll-fade-in"            # modifiers on the grid
items:
  - { name: "...", content: "...", style: "solid style2 major fa-gem" }
```

### `section`

Plain `wrapper` section for markdown content. Defaults to
`style1 align-left`.

### `elements` / `elements_reference`

Static showcases of the typography/form/table elements and the full
modifier reference. No front matter; add them to `sections` without a
`page`. Set `params.demo = true` to load `demo.js`, which makes the
modifier examples clickable (demo chrome — leave it off in production).

## Analytics

Google Analytics (GA4) uses Hugo's standard service config. Set the
measurement ID in the site's `hugo.toml` and the theme's `head` partial
emits the gtag snippet on every page:

```toml
[services.googleAnalytics]
  id = "G-XXXXXXXXXX"
```

No ID, no snippet. It only renders in production builds (`hugo`), not under
`hugo server`, so local previews don't pollute the numbers. Hugo's
`[privacy.googleAnalytics]` settings (e.g. `disable = true`) are honored.

## Assets

FontAwesome (css/js/webfonts) ships in the theme's `static/`; enable via
`params.asset` as above. Favicon files are **not** shipped with the theme
itself — generate your own set into the project's `static/` and set
`params.favicon = true`.

## Example site

`exampleSite/` is a standalone, functional Hugo site exercising every
partial — run `hugo -s exampleSite --themesDir ../..` from `themes/mystory/`.
Its sample images, gallery, and favicon/social demo assets are carried over
from [hugo-story](https://github.com/caressofsteel/hugo-story), the theme
this was forked from, purely so the example builds and looks complete out of
the box. Adopting projects should replace all of it — images, favicons, and
`params.social` — with their own.

## License

[Creative Commons Attribution 3.0](LICENSE). Original design:
[Story](https://html5up.net/story) by HTML5UP; Hugo port:
[CaressOfSteel](https://github.com/caressofsteel/hugo-story). The theme
footer carries this attribution; keep it or replace the footer partial in
your project.
