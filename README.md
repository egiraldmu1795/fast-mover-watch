# Storefront — Fast Mover Watch Signal Subscription

A **lean, single-file static landing page** for the Fast Mover Watch subscription product.
No build step, no framework, no server. `index.html` is fully self-contained (inline CSS + JS).
Deploy anywhere static: Netlify drop, Cloudflare Pages, GitHub Pages, or open locally to preview.

## The hard line (ADR-0004)

This page **does not charge anyone and nothing is live.** EAB built the page; **Esteban** connects
payment and presses publish. Until then the page shows a "DRAFT / NOT LIVE" banner and every
subscribe button just explains that payment isn't wired.

## Go-live checklist (Esteban — ~20 min)

### Step 1 — Create the Gumroad Membership product

1. Log in to [gumroad.com](https://gumroad.com) → **New Product** → choose **Membership**.
2. Name it: **"Fast Mover Watch — Daily Signal Subscription"**.
3. Description (copy from `OFFER.md`): paper-trading / educational daily watchlist, pre-market
   momentum candidates, full levels, 10AM discipline, paper ledger. Paid members only.
   Include the full disclaimer from `OFFER.md` in the Gumroad product description.
4. Add **pricing tiers** (Gumroad Memberships support multiple):
   - **Monthly**: $79.00 / month
   - **Quarterly**: $199.00 / 3 months *(Gumroad supports custom billing intervals — set to
     "every 3 months" or use a separate membership tier)*
   - **Annual**: $699.00 / year
   > Gumroad currently handles monthly and annual natively; for quarterly, create a separate
   > product or tier named "Fast Mover Watch — Quarterly" at $199 billed every 3 months,
   > then link all three from the landing page (or simplify to monthly + annual at launch).
5. Set fulfillment to **email delivery** (MVP): Gumroad sends a confirmation email with a note
   that the daily `paid.md` digest will be delivered each market morning via email.
   Add your delivery email address and instructions in the Gumroad "Thank you" note field.
6. **Publish** the membership. Copy the product URL (e.g., `https://egirald.gumroad.com/l/XXXX`).

### Step 2 — Wire the URL into the landing page

Open `business/signal-subscription/storefront/index.html`.
Find the `WIRE SUBSCRIPTION HERE` block near the bottom of the `<script>` tag:

```js
const BUY_URL = "";   // <-- paste Gumroad membership URL here
const DRAFT   = true; // <-- set to false once BUY_URL is set and you approve publish
```

- Set `BUY_URL` to the Gumroad URL you copied in Step 1.
- Set `DRAFT = false` — this removes the "DRAFT / NOT LIVE" banner.

### Step 3 — Review disclaimers

Read the full disclaimer block at the bottom of `index.html` and confirm it accurately represents
the service. The claim-safety rules from `OFFER.md` must remain intact:
- Paper-only / simulation / educational.
- No income, earnings, profit, or performance guarantees.
- All levels illustrative, not real quotes.
- No live trading, no real brokerage.

**Do not remove or soften these disclaimers.**

### Step 4 — Deploy

Drag the `storefront/` folder to [Netlify Drop](https://app.netlify.com/drop),
[Cloudflare Pages](https://pages.cloudflare.com), or GitHub Pages.
The page is fully static — one HTML file, no server, no build step.

Optionally add `robots.txt` and `sitemap.xml` for SEO (replace `REPLACE-WITH-YOUR-DOMAIN`
with your real domain if you add those files).

### Step 5 — Delivery MVP

Until a member portal is built, delivery is:
- **Free tier**: post `data/digests/YYYY-MM-DD/free.html` publicly (GitHub Pages, Netlify, etc.).
- **Paid tier**: email the daily `data/digests/YYYY-MM-DD/paid.md` to paid members.
  Gumroad can export your member email list; use your preferred email tool (Gmail, Mailchimp, etc.)
  to send the digest. The `generate_daily_digest.ps1` script (see `scripts/`) runs the digest
  generator automatically each market morning.

### Step 6 — Approve publish

That's the gate. Once you flip `DRAFT=false`, set `BUY_URL`, and deploy — it's live.
EAB will not set `BUY_URL`, flip `DRAFT`, or deploy to a public host. Those are
the human-press-go steps.

---

## What's intentionally NOT automated

Per the money/publish guardrail (ADR-0004), EAB will not:
- Set `BUY_URL` to a real URL.
- Set `DRAFT = false`.
- Deploy to a public host.
- Charge any card or process any subscription.

EAB *will* keep improving the copy and scaffold on request.

---

## Files

- `index.html` — the deployable subscription landing page. Inline CSS + JS. Self-contained.
- `README.md` — this file (go-live checklist).

## Daily digest delivery dependency

The landing page references the daily Fast Mover Watch. The digest generator is:

```
scripts/generate_daily_digest.ps1
```

This script runs `scripts/fast_mover_digest.py` and outputs:
- `data/digests/YYYY-MM-DD/free.html` — public teaser (post this)
- `data/digests/YYYY-MM-DD/paid.html` — full digest HTML
- `data/digests/YYYY-MM-DD/paid.md` — email-ready markdown body

See `scripts/generate_daily_digest.ps1` for the `schtasks` command to schedule it daily.

## Compliance note

The page carries all required disclaimers (paper-only, educational, not financial advice,
no income guarantees, illustrative levels only, no live trading). Keep them. They protect
you legally and they build trust with serious learners — the right audience for this product.
