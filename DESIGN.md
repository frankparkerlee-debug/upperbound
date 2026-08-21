# Design System — Upperbound

Version-controlled source of truth for upperboundrx.com, the patient portal, and
every AI- or human-generated asset. If an output disagrees with this file, the
output is wrong. Reference implementation: `mockup.html` / `index.html`.

## 1. Brand Overview

Upperbound is a human performance clinic, online — physician-guided pathways to
more capacity in ordinary life (the gym, the road, 9pm). Warm, approachable
pharmacy-wellness in the Hims genre, distinguished by radical honesty devices
(no prepay, checkpoints, refund-if-declined) and the Lifeline visual motif: the
brand's rising line, present as the wordmark overbar and as a light ribbon in
photography. Audience: one voice for both the lifter and the suburban mom.

## 2. Visual Identity

### Color

| Token | Hex | RGB | HSL | Role |
|---|---|---|---|---|
| Oat | `#F7F2E9` | 247,242,233 | 39,47%,94% | Page background |
| Paper | `#FFFDF8` | 255,253,248 | 43,100%,99% | Cards, panels, quiz box |
| Sand | `#EFE8D9` | 239,232,217 | 41,41%,89% | Footer, secondary fields |
| Ink | `#26211A` | 38,33,26 | 35,19%,13% | Text, primary CTAs, manifesto band |
| Muted | `#7B7365` | 123,115,101 | 38,10%,44% | Secondary text |
| Line | `#E6DECC` | 230,222,204 | 42,34%,85% | Hairlines (solid) |
| Dot | `#D0C6B1` | 208,198,177 | 41,25%,75% | Dotted rules |
| Link Indigo | `#4338B8` | 67,56,184 | 245,53%,47% | Links, selection, overbar, ✓ marks — never button fills |
| Sage | `#DCE5D2` | 220,229,210 | 88,27%,86% | Recovery field, promise band |
| Sky | `#D8E4EF` | 216,228,239 | 209,42%,89% | Output field, hero media card |
| Butter | `#F4E3B0` | 244,227,176 | 45,76%,82% | Composition field, finder band |
| Blush | `#EFDBCF` | 239,219,207 | 23,50%,87% | Vitality field |
| Banner | `#F4D670` | 244,214,112 | 46,86%,70% | Symptom banner only |

Rules: max one action color (Ink). Indigo is an *accent*, capped at links,
selection states, checkmarks, and the wordmark overbar. No gradients on text.
No pure black. No neon, no glow shadows — shadows are warm-tinted
`rgba(64,52,30, …)` only.

```css
:root{
  --oat:#F7F2E9; --paper:#FFFDF8; --sand:#EFE8D9; --ink:#26211A; --muted:#7B7365;
  --line:#E6DECC; --dot:#D0C6B1; --link:#4338B8;
  --sage:#DCE5D2; --sky:#D8E4EF; --butter:#F4E3B0; --blush:#EFDBCF; --banner:#F4D670;
  --serif:"Hanken Grotesk",-apple-system,sans-serif; /* alias kept for legacy rules */
  --sans:"Hanken Grotesk",-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif;
  --mono:ui-monospace,"SF Mono",Menlo,Consolas,monospace;
}
```

### Typography — sans only (decided 2026-08-07; do not reintroduce serif)

Family: **Satoshi** (Fontshare) — chosen from the taste-standard list (Geist /
Cabinet Grotesk / Satoshi); Hanken and Inter are banned. Mono: system stack.
Display and manifesto set in Satoshi **Black (900)**; headings 700; body 400–500.

| Level | Size | Weight | Tracking | Use |
|---|---|---|---|---|
| Display | clamp(42px, 5.3vw, 80px) | 700 | −0.03em | Hero h1 |
| Manifesto | clamp(34px, 5.4vw, 76px) | 800 | −0.03em | Statement beats |
| H2 | clamp(30px, 3.8vw, 52px) | 700 | −0.02em | Section titles |
| Tile/H3 | 20–25px | 650 | −0.015em | Cards, steps |
| Body | 15–17px | 400–500 | 0 | Copy; 65ch max |
| Kicker/Mono | 9.5–12px | 500 | +0.12–0.2em, uppercase | Eyebrows, labels, ticks |

Keyword device: the emphasized word ("variable.") sets italic 700 in Link
Indigo with the overbar (`::before` rule above the word) — the logo device
applied to type. Wordmark: lowercase `upperbound`, sans 700, indigo overbar.

### Spacing, radius, shadow

