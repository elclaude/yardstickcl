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

## Input formats

Length and width are free-text fields parsed by `parseFeet()`. All of these
mean the same bed:

```
12.5        12'6"       12ft 6in       12 6
12          12'         12 feet        12-6
6"          6in         .5             12’ 6”   (smart quotes)
```

A read-back line under each field shows how the value was interpreted
(`= 12 ft 6 in`), using `≈` when the value isn't a whole number of inches.
Unparseable input reads `can't read that` and the results fall back to the
empty state rather than showing a stale number.

The fields are `type="text"` — `type="number"` rejects `'`, `"`, and spaces —
with `inputmode="decimal"` to keep a numeric keypad on mobile.

## Sticky summary bar

Below 48rem, a fixed bar mirrors the bag counts and cubic yards so they stay
readable while dimensions are being typed. Above 48rem it is `display: none`
and the page reverts to its normal bottom padding.

iOS does not shrink the layout viewport when the on-screen keyboard opens, so a
`position: fixed; bottom: 0` element ends up *behind* the keyboard — precisely
when this bar needs to be visible. `trackKeyboard()` reads `window.visualViewport`
(the actually-visible area), works out how much the keyboard overlaps, and sets
`bottom` to that value. Verified by faking `visualViewport.height` and firing
the real `resize` handler; the bar lands exactly on the keyboard's top edge.

The bar is `aria-hidden` — the results panel above is already an `aria-live`
region, and announcing both would double every update.

## The math

```
cubic feet  = length_ft x width_ft x (depth_in / 12)
cubic yards = cubic feet / 27
bags        = ceil(cubic feet / bag_size)
```

Bag counts always round up: bags are sold whole, listed bag volume is loose fill
measured before it settles, and real beds have curves and low spots that consume
more than the arithmetic predicts.
