# Platform Architecture — Standalone Telehealth Venture

*Draft. Separate company, separate stack, separate accounts from Merit, Halo, and Conduit.*

---

## 1. The decision that governs everything else

You are **not** building an EHR, a physician network, or a pharmacy. You are building:

1. A **funnel** (brand → intake → checkout)
2. A **glue layer** (intake → clinical case → Rx → fulfillment → labs → results)
3. A **member surface** (protocol, doses, check-ins, results, reorder)

Everything clinical rides on vendors who already carry the licensure, malpractice, and Surescripts certification. That is what "legitimized" means operationally — not a badge on the footer.

A 1–2 engineer team can ship this in ~8 weeks. Every week spent building charting or e-prescribing from scratch is a week of pure loss.

---

## 2. Reference architecture

```
                  ┌─────────────────────────────────────────┐
                  │  Next.js 16 app (Vercel or Render)       │
                  │  marketing · intake quiz · checkout ·     │
                  │  member portal                            │
                  └───────────────┬─────────────────────────┘
                                  │
        ┌────────────────┬────────┴────────┬──────────────────┐
        │                │                 │                  │
  ┌─────▼──────┐  ┌──────▼───────┐  ┌──────▼──────┐  ┌────────▼───────┐
  │ Commerce DB │  │  Medplum      │  │ MD Integr.  │  │  Junction      │
  │ Postgres/   │  │  (FHIR store) │  │ or Beluga   │  │  (labs)        │
  │ Prisma      │  │  ── THE CHART │  │ ── the MDs  │  │                │
  │ NO PHI      │  │  Apache 2.0   │  │ + eRx +     │  │ Quest/Labcorp/ │
  │             │  │               │  │ pharmacy    │  │ BioReference   │
  └─────────────┘  └───────────────┘  └─────────────┘  └────────────────┘
```

**The load-bearing idea: a hard PHI boundary.** Commerce data (orders, subscriptions, affiliate attribution, marketing) lives in your own Postgres and contains no protected health information. The clinical chart — intake responses, case notes, prescriptions, results — lives in Medplum. This one decision makes your HIPAA posture defensible, keeps your BAA surface to three vendors, and means a marketing-side breach is not a reportable health breach.

---

## 3. Build vs. buy, line by line

| Layer | Decision | Choice | Why |
|---|---|---|---|
| Physician network + async visits | **Buy** | MD Integrations (primary bid) or Beluga Health (competing bid) | They carry licensure in all 50 states, malpractice, protocol review, and route to compounding partner pharmacies. Never build this. Get both to quote per-consult and negotiate. |
| E-prescribing | **Bundled** | Comes with MDI/Beluga in phase 1 | You only need your own eRx vendor if you later stand up your own medical group. Then: MDToolbox (~$30–38/prescriber/mo, published pricing, compound-capable) or DoseSpot (heavier, battle-tested compound ingredient workflow). |
| Clinical data plane | **Adopt** | Medplum (Apache 2.0, TypeScript, FHIR R4) | Patients, Questionnaire/QuestionnaireResponse intake, Tasks for case status, audit log, access policies, provider admin UI — all out of the box. Hosted tier first, self-host later. github.com/medplum/medplum |
| Labs | **Buy** | Junction (formerly Vital) | One API across Quest, Labcorp, BioReference plus at-home kits; solves the NY/NJ compliance problem. docs.junction.com |
| Sync video (only if needed) | **Buy** | Daily ($500/mo HIPAA tier) or Zoom Video SDK | Start async-only. Add sync only when a state requirement or a conversion test demands it. |
| Intake quiz | **Build** | Native Next.js, answers posted into Medplum `QuestionnaireResponse` | The quiz is the conversion funnel *and* the clinical input. It is ~1 week of work and must not be outsourced to a form vendor's UI. |
| Payments | **Open** | See §6 — this is a real decision, not a default |
| Email/SMS | **Buy** | Resend + a transactional SMS provider under BAA | Keep clinical notifications separate from marketing sends. |

### Do NOT fork Ottehr

`masslight/ottehr` is the most feature-complete open telehealth product (patient portal + provider EHR + video + eRx + charting), and it is tempting. Two blockers:

- **The license is not open source.** It is a custom MassLight license carrying a non-compete clause, a mandatory-attribution clause, an AI-training ban, and a source-transparency obligation for commercial users. A venture in this category needs that read by counsel before a single line is copied.
- **It hard-depends on Oystehr**, MassLight's proprietary metered backend. "Open source frontend, vendor-locked backend."

Read it as reference architecture for visit-flow and charting UX. Do not build on it.

