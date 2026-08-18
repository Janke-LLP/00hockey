# Zero:Zero Hockey — Landing Page

A single static landing page for the "Helping Your Child Navigate Minor Hockey" preseason series. No build step, no backend — just `index.html` and one stylesheet. Registration, confirmations, reminders and in-session polling are all handled by Microsoft Teams Virtual Events, so this page's only job is to explain the program and send people to that Teams registration link.

## File structure

```
00hockey/
├── index.html                          the landing page
├── resume.html                         Scott's coaching background, as readable HTML
├── lace-em-up.html                     Lace 'Em Up program page, with a registration form
├── LACE_EM_UP_SETUP.md                 how to connect that form to a Google Sheet
├── css/styles.css                      brand styles (colors, type, layout), shared by all pages
├── assets/Scott-Janke-Coaching-Resume.pdf   original resume PDF (linked as an optional download)
└── README.md                           this file
```

`resume.html` transcribes the resume content directly into the page rather than embedding the PDF, since that's a better experience on mobile and for screen readers. The PDF itself still lives in `assets/` and is linked near the bottom of the page for anyone who wants the original document.

## Preview it locally

No install needed. From this folder, run:

```
python3 -m http.server 8000
```

Then open `http://localhost:8000` in a browser. (Any static server works — this is optional, you can also just double-click `index.html`.)

## Publish it with GitHub Pages

1. Push this folder's contents to the `main` branch of this repo (`Janke-LLP/00hockey`).
2. On GitHub, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to "Deploy from a branch," branch `main`, folder `/ (root)`.
4. Save. GitHub will give you a `https://janke-llp.github.io/00hockey/` URL within a minute or two.

## Pointing your own domain at it

Since you have your own domain:

1. In your DNS provider, add a `CNAME` record pointing your subdomain (e.g. `www` or a dedicated one) to `janke-llp.github.io`. For an apex/root domain (no `www`), use GitHub's documented `A` records instead — see [GitHub's custom domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site).
2. Add a file named exactly `CNAME` (no extension) to the root of this repo, containing just your domain on one line, e.g.:
   ```
   www.yourdomain.org
   ```
3. In **Settings → Pages**, enter the same domain in the "Custom domain" field and enable "Enforce HTTPS" once it's verified.

DNS changes can take anywhere from a few minutes to a few hours to propagate.

## Things to update before launch

- **Session dates/time** — currently Aug 25, Sep 1, Sep 8, 2026 at 8:00 pm Saskatchewan time, in `index.html`. Search for "Aug 25" to find all three.
- **CTA button count** — both large "Register" buttons currently read "Register for Webinar 1 of 3." After each session airs, update to "2 of 3" then "3 of 3." Search for "Webinar 1 of 3" in `index.html` (each instance has an HTML comment right above it as a marker).
- **Lace 'Em Up form endpoint** — connected to a Google Apps Script Web App (see `LACE_EM_UP_SETUP.md`). Submit a real test entry once it's live to confirm a row lands in the Sheet.
- **Lace 'Em Up eligible birth years** — the page currently says "born in 2021, 2022, or 2023, or those new to skating or hockey." The birth years were estimated from last year's poster (which listed 2020/21/22 for the 2025 session) — confirm before publishing.
- **Registration link** — the Microsoft Teams event URL is used in three places (nav, hero, and the register section). Search for `events.teams.microsoft.com` to update if it changes.
- **Privacy and conduct guidelines** — the fine print near the register button references these; add real links once that copy exists.
- **Heading font** — headings currently use Poppins (loaded from Google Fonts) as a stand-in for Gilroy, which isn't available as a free web font. To use real Gilroy, replace the Google Fonts `<link>` in `index.html` with self-hosted `@font-face` rules pointing at licensed Gilroy font files, and update `--font-heading` in `css/styles.css`.
- **Photos** — none are used currently, by design. If you add any later (presenter headshot, session imagery), follow the brand guide's photography direction (quiet, real moments — not staged or celebratory).

## Accessibility notes

The page includes a skip-to-content link, visible keyboard focus states, semantic headings/landmarks, and respects `prefers-reduced-motion`. Worth a quick manual tab-through before launch to confirm it still feels right after any future edits.
