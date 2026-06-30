# Credo Legal — Meta Ads v3 · Upload Handoff

Everything needed to build the Credo Legal debt-defense campaign in Meta Ads Manager (via your MCP or by hand). Prepared 2026-06-29.

## What's in this package

```
credo-meta-ads-v3-handoff/
├── README.md                  ← you are here
├── campaign-manifest.json     ← machine-readable full structure (campaign → ad sets → ads → copy + creative paths)
├── copy/
│   ├── headlines.csv          ← 5 campaign-wide headline variants
│   ├── messages.csv           ← 70 message variants (14 ads × 5), footer already appended
│   └── ads.csv                ← per-ad: final URL, UTM URL, description
├── asset-manifest.csv         ← every creative file → ad / direction / ratio
├── asset-overview.xlsx        ← same content as the CSVs/JSON, in one human-readable workbook
└── creatives/
    └── <Ad>/                  ← 12 images (3 directions × 4 ratios) + 1 video per ad
```

`campaign-manifest.json` is the source of truth for a scripted upload. The `.xlsx` and `.csv` files are the same data for human review.

## Campaign structure

One campaign → 5 ad sets (by debt/service type) → 14 ads (one per Google ad group).

| Ad set | Ads |
|---|---|
| Credit Card | CC_Lawsuit, CC_Harassment, CC_Negotiation |
| Lawsuit | Collection_Lawsuit, Creditor_Suing, Served_Papers |
| Harassment | Creditor_Harassment, FDCPA_Harassment, Multiple_Collectors |
| Medical Debt | Medical_Harassment, Medical_Lawsuit, Medical_Rights |
| Garnishment | Pre_Judgment_Garn, Post_Judgment_Garn |

Each ad ships with: **5 headline options** (shared campaign-wide), **5 message options** (per ad: Default, Fight, Reassurance, Affordability, Fresh Start), **3 image directions** (Illustration / Photo / Still life) × **4 ratios** (1:1, 4:5, 9:16, 1.91:1), and **1 video** (9:16). CTA is **Book Now** on every ad; destination is the ad's landing page (see below).

## ⚠️ Required campaign settings (read before uploading)

- **Special Ad Category = CREDIT.** Consumer-debt / financial-services ads must declare this at the campaign level. It restricts targeting: no gender targeting, no ZIP targeting, detailed-targeting options are limited, and geo radius has a 15-mile minimum. Build targeting accordingly.
- **Objective:** Leads (`OUTCOME_LEADS`). Conversion happens on the landing-page qualifier form, so wire the pixel/dataset + a Lead event. (Confirm with the operator if they'd rather use Meta Lead Forms instead of the site form.)
- **Geo:** United States, **exclude New York** (compliance). 
- **Texas & Florida:** these states require ad **filing** plus local-counsel-on-ad — Florida is **pre-approval before launch** ($150/ad, ~15-day review); Texas is post-filing within 10 days ($100/ad). **Do not launch TX or FL** until those filings and the state-specific ad disclosures are handled. Easiest path: exclude TX and FL at launch and add them once cleared.
- **Launch status: PAUSED.** Bring it up paused so the operator can QA before any spend.

Placeholders the operator must supply (also listed in the JSON under `placeholders_operator_must_fill`): ad account ID, Facebook Page ID, Instagram account (optional), pixel/dataset ID, budget per ad set, schedule.

## Copy

**Headlines (5, shared across all ads):** Get a Free Review of Your Case · Talk to an Attorney, Free · See If You Can Fight Back · You Don't Have to Face Them Alone · Know Your Options, Free. Default = the first.

**Messages (5 per ad):** in `copy/messages.csv` and the manifest. The **compliance footer is already appended** to every message:

> Crēdo Legal. The choice of a lawyer is an important decision and should not be based solely upon advertisements.

Note: Meta primary text can't be styled — the footer appears as a plain final line, same font/size as the message. This is the **universal compliance layer only**. State-specific disclosures (attorney name/address for KS/OH/SD/NJ/KY, jurisdiction notice, TX/FL local counsel) are **not** in the copy and must be added per the compliance matrix (separate doc) before those states run.

## Landing pages

Each ad points to the redesigned landing page whose angle matches its message. URLs are the production slugs on `start.credolegal.com` (all 14 **verified live 2026-06-29**). The `final_url_utm` field adds `utm_source=meta&utm_medium=paid_social&utm_campaign=credo_meta_v3&utm_content=<ad>` so Meta traffic is separable from Google in analytics — use the UTM version unless the operator says otherwise. Full table: `copy/ads.csv` (and `landing-page-map.md` in the campaign folder).

Note: those slugs currently serve the **existing Webflow** pages; the **redesigned** versions live on the `credo-public-harassment-lp` preview mirror and will serve at the same slugs once promoted. The `landing_page_redesign_preview` field in the manifest links each redesigned page for reference.

## Recommended test plan (full library shipped)

The full library is 5 headlines × 5 messages × (3 directions × 4 ratios + video) per ad — far too many combinations to launch at once. Suggested **staged** approach:

1. **Phase 1 — find the winning creative.** Per ad, launch the **Default** message + **Default** headline, with 4 sibling ads differing only by creative direction (Illustration / Photo / Still life / Video). Upload the 1:1 and 4:5 as the primary assets and let Meta serve the 9:16 / 1.91:1 per placement. One variable; clean read on which visual wins.
2. **Phase 2 — message angle.** On the winning creative, test the 5 message variants (Default vs Fight / Reassurance / Affordability / Fresh Start) as separate ads.
3. **Phase 3 — headline.** On the winning creative + message, rotate the 5 headlines.

Keep each ad set's ads within that ad set. Avoid pure Dynamic Creative if you want clean per-variant attribution (it blends everything). All assets and all copy are included so any combination can be built.

## Asset naming

`creatives/<Ad>/<Ad>_<direction>_<ratio>.<ext>` — e.g. `creatives/CC_Lawsuit/CC_Lawsuit_photo_9x16.png`, `creatives/CC_Lawsuit/CC_Lawsuit_video_9x16.mp4`. Directions: `illustration`, `photo`, `stilllife`, `video`. Ratios: `1x1`, `4x5`, `9x16`, `1.91x1`. (Most images are PNG; a few 4:5 masters are JPEG — extension is preserved in the filename and the manifest.)

## Open items for the operator

- Confirm Special Ad Category = Credit, objective, pixel/dataset + Lead event, budget, schedule.
- Decide TX/FL handling (recommend exclude at launch).
- Confirm UTM scheme (or request clean URLs).
- Supply state-specific compliance disclosures before KS/OH/SD/NJ/KY/TX/FL ads run.
- A few **current** live LP pages have minor copy typos (e.g. "Medial Debt," "Harasssing") and `wage-garnishment-prevention` has a prevention/exemptions subhead mismatch — for the site owner to fix; not blockers for the ad upload.
