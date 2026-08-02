# Mulch Calculator

A single-page mulch calculator. Enter bed dimensions in feet and inches, pick a
depth, get the number of 2 cu ft and 3 cu ft bags to buy.

No frameworks, no build step, no dependencies. The whole site is `index.html` —
CSS and JS are inline.

## Deployment

Deployed to Cloudflare Pages directly from this repository.

| Setting                | Value          |
| ---------------------- | -------------- |
| Framework preset       | None           |
| Build command          | *(leave empty)* |
| Build output directory | `/`            |

Because there is no build step, Cloudflare publishes the repository root as-is.
Pushing to `main` triggers a deploy.

`_headers` is a Cloudflare Pages convention — it sets security headers and keeps
`index.html` uncached so edits go live immediately rather than sitting in a CDN
cache. It has no effect when opening the file locally.

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
