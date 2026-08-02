# Mulch Calculator

A single-page mulch calculator. Enter bed dimensions in feet and inches, pick a
depth, get the number of 2 cu ft and 3 cu ft bags to buy.

No frameworks, no build step, no dependencies. The whole site is `index.html` —
CSS and JS are inline.

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

Then open <http://localhost:8080>. To test on a phone, bind to all interfaces
and use the machine's LAN IP:

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