Similar verdicts: **HAPI FHIR** is solid but Java and data-layer-only — Medplum dominates it for a TypeScript team. **OpenEMR** (v8.0.0, ONC-certified, March 2026) is mature but a PHP/MySQL monolith you would run beside your product, not inside it; only worth it if you need ONC certification cheaply, which you do not.

---

## 4. What carries over from Conduit — and what must not

The Conduit member portal (`~/Desktop/Conduit/Conduit Archive/conduit-app`, Next.js 16 + Prisma + NextAuth v5 on Render) already solved this problem once. Reuse the *thinking*, re-implement the *code*, share *nothing*.

**Patterns worth carrying (re-implement, don't copy):**

- The clinical data model shape. Conduit's schema already has `Provider`, `Pharmacy`, `CareCase`, `CareMessage`, `Protocol`, `Peptide`, `Vial`, `Dose`, `CheckIn`, `Snapshot` — that is the right decomposition of this problem, learned the hard way. In the new venture most of it becomes FHIR resources in Medplum instead of Prisma models, but the domain boundaries hold.
- Three role-scoped route groups: `(provider)`, `(pharmacy)`, `(admin)`. A provider queue, a pharmacy fulfillment queue, and an ops console are non-negotiable and easy to underestimate.
- `lib/consent-documents.ts` and `lib/clinical-protocols.ts` — the pattern of versioned, code-resident consent and protocol definitions, so every case records exactly which document version the patient agreed to. Carry the pattern; the content must be redrafted for the new entity.
- Magic-link auth (NextAuth v5 + Resend) rather than passwords.

**Hard separations — treat as rules, not preferences:**

- Separate GitHub repo, separate Render/Vercel account, separate database, separate domain, separate Meta/Google ad accounts, separate PayPal/Stripe entity, separate Medplum project.
- No shared pixel, no shared sending domain, no shared payment processor account. The whole point of a standalone venture is that a problem in one brand cannot reach the others.
- Zero copied brand assets, copy, or visual system. Shared *infrastructure patterns* are fine; shared *brand DNA* defeats the purpose.

---

## 5. Compounding pharmacy routing — the part people get wrong

Compounded prescriptions travel to the compounder's NCPDP ID over Surescripts as free-text compound orders. Any Surescripts-certified vendor can transmit them; the constraint is whether your specific compounding pharmacy is reachable on the network and willing to accept your volume.

Practical sequence:
1. Sign the compounding pharmacy **first**, before you pick a telehealth vendor. Get their NCPDP ID, their state licensure map, and their formulary in writing.
2. Ask MDI and Beluga, in the first sales call, whether their physicians can prescribe to *that specific pharmacy* — not whether they "support compounding" generally.
3. Confirm which peptides that pharmacy will actually compound as of the current FDA posture, and get it re-confirmed quarterly. This list moves.

Photon Health has by far the best developer experience in eRx (GraphQL API, prebuilt Elements components, real sandbox), but its compounding support is a sales conversation rather than a documented feature. Note for the record: Photon's "peptide eRx" and "GLP-1 bioreactor" blog posts are April Fools jokes and should never be cited as capability.

---

## 6. Open decisions that need Parker

1. **Payments.** Merit is PayPal-only for hard reasons. This venture is a *legitimate physician-prescribed telehealth business*, which is a materially different risk profile — Stripe is generally available to it, and subscription billing plus BAA-covered infrastructure is far easier on Stripe. Confirm whether the PayPal-only constraint travels to this entity or stops at Merit.
2. **Own medical group vs. vendor network.** Phase 1 should use MDI/Beluga's physicians. Standing up your own PC/MSO structure gives better margins and control but adds legal cost and a compliance surface. Decide when, not whether.
3. **Labs required or optional.** Requiring bloodwork raises cost and friction but is the single strongest legitimacy signal and the foundation of any data moat. This is a positioning decision as much as a clinical one.
4. **LegitScript certification.** Required for Google Ads on this category. It needs a named physician entity, a named pharmacy, and a street address — the same blockers on file for Conduit's application. Start it early; it gates paid acquisition.

---

## 7. Build sequence

| Phase | Work | Duration |
|---|---|---|
| 0 | Entity, pharmacy contract, telehealth vendor selection, domain + accounts | 2–3 wks (mostly non-engineering, run in parallel) |
| 1 | Medplum project + intake quiz + case creation glue | 2 wks |
| 2 | Checkout + subscription + order → Rx → fulfillment webhooks | 2 wks |
| 3 | Member portal: protocol, doses, check-ins, reorder | 2 wks |
| 4 | Provider queue + pharmacy queue + admin console | 1–2 wks |
| 5 | Labs (Junction) + results display | 1 wk |

Roughly a two-month engineering build. The critical path is phase 0 — contracts, not code.
