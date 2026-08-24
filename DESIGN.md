# Design System — Upperbound (v31 · "The Blend")

Version-controlled source of truth for upperboundrx.com, the patient portal, and
every AI- or human-generated asset. If an output disagrees with this file, the
output is wrong. Reference implementation: `index.html` (= `mockup.html`).
Locked 2026-08-23 after four direction studies (`directions.html`, Direction D).

## 1. Brand Overview

Upperbound is a human performance clinic, online: physician-guided 12-week
pathways to more capacity in ordinary life (the gym, the road, 9pm). The system
blends three mediums, each doing what it's best at: film for the life,
photography for the in-between, and plain typography for the honesty facts.
Warm editorial ground, racing green and sienna, timestamps as the narrative
device. Audience: one voice for both the lifter and the suburban mom.

## 2. Visual Identity

### Color

| Token | Hex | Role |
|---|---|---|
| Bone | `#FBFAF7` | Page background |
| Paper | `#FFFDF6` | Cards, finder, terms |
| Cream | `#F6EFDD` | Footer, Composition tile, typographic tiles |
| Ink | `#15140F` | Text, manifesto band, media tile grounds |
| Body | `#4A473F` | Body copy |
| Muted | `#6E6A60` | Secondary text, small labels |
| Line | `#E3DCCB` | Hairlines, card borders |
| Racing Green | `#1F3D2B` | Primary CTAs, featured tile, finder band, banner |
| Green Hover | `#2A5039` | CTA hover only |
| Sienna | `#B34725` | Keyword accent, kickers, overbar, links-as-actions |
| Amber | `#E8B44F` | Small accents: bullets, tags, accents on green |
| Blush tint | `#F4E4DB` | Reserve tile tint (unused since 3-pathway cut) |

Rules: green fills buttons; sienna never fills buttons (it's the accent and
keyword color); amber is a garnish, never a ground for text blocks. No pure
black, no neon, no glow shadows. Shadows warm-tinted only.

```css
:root{
  --bone:#FBFAF7; --paper:#FFFDF6; --cream:#F6EFDD; --ink:#15140F;
  --body:#4A473F; --mut:#6E6A60; --line:#E3DCCB;
  --green:#1F3D2B; --green2:#2A5039; --sienna:#B34725; --amber:#E8B44F;
  --disp:"Cabinet Grotesk",-apple-system,BlinkMacSystemFont,sans-serif;
}
/* load:
<link href="https://api.fontshare.com/v2/css?f[]=cabinet-grotesk@400,500,700,800&display=swap" rel="stylesheet">
*/
```

### Typography — sans only (do not reintroduce serif)

One family: **Cabinet Grotesk** (Fontshare), for everything including small
text. Geist Mono was retired 2026-08-24: uppercase, letter-spaced mono
micro-labels read as robot voice (an AI tell). Small text is 12.5 to 14px
Cabinet Grotesk, sentence case, normal tracking, weight 600 to 700. Inter,
Hanken Grotesk, and Satoshi are not used.

| Level | Size | Weight | Tracking |
|---|---|---|---|
| H1 masthead | clamp(44px, 6.4vw, 104px) | 800 | −.035em |
| H2 | clamp(30px, 4vw, 54px) | 800 | −.025em |
| Tile/H3 | 21–26px | 800 | −.015em |
| Body | 14.5–16.5px | 400–600 | 0 |
| Small labels | 12.5–14px | 600–700 | 0, sentence case |

Keyword device: the emphasized word ("variable.") sets in Sienna, roman (no
italic). Ghost footer wordmark at 6% ink.

### Logo (LOCKED 2026-08-24 · Direction A "The Overbar")

Lowercase `upperbound` in Cabinet Grotesk 800 with a sienna bar above the
"up". Geometry is measured from glyph ink, not eyeballed: the bar is 1em wide,
0.15em tall, sits 0.15em above the x-height, left-aligned to the u's ink edge.
The standalone mark is bar + u as one centered composition (bar 6/7 of u ink
width, centered over it). App icon: green rounded square, amber bar, bone u.
Final vector assets (real font outlines, no font dependency): `/brand/
lockup.svg`, `lockup-on-dark.svg`, `mark.svg`, `mark-on-dark.svg`, `icon.svg`;
favicon at `/favicon.svg` and portal `src/app/icon.svg`. Decision record:
`/logos.html`. Do not redraw by hand; regenerate from font outlines.

## 3. Signature Components

- **Masthead hero**: H1 left, quiet issue-line right, over the media collage.
- **Media collage**: 12-col grid; film panel spans 7×2, two photo stills span
  5 each. Every media tile carries a **timestamp caption** (sentence-case
  sans on a blurred smoke chip): `6:40 am · The long way in`. Twelve-hour,
  human time, never military. Timestamps are the brand's storytelling
  device: the page is one day in a member's life.
