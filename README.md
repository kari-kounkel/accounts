# Court of Accounts

Landing page for *Court of Accounts: A Tale of Ledgers, Loyalty, and Dressed-Up
Chickens* (Fast Camel Press), plus **Kingdom Tools**, the companion workbook.

Static site, no build step. Deploys to https://accounts.karikounkel.com.

## Where this came from

These pages were written in July 2026 and lived at `kkstore/coa/` — reachable at
`karikounkel.shop/coa/` but never moved onto their own domain. They were always
*written* for this one: the Open Graph tags and the "Open the Kingdom Tools" band
both name `accounts.karikounkel.com`. This repo is that move.

## Why `/tools` matters more than anything else here

**The barcode printed inside the book points at `accounts.karikounkel.com/tools`.**
Until this repo was deployed that address had no DNS record at all — it had never
resolved for anyone, so every reader who scanned the code got nothing.

`tools.html` is that page. `cleanUrls` in `vercel.json` serves it at `/tools`,
which is the printed address exactly.

**Do not rename, move, or delete `tools.html`, and do not remove `cleanUrls`.**
The address is printed in a physical book and cannot be changed.

## Files

| File | What it is |
|---|---|
| `index.html` | The book page — deal-a-courtier, Meet the Court (23 characters), editions, Part One / Part Two |
| `tools.html` | **Kingdom Tools** — serves at `/tools`. The workbook download and the 20 tools-to-characters map |
| `Kingdom_Tools.xlsx` | The workbook itself. 21 tabs: Welcome, plus one per character |
| `thanks.html` | Post-checkout page. Calls `/api/get-pdf` with the Stripe session id |
| `api/get-pdf.js` | Verifies a paid Stripe Checkout Session, then mints a 7-day signed Supabase Storage URL for the book PDF |
| `cover.png` | Cover art, also the Open Graph share image |

## Deploying

Same setup as `kari-kounkel/ladybug` and `kari-kounkel/chickens`:

1. Vercel → New Project → import `kari-kounkel/accounts`
2. Framework Preset: **Other** — no build command, no output directory
3. Domains → add `accounts.karikounkel.com`
4. DNS: one CNAME, `accounts` → `cname.vercel-dns.com`
   (DNS for karikounkel.com is at DreamHost. This is what `chickens.karikounkel.com`
   uses today; ladybug is still on an older A record — follow chickens.)

### Environment variables — required, or checkout delivery breaks

`api/get-pdf.js` needs two env vars set **on this Vercel project**. They are
per-project and do not carry over from ladybug:

| Variable | Value |
|---|---|
| `STRIPE_SECRET_KEY` | `sk_live_…` or `rk_live_…` (Checkout Sessions: Read is enough) |
| `SUPABASE_SERVICE_ROLE_KEY` | service_role JWT from the **kkstore** Supabase project |

It also expects, in the kkstore Supabase project: a **private** Storage bucket
named `coa-pdf` containing `court-of-accounts.pdf`. If those are named
differently, update `BUCKET` / `FILE` at the top of `api/get-pdf.js`.

Without these, buyers reach `thanks.html` and get no download.

## ⚠️ The buy buttons do not work yet

`index.html` ships with three **placeholder** hrefs, not real links:

```
STRIPE_LINK_DIGITAL     Digital Edition    $25
STRIPE_LINK_PRINT       Print Edition      $35
STRIPE_LINK_DELUXE      The Court Edition  $50
```

Clicking any of them goes nowhere. Replace each with its real Stripe Payment Link
(or the Amazon URL for print) before the page is promoted anywhere.

This does **not** block the barcode: `/tools` and the Kingdom Tools workbook work
regardless, and that is what the printed code points at.

**Until the real links are pasted, the page will not 404 a buyer.** A short script
at the bottom of `index.html` (`orderButtonSafetyNet`) detects any href still set to
a placeholder and turns that button into a pre-filled email order to
kari@karikounkel.com naming the edition and price, and rewrites the "Secure checkout
by Stripe" footnote to match. It only touches buttons whose href is still a
placeholder, so **pasting a real Payment Link is all that is needed** — the script
then ignores that button and there is nothing to undo.

## Still to wire

- **The three Stripe links above** — the one thing standing between this page and money
- The `coa-pdf` bucket and the two env vars, so paid downloads deliver
- Analytics is the shared karikounkel GA4 property (`G-WHKMKCD1SD`); split it out if
  you want this domain tracked separately
- Decide what `karikounkel.shop/coa/` should do now — leaving both live means two
  copies drifting apart, which is how this page stayed lost for six weeks. A
  redirect to `accounts.karikounkel.com` is the obvious fix.
- The Court of Accounts marble on `karikounkel.com` is still `live:false, link:"#"`.
  Point it here once the domain resolves.

## An earlier draft

`claude/parchment-draft` holds a different take on this site, written before these
pages were found — parchment and court navy, with a `/tools` that indexed the CARES
Works business-tool library instead of the workbook. Kept for reference only.
**It carries wrong facts** (the subtitle as "Fancy Chickens", the older cover, and
membership pricing rather than the $25/$35/$50 editions), so do not merge it. Its
one idea worth stealing is a link between this book and the CARES Works tool
library, which nothing currently connects.