- Section rhythm: `clamp(56px, 9vh, 104px)`; bands padded `clamp(34px,4.5vw,60px)`.
- Radius scale: pills `999px` · bands `28px` · tiles `24px` · panels/cards `18–22px` · image windows `12–18px`. Never a single uniform radius everywhere.
- Shadows (warm): card `0 2px 10px rgba(64,52,30,.05)` · hover `0 18px 40px rgba(64,52,30,.13)` · floating `0 18px 60px rgba(38,33,26,.22)`.

## 3. Signature Components

- **Symptom banner** (butter): one recognition line + underlined CTA. Top of page, always.
- **Pill buttons**: ink fill, oat text, trailing `→` that slides on hover, −1px hover lift, `scale(.985)` press. One primary CTA per view; secondary is an underlined quiet link — never two pills.
- **Pathway shelf (bento)**: asymmetric spans per magic-ui bento pattern — Recovery 2×2 with media fill, Output 2×1 wide, Composition/Vitality 1×1 text-only on their field colors. Never equal tiles on desktop. Quiz selection outlines the picked tile in indigo.
- **✓-chips**: white pills with indigo checks, floating on the hero media card with perpetual 5.5s float loops (staggered).
- **Finder**: white paper card on butter band; 3 questions; result deep-links `/start?pathway=…`.
- **Scroll-film interlude**: full-bleed photograph + one short line, between card sections. At least one per page; this is what keeps the page from reading hero-grids-footer.
- **Manifesto beat**: ink band, monumental 800 type, one sentence. Maximum one per page.
- **Panels**: white cards for the interactive instruments (day curve, 12-week arc) with mono headers and dotted rules.
- **Entrance choreography**: headline words blur-in sequentially → media card fades up. All motion behind `prefers-reduced-motion`.

## 4. Motion

- Reveal: opacity+translateY, `.7s cubic-bezier(.2,.8,.2,1)`, staggered `--i * 90–110ms` cascades. Never mount lists instantly.
- Perpetual micro-loops on hero chips only (restraint beats saturation).
- Images: slow 4–30s scale drifts on hover/ambient. Animate transform/opacity only.
- Everything disabled under `prefers-reduced-motion`.

## 5. Imagery

- **The Lifeline**: warm, lived-in spaces with a single electric indigo light
  ribbon (long-exposure style). Current set is AI-generated art direction —
  replace with commissioned/licensed shots on the same brief.
- Real humans only via **licensed stock or commissioned shoots** (Adobe Stock).
  **AI-generated people/faces are banned.** Faceless silhouettes acceptable.
- Product imagery: welcome-kit packaging on soft fields. **Never medication
  vials, pens, pills, or dosing paraphernalia on marketing surfaces.**

## 6. Voice

Attributes (testable):
1. **Direct** — "Cancel in two clicks," not "flexible cancellation options."
2. **Honest to a fault** — name the trade ("Not approved? Full refund."), disclaim illustrations ("not a measured outcome or a promise").
3. **Life-performance framed** — gym, road, 9pm, toddlers, trailheads; never boardroom or bro-optimization.
4. **Plain-spoken clinical** — "a physician reads every intake," not "AI-powered care orchestration."
5. **Warm, not soft** — sentence case, short sentences, no exclamation points.

Banned vocabulary: *optimize/optimization, longevity, healthspan, biohack,
unlock, elevate, seamless, revolutionary, best-in-class, journey (as filler),
game-changer* — plus **all molecule/compound names, "peptide," "vial," "dose,"
"compounded," and any outcome claim or invented statistic** on marketing
surfaces (LegitScript posture; see STRATEGY.md).

## 7. Guardrails

**Agents may generate freely:** layout/copy iterations within this system;
social/email drafts in voice; Lifeline-brief object imagery (no people).

**Human review required:** pricing, guarantees/refund language, member
stories (must be real + permissioned at launch), certification references,
anything naming the pharmacy or physicians.

**Never:** medication names or imagery on marketing; outcome/marker claims;
AI-generated faces; fabricated metrics or testimonials presented as real;
prepay lock-in offers; serif typefaces; neon glows; centered heroes; equal
uncolored card grids; Inter or Hanken Grotesk; **em dashes in any copy** (the
definitive AI-writing tell — use commas, colons, or periods; " · " for label
separators); fake stats or invented metrics.

## 8. Agent Usage

Load this file before generating anything visual or written for Upperbound.
Copy the `:root` block verbatim. When this file and an older mockup conflict,
this file wins. Update this file in the same commit as any deliberate system
change — the diff is the design decision log.
