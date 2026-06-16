# Payments Jargon — deploy & edit guide

A single-page payments glossary (502 terms). Plain HTML/CSS/JS, no build step.
The content lives in `data/terms.json`; the page fetches it at load. You can edit
that file three ways: by hand, conversationally with an AI agent, or through the
Pages CMS visual editor.

## What's in here

```
index.html        The site (fetches data/terms.json on load)
data/terms.json   All 502 terms — the single source of content
og-image.png      Social share card (referenced by the meta tags)
.pages.yml        Pages CMS config (visual editing)
README.md         This file
```

---

## One-time setup (your part — ~15 minutes)

These steps need your own accounts, so they're yours to click. I can't (and
shouldn't) log into GitHub, Netlify or Porkbun on your behalf.

### 1. Put the files on GitHub
1. Create a new repository at https://github.com/new (e.g. `payments-jargon`).
   Private is fine — Netlify and Pages CMS can both read private repos once you
   authorise them.
2. Upload every file in this folder, keeping the structure (so `data/terms.json`
   stays inside a `data` folder, and `.pages.yml` sits at the top level).
   Easiest path: on the new repo page, "uploading an existing file" → drag the
   whole contents in. (GitHub may hide `.pages.yml` because it starts with a dot —
   it still uploads; you can confirm it's there afterwards.)

### 2. Deploy on Netlify
1. In Netlify: **Add new site → Import an existing project → GitHub**, and pick
   the repo. Authorise Netlify to access it if prompted.
2. Build settings: leave **build command empty** and set **publish directory** to
   the repo root (`.`). There's no build step — it's a static site.
3. Deploy. You'll get a temporary `something.netlify.app` URL. Open it and check
   it loads and the terms appear.

   > Connecting via GitHub (rather than drag-and-drop) matters: it gives you
   > automatic redeploys whenever `terms.json` changes, and it's what makes the
   > CMS and conversational editing flow through to the live site.

### 3. Point paymentsjargon.com at it
1. In Netlify: **Domain settings → Add a domain →** `paymentsjargon.com`.
2. Netlify will show you the exact DNS records to add. Two routes:
   - **Simplest — use Netlify DNS:** Netlify gives you a set of nameservers
     (e.g. `dns1.p0x.nsone.net`…). In Porkbun, open the domain → **Authoritative
     Nameservers / NS** → replace Porkbun's with the four Netlify gives you.
   - **Keep DNS at Porkbun:** add the records Netlify displays — typically an
     **A record** for the apex (`@`) to Netlify's load-balancer IP and a
     **CNAME** for `www` to your `*.netlify.app` address.
   Use the exact values Netlify shows you on the day — don't copy them from memory
   or old guides, as they can change.
3. HTTPS is automatic: once DNS resolves, Netlify provisions a free Let's Encrypt
   certificate (can take a few minutes to an hour to propagate).

> The SEO meta tags and the share image already point at
> `https://paymentsjargon.com/`, so they start working the moment the domain is
> live — no extra step.

### 4. Turn on visual editing (Pages CMS)
1. Go to https://app.pagescms.org and **sign in with GitHub**.
2. Authorise it for your repo. It reads `.pages.yml` and gives you a form-based
   editor for every term — add, edit, reorder, toggle the buzzword flag.
3. Saving in Pages CMS commits to GitHub, which triggers a Netlify redeploy. Your
   change is live in a minute or two.

---

## Editing the glossary, day to day

All three of these edit the same `data/terms.json`, so you can mix and match:

- **Pages CMS** (visual) — best for a quick single fix or adding one term from
  your phone. Go to app.pagescms.org, edit, save.
- **Conversational** (an AI agent) — best for bulk or structural work: "re-link
  every SEPA entry", "audit the buzzwords", "add these 10 terms". The agent edits
  `terms.json`; you then commit/push it (or paste the updated file into GitHub).
- **By hand** — `terms.json` is plain JSON; edit and commit if you're comfortable.

### Term shape
```json
{
  "w": "Interchange",          // term (required)
  "c": "pricing",              // category code (required)
  "ph": "/ˈɪn.tə.tʃeɪndʒ/",    // optional IPA, only for tricky pronunciations
  "exp": "European Payments Initiative", // optional subhead/acronym expansion
  "p": "The actual definition…",         // required
  "x": "The relatable, witty one…",      // required
  "g": "What it's good for…",            // required
  "b": 1                        // optional — present/true = buzzword (✦)
}
```
Category codes: `cards`, `rails`, `infra`, `crypto`, `methods`, `pricing`,
`risk`, `eco`.

Cross-links: inside any definition write `[[Term]]` or `[[Term|display text]]`
to link to another entry.

---

## A note on SEO

Site-level SEO is done: title, meta description, Open Graph + Twitter cards,
canonical URL, JSON-LD, and the share image. Test the link preview with
https://www.opengraph.xyz once you're live.

The one limitation: because this is a single page, Google indexes it as one page,
not one per term. You'll rank for "payments jargon / glossary", but not for each
individual term. Giving every term its own URL is a future enhancement — it can be
done from this same `terms.json` with a small static-site generator, without
rebuilding the site in a heavier framework.
