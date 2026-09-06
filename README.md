# Court of Accounts

Landing page for *Court of Accounts: A Tale of Ledgers, Loyalty, and Fancy Chickens*
(Fast Camel Press).

Static site. Built to mirror `kari-kounkel/ladybug` and `kari-kounkel/chickens` —
plain HTML, no build step, Fraunces + Mulish. Deploys to
https://accounts.karikounkel.com.

## Why this one has a `/tools` page

**The barcode printed inside the book points at `accounts.karikounkel.com/tools`.**
That is the whole reason this repo exists. Before it, the domain had no DNS record
at all — it had never resolved for anyone — so every reader who scanned that code
got nothing.

`tools.html` is that page. With `cleanUrls` on (see `vercel.json`) it serves at
`/tools`, which is the printed address exactly.

**Do not rename, move, or delete `tools.html`.** The address is printed in a
physical book and cannot be changed.

## Deploying

Static site, no build step. Same setup as `kari-kounkel/ladybug`:

1. Vercel → New Project → import `kari-kounkel/accounts`
2. Framework Preset: **Other** — no build command, no output directory
3. Domains → add `accounts.karikounkel.com`
4. DNS: one CNAME, `accounts` → `cname.vercel-dns.com`
   (DNS for karikounkel.com is at DreamHost)

## The tools list is live, not copied

`tools.html` reads the catalogue from the **cares-works** Supabase project
(`qcikhcnclduakriextsz`) at page load, rather than hard-coding 30 tools into
this file. Publish a tool in CARES Works and it appears here; nothing to
re-deploy, and the page cannot drift out of date.

Each tool links to `https://tools.caresmn.com/tools/<slug>`, except the few rows
that carry their own `href` (for example Steward, at `/steward`).

**Security shape.** The key in `tools.html` is the project's publishable anon key
— the same one already shipped in the public `tools.caresmn.com` JavaScript
bundle, so this file exposes nothing that was not already public. RLS is on for
`public.tools` with exactly one policy: `SELECT` where `is_published = true`.
There is no insert, update, or delete policy. The key can read published tools
and can do nothing else.

**Not verified from the build sandbox:** the browser-to-Supabase request itself —
outbound `supabase.co` is blocked here. The render path and the failure path were
both verified against a stubbed response in headless Chromium. Load `/tools` once
after the first deploy. If the list does not appear, the likely cause is the key
format; swapping `SUPABASE_KEY` for the project's current anon JWT is the
one-line fix.

Failure is handled: if the fetch errors or returns nothing, the page shows a
button through to `tools.caresmn.com/tools` instead of a dead end. A reader
holding the book should never hit a wall.

## Categories

`tools.html` orders categories in `CATEGORIES` (bookkeeping first, since that is
the book's subject). A tool whose category is not in that list still renders,
under **More tools** — new categories never silently disappear. To promote one,
add it to the array.

## The cover

`images/cover-front.jpg` is the published cover (600×900), copied from
`cares-works/public/court-of-accounts-cover.jpg`. It is also the Open Graph
share image, so whatever replaces it is what shows up when the link is pasted
anywhere.

## No mailing-list form

Ladybug and chickens each carry a signup form, because both were pre-launch and
needed a launch-day list. This book is already in print and is serialized to
CARES Works members, so the list already exists — the membership is the list.
Adding a second one here would split it. That is a decision, not an oversight.

## Still to wire

- **Buy link for the print edition.** The page currently routes readers to the
  membership (monthly / annual), which is how the book is read online. There is
  no retail link on the page because none was supplied.
- **Google Analytics.** Ladybug carries `G-T1PLCXES2Y` and chickens has none.
  Add a property for this domain if you want it tracked.
- **Chapters 4–13 and the epilogue.** The docket lists the four titles that
  exist; the page says "Thirteen chapters and an epilogue in all" without naming
  the unwritten ones.

## Facts the page asserts

Everything on the page traces to existing material, not invention:

| Claim | Source |
|---|---|
| Title, subtitle, cover | `cares-works/public/court-of-accounts-cover.jpg` |
| "A business parable set in the Kingdom of Eggerton"; busy vs. profitable; the cost of loyalty; the chickens getting fancy | `cares-works/src/pages/Dashboard.jsx`, Court of Accounts tab |
| The whimsy warning, verbatim | Same file — the notice above the chapter list |
| Chapter titles: Prologue (with audio), The Kingdom of Eggerton, Lady Delia and the Court, The Record Keepers | `COURT_CHAPTERS` in the same file |
| "Thirteen chapters and an epilogue in all" | `COURT_SLUGS` in `cares-works/src/App.jsx` — prologue, chapter-1…13, epilogue |
| One chapter a month; annual members get the full book on day one | Dashboard copy and the CARES Works landing page |
| $27/month, $270/year, and both Stripe links | `cares-works/src/pages/Landing.jsx` |
| "Where your books face the judge" | The Court of Accounts marble description in `kari-kounkel/karikounkel`'s `marbles.js` |
| "Revenue is vanity. Net profit is sanity." | The vanity callout on the CARES Works landing page |
| Rust `#B5651D` in the palette | The colour already assigned to this book's marble in `marbles.js` |

The Eggerton framing in "A tiny kingdom, and a very large problem" is written
from the premise above; it does not name characters or events beyond the four
published chapter titles.