- **Fact chips**: the honesty facts as plain sentence-case pills on paper
  (`Physician review in 24 hr · Renews on autopilot: never · Not approved: $0`).
  NEVER render these as charts, dashboards, or invented visualizations —
  data-instrument styling was explicitly rejected 2026-08-21.
- **Pathway shelf**: three tiles: Weight Loss featured on green with amber
  "Most popular" tag, Recovery on paper, Output on cream. Dashed-rule bullet
  lists with amber dots; each tile carries a one-line therapy sentence
  ("Built on GLP-1 class therapy, prescribed only if it's right for you.").
  Finder result outlines the picked tile sienna.
- **Interlude**: full-bleed photograph + timestamp + one short line
  ("Saturday is the point."). At least one per page.
- **Finder**: paper card on the green band; 3 questions; result deep-links
  `https://app.upperboundrx.com/start?pathway=…` (real URLs, never anchors).
- **Day strip**: 3-up mix of video tiles and one cream typographic tile,
  all timestamped.
- **Manifesto beat**: ink band, one sentence, max one per page.
- **Terms card**: paper card, label/value rows, sienna for the emphasized
  clause. Plain type only.
- **Pills**: green fill, −1px hover lift, scale(.985) press. One primary CTA
  per view; secondary is an underlined quiet link.

## 4. Motion

- Reveals: opacity+translateY .7s, staggered `--i * 90ms`.
- Videos: `autoplay muted loop playsinline` + IntersectionObserver play/pause
  + one-time touch/click nudge. Compose every video tile to also work as a
  photograph (iOS Low Power Mode shows the first frame).
- Stills: slow 26s scale drift.
- Everything behind `prefers-reduced-motion`.

## 5. Imagery

- **Real photography and film only. AI-generated imagery is retired from all
  marketing surfaces** (completed 2026-08-23). Current assets are free-license
  Pexels placeholders in `img/stock/` and `video/`; replace with a licensed
  Adobe Stock curation on one consistent warm grade.
- Brief: warm domestic and athletic life, real bodies of different builds,
  faces incidental or turned away. Dawn kitchens, garage gyms, trails,
  9pm living rooms. No race bibs, no visible brand logos, no gym-chain gloss.
- Never: medication, pens, pills, dosing paraphernalia, AI faces, sterile
  clinical settings.

## 6. Voice

1. **Direct**: "Cancel in two clicks," not "flexible cancellation options."
2. **Honest to a fault**: name the trade; disclaim illustrations; the
   physician's "no" is part of the brand.
3. **Life-performance framed**: gym, road, 9pm, toddlers, trailheads. Never
   boardroom, never bro-optimization.
4. **Plain-spoken clinical**: "a physician reads every intake."
5. **Warm, not soft**: sentence case, short sentences, no exclamation points.

Copy is written for the customer. Design commentary ("film for the life…")
never appears on the page as body copy (violation caught 2026-08-23).

Banned vocabulary: *optimize/optimization, longevity, healthspan, biohack,
unlock, elevate, seamless, revolutionary, best-in-class, journey (filler),
game-changer*. Therapy naming (2026-08-23, licensed-telehealth posture):
**allowed** are "GLP-1 class," "NAD+," and "sermorelin," stated factually with
"prescribed only if it's right for you." **Still banned:** brand or generic
drug names beyond those three (never semaglutide/tirzepatide), "peptide,"
"vial," "compounded," all dosing content, and any outcome claim, marker
claim, or invented statistic (no-testing model: never claim a marker moved).

## 7. Guardrails

**Agents may generate freely:** layout/copy iterations inside this system;
email/social drafts in voice; typographic tiles.

**Human review required:** pricing, guarantees/refund language, member
stories (real + permissioned at launch; placeholders must carry the
"illustrative composites" footnote), pharmacy or physician references.

**Never:** medication names or imagery on marketing; outcome/marker claims;
AI faces; fabricated metrics or testimonials presented as real; prepay
lock-in offers; serif typefaces; Inter or Hanken Grotesk; neon glows; pure
black; centered heroes; equal uncolored card grids; data-instrument widgets
for brand facts; **uppercase letter-spaced mono micro-labels and 24-hour
timestamps** (robot voice); **em dashes in any user-facing copy** (use commas, colons,
periods; " · " in labels); marketing CTAs that point at page anchors instead
of real app URLs.

## 8. Agent Usage

Load this file before generating anything visual or written for Upperbound.
Copy the `:root` block verbatim. Every deploy: scan for em dashes and banned
vocabulary (target: zero), verify CTAs resolve to `app.upperboundrx.com`.
When this file and an older mockup conflict, this file wins. Update this file
in the same commit as any deliberate system change.
