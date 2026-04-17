# Pinho Law — Lead Flow & Qualification

The firm has **two practice areas with two very different lead flows**. Learn both before you touch either.

---

## Immigration Lead Flow

This is the more structured, paid-traffic-driven flow. It's where most of the dev work has gone so far.

```
Traffic Source        →  Intake                 →  CRM               →  Legal Team
─────────────────────────────────────────────────────────────────────────────────────
Meta Ad                  Website →                 Pipedrive            Gregori Pretto
Google Ad                  Leadster chatbot        (active deal          (Head of Immigration)
Organic / SEO              OR                      pipeline for          + Thomaz (ops)
Referral                   WhatsApp deep-link      immigration leads)    + William (paralegal)
                           button (→ 4144)
```

### Two Website Intake Paths

When a prospect lands on the Pinho Law website, they have two ways in:

1. **Leadster chatbot** — embedded on the site. Bilingual (EN/PT), 18-question conditional-logic funnel. Collects structured data up front, scores the lead, hands it downstream.
2. **WhatsApp button** — deep-links the prospect into a conversation with the firm's immigration intake WhatsApp number ending in **4144**, owned by **Thaisa** (front desk / immigration intake). Lower friction, less structured — it's a real conversation, not a form.

Both paths are legitimate and the firm wants to keep both. They serve different prospect preferences — some people want to answer a form, some people want to text. A dev change that breaks one of these paths is a revenue bug.

### What Happens After Intake

**From Leadster:** submission fires a webhook to n8n, which normalizes the data, scores the lead, and writes to Pipedrive.

**From the WhatsApp button (4144, Thaisa):** conversation lands in Thaisa's WhatsApp inbox. How that conversation becomes a Pipedrive deal is one of the things you'll need to trace end-to-end in your first week. Could be manual entry by Thaisa, could be partial automation through n8n, could be inconsistent. Find out, don't assume.

### Where Immigration Leads Live

**Pipedrive is the source of truth for immigration leads.** Every active immigration lead, every stage advancement, every note — Pipedrive. When the legal team asks "what's happening with lead X," the answer is in Pipedrive.

If you're building a dashboard view, report, or automation that touches immigration leads, Pipedrive is the system you pull from.

### The Legal Team on the Immigration Side

- **Gregori Pretto** — Head of Immigration. Strategic calls, complex case decisions.
- **Thomaz** — Immigration operations. Day-to-day case execution.
- **William** — Paralegal. Document prep, filings.
- **Dra. Izi** — stays involved in strategy and appears as face on marketing creative.

---

## Business Law Lead Flow

This flow is **fundamentally different** from immigration. Do not confuse them.

```
Source                  →  Intake                    →  Where it Lives
─────────────────────────────────────────────────────────────────────────────
Referral                   Aline's WhatsApp (0705)      WhatsApp threads
Repeat business            — business intake            (not currently in
                           owner                        Pipedrive or any
                                                        structured CRM)
```

### Key Facts

- **Business law leads are almost entirely referrals and repeat business.** There is no paid acquisition funnel for business law right now.
- Leads come in through **Aline's WhatsApp, number ending 0705** — Aline owns business intake as part of her role (Executive Assistant / Paralegal / business intake).
- They do **not** land on the firm's main immigration number (4144, Thaisa), and usually not through the website at all.
- There is **no centralized CRM for business law today.** The deal lives in the thread. This is an operational gap, not a design choice.
- The business practice is led by **Guilherme** (attorney, Head of Business department). He takes over once Aline has made the initial connection and qualified interest.

### Implication for Dev Work

When someone says "lead" at Pinho, the default assumption is immigration. If a dev project description uses "lead" without qualifying, ask which practice area — the systems, the data model, and the people involved are all different.

Any dev work that proposes to "unify the CRM" or "bring business leads into Pipedrive" is touching a real operational decision, not a cleanup. Don't start that work without explicit direction from Mike — it affects how Aline works every day, and the business practice runs on relationships that live in those threads.

---

## The Leadster Intake Form (Immigration Path)

