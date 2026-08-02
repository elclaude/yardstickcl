# Yardstick

Calculators for outdoor projects. Measure in feet and inches, get the number of
bags to buy.

No frameworks, no build step, no dependencies. Each page is a single HTML file
with its CSS and JS inline.

## Structure

```
index.html                   hub — intro + list of calculators
mulch-calculator/index.html  the mulch calculator
sitemap.xml                  both URLs, absolute
_headers                     Cloudflare-only, inert here (see below)
```

Links between pages are **relative** (`mulch-calculator/`, `../`), not
absolute. This matters: GitHub Pages serves this project from the
`/yardstickcl/` subpath, so a root-absolute link like `/mulch-calculator/`
would resolve to the wrong place and 404.

### Adding a calculator

There is no build step, so the footer is duplicated per page. Adding one means:

1. `new-calculator/index.html` — copy an existing page for the shared styles
   and the `.site-footer` block.
2. Add an `<li>` to the footer list **in every page**, and mark the current
   page with `<span aria-current="page">` instead of a link.
3. Add a card to the hub's `.cards` list.
4. Add a `<url>` entry to `sitemap.xml`.

If this grows past three or four calculators, extracting the shared CSS into
one stylesheet is worth the extra request.

## Deployment

Deployed to GitHub Pages from `main`, folder `/ (root)`.

<https://elclaude.github.io/yardstickcl/>

There is no build step, so Pages publishes the repository root as-is. Pushing to
`main` redeploys. Responses carry `Cache-Control: max-age=600`, so an edit can
take up to ten minutes to appear — hard-refresh if you're checking immediately.

### About `_headers`

`_headers` is a **Cloudflare Pages** convention and has no effect on GitHub
Pages, which does not support custom response headers at all. The file is kept
only so the security headers survive if this ever moves to Cloudflare. Treat it
as inert today — the deployed site sends no CSP, `X-Frame-Options`, or
`Referrer-Policy`.

That is acceptable here: the page is fully self-contained, loads nothing
externally, and collects no input that leaves the browser.

## Local development

Anything that serves static files works. With Python:

```
python3 -m http.server 8080
```

Run it from the repository root so the paths match production —
<http://localhost:8080> is the hub, `/mulch-calculator/` is the calculator. To
test on a phone, bind to all interfaces and use the machine's LAN IP:

```
python3 -m http.server 8080 --bind 0.0.0.0
```

## The math

```
cubic feet  = length_ft x width_ft x (depth_in / 12)
cubic yards = cubic feet / 27
bags        = ceil(cubic feet / bag_size)
```

Bag counts always round up: bags are sold whole, listed bag volume is loose fill
measured before it settles, and real beds have curves and low spots that consume
more than the arithmetic predicts.
