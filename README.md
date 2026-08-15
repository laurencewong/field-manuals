# Field Manuals — storefront

Static storefront for the **Field Manuals** guides, hosted on GitHub Pages.
Plain HTML/CSS/JS in one file (`index.html`) plus the guide PDFs under `guides/`.
No build step, no framework, no server.

```
index.html                       the whole storefront (design + copy + logic)
guides/
  governed-agent-boundary-kit.pdf   FM-01 — downloads on unlock
  headless-claude-runners.pdf       FM-02 — downloads on unlock
fonts/
  anton-latin.woff2                 self-hosted display face — Anton (h1, doc numbers, card titles)
  ibm-plex-mono-{400,500,600}-latin.woff2  self-hosted mono furniture — IBM Plex Mono
  space-grotesk-latin-var.woff2     self-hosted body — Space Grotesk (variable 300–700)
  OFL.txt                           font licenses (all SIL OFL 1.1)
CNAME.example                    custom-domain placeholder (rename to CNAME)
.nojekyll                         serve files as-is, skip Jekyll
```

The type is the locked field-manual stack — **Anton** (display), **IBM Plex Mono**
(the load-bearing mono furniture: spec blocks, stamps, doc numbers), and **Space
Grotesk** (body). All are self-hosted, not CDN-loaded — no external dependency at
runtime — and all are licensed under the SIL Open Font License 1.1 (`fonts/OFL.txt`).

> **Note:** the page carries no build/version changelog in its footer — that kind
> of internal note lives here in the README only, never on the live storefront.

The loyalty dataset is **preview-only** on the page — there is intentionally no
PDF for it, and its card offers a list join, not a download.

---

## How the page works today

1. Visitor clicks **Get the … guide**, enters an email.
2. The email is validated, a download code is shown, and the PDF download
   starts immediately (with a manual download button as backup).
3. If an email endpoint is configured (below), the address is also POSTed to
   your list. Until then, unlock is purely client-side — no email is stored.

---

## 1. Connect the email list  ← your one wiring step

Open `index.html`, find this line near the bottom (in the `<script>`):

```js
var EMAIL_ENDPOINT = null;
```

Replace `null` with your form endpoint as a string, e.g.:

```js
var EMAIL_ENDPOINT = "https://formspree.io/f/abcdwxyz";
```

Any endpoint that accepts a JSON `POST` works. The page sends:

```json
{ "email": "visitor@example.com", "guide": "boundary" }
```

Quickest option — **Formspree** (free tier, no code):
1. Create a form at https://formspree.io → copy its form URL.
2. Paste it in as `EMAIL_ENDPOINT`, commit, push.

Also fine: **ConvertKit** / **Mailchimp** — use the form's action/POST URL.
(These may expect form-encoded fields rather than JSON; if so, tell me and I'll
switch the `postEmail()` body to `FormData`. Formspree accepts the JSON as-is.)

Downloads keep working whether or not the endpoint is set — capture is additive.

---

## 2. Custom domain (optional)

A placeholder `CNAME.example` file is in the repo (kept inert so it can't
interfere with the default `*.github.io` URL). To point your own domain:

1. Rename `CNAME.example` to `CNAME` and edit it to contain only your domain,
   e.g. `manuals.uplevelstudios.com` (one line, no `https://`, no trailing slash).
2. At your DNS provider add either:
   - a `CNAME` record: `manuals` → `laurencewong.github.io`, **or**
   - for an apex/root domain, four `A` records to GitHub Pages IPs:
     `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`.
3. In the repo: **Settings → Pages → Custom domain**, enter the same domain,
   save, and tick **Enforce HTTPS** once the cert provisions (a few minutes).

Until you do this, the site lives at the `*.github.io` URL (see repo About).

---

## 3. Paid tier later (optional — not built)

v2 is all free. To sell a future guide without adding a backend, drop in
**Gumroad**:

1. Upload the PDF as a Gumroad product, set a price, get its product URL.
2. Replace that guide's download link with a Gumroad "Buy" link (or use the
   Gumroad overlay embed snippet on the button).
3. Gumroad handles checkout, payment, and delivery — the storefront stays static.

No payment code lives in this repo, by design.

---

## Deploy / update

Pages serves the `main` branch root. To publish a change:

```sh
git add -A && git commit -m "update storefront" && git push
```

Pages rebuilds in ~1–2 minutes.

**Publishing rule:** only ship-approved guides go in `guides/`. Do not commit
vault internals, drafts, decision logs, or the on-hold loyalty guide.