**18 questions, bilingual (English + Portuguese), with conditional logic.**

The form branches based on visa category. EB-1 path asks different qualifying questions than EB-2 NIW path than family-based path. This is intentional — qualification criteria differ, and the legal team's conversation goes smoother when the right info is captured up front.

### Key Questions That Drive Scoring

| Question | Why it matters |
|---|---|
| Visa category of interest (EB-1 / EB-2 NIW / Family-based / Unsure) | Routes to the right qualification branch |
| Current location (US / Brazil / Other) | Affects urgency and process |
| Budget readiness | Single strongest predictor — 89% of leads say yes; the 11% who don't are almost always COLD |
| Profession / industry | Tech professionals skew HOT; other fields need more qualification |
| Timeline | "ASAP" / "within 6 months" → HOT bias; "just exploring" → COLD bias |
| Prior immigration attempts | Flags complexity |
| English proficiency | Service routing — determines if bilingual Portuguese support is needed |

## The HOT / WARM / COLD Scoring Model

Three-tier scoring, calculated in n8n before the lead reaches Pipedrive:

### HOT
- ICP bullseye (tech professional, 30–55, budget ready, short timeline)
- Clear qualifying credentials for the chosen visa category
- Action: immediate follow-up, priority assignment to Gregori's team

### WARM
- Meets most but not all HOT criteria
- Budget ready but longer timeline, or strong credentials but outside core ICP
- Action: standard nurture, consultation offered

### COLD
- No budget, no timeline, minimal qualifying credentials, or wrong fit entirely
- Action: low-touch nurture or exit

### Why This Matters For Devs

The scoring algorithm lives in an n8n workflow. If you change it, you change who Gregori's team calls first. **Do not touch scoring without talking to Mike first** — it is tuned on 12 months of live data and changes have downstream revenue impact.

---

## Known Weak Points in the Flow

Things that break, or have broken, and will break again:

1. **Google Tag disconnection in GTM** — silently killed Google Ads conversion data for ~8 months. Verify tags after any GTM change.
2. **Leadster → n8n webhook silence** — if Leadster changes their webhook format, n8n payload parsing can break silently. Leads still get collected by Leadster but never reach Pipedrive. Monitor for gaps.
3. **WhatsApp → Pipedrive handoff** — if this is manual (which it may be), it depends on whoever owns the WhatsApp inbox being consistent. Leads drop through the cracks here. Improving this without breaking Aline and Izi's workflow is a real project, not a quick fix.
4. **Pipedrive custom field references** — custom fields in API calls need the internal key, not the display label. Same class of bug as the GHL footgun.
5. **Meta Pixel deduplication** — if both the Pixel and Conversions API are firing the same event without proper `event_id`, Meta counts it twice and optimization gets confused.

---

## Conversion Events We Actually Care About

From a dev perspective, these are the events that must fire reliably for the immigration funnel:

| Event | When it fires | Why |
|---|---|---|
| `Lead` | Leadster form submitted / WhatsApp conversation initiated via the button | Meta Ads optimizes on this |
| `Qualified Lead` | n8n scores as HOT or WARM | Internal metric, not sent to ad platforms |
| `Consultation Booked` | Pipedrive deal advances to "Consultation Scheduled" stage | High-value conversion — this is what Google Ads should optimize on |
| `Contract Signed` | Pipedrive deal advances to "Retainer Paid" stage | Revenue event |

**Push back** if you ever see a proposal to "send the Lead event to Google Ads for optimization." Google Ads should optimize on **Consultation Booked** or **Contract Signed**, not raw leads. Optimizing on raw leads floods the pipeline with COLD leads and burns ad spend.

---

## A Note on Referrals

Referrals show up in both flows:

- **Immigration referrals** often still go through Leadster or WhatsApp-4144 because Dra. Izi or the team points the referrer there — that's fine, it keeps the structured path intact.
- **Business referrals** go directly to Aline or Izi's WhatsApp. Those almost never touch Pipedrive.

When you're looking at data and see a lead with no ad attribution, no Leadster source — it's probably a referral. That's not a bug.
