# Pinho Law — Tech Stack Map

## The One-Page View

```
┌─────────────────────────────────────────────────────────────────┐
│                        MARKETING LAYER                          │
│  Meta Ads  •  Google Ads  •  GTM  •  Organic / Referral         │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                        INTAKE LAYER                             │
│  Leadster (18-question bilingual form, conditional logic)       │
│  WhatsApp (5 numbers, each with a specific owner/purpose —      │
│  4144=Thaisa immigration intake, 0705=Aline business intake,    │
│  see Glossary for full table)                                   │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     AUTOMATION LAYER                            │
│  Pipedrive (primary, active)  •  GoHighLevel (GHL, uncertain)   │
│  n8n workflows orchestrate between them                         │
│  ⚠  GHL status is an open question — see "Open Questions"       │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                       DATA LAYER                                │
│  Pipedrive = current source of truth for dashboard data         │
│  Supabase = possible future data layer (not yet decided)        │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                   PRESENTATION LAYER                            │
│  pinho-law-dashboard.vercel.app                                 │
│  Next.js + React + TypeScript + Tailwind                        │
│  Deployed on Vercel (behind deployment protection)              │
└─────────────────────────────────────────────────────────────────┘
```

## Who Owns What

| System | Source of Truth For | Do NOT use it for |
|---|---|---|
| **Leadster** | Raw intake form submissions | Long-term lead storage |
| **WhatsApp (5 numbers, per-owner)** | Direct inbound conversations — 4144/Thaisa for immigration, 0705/Aline for business, plus 7411/Mike, 8705/William, 2726/Clauderlane for internal/case/finance. See Glossary for the full table. | Structured lead data — messages land in a specific person's inbox, not automatically in the CRM |
| **Pipedrive** | Active lead pipeline, deal stages, the record the team actually works | Don't assume — it's the current primary CRM |
| **GoHighLevel (GHL)** | Historical context only (for now) | Do NOT build new workflows on GHL until the audit is done |
| **n8n** | Workflow orchestration between Leadster, WhatsApp, Pipedrive, GHL | Business logic that needs version control and tests |
| **Supabase** | Possibly future structured data layer | Nothing production-critical yet — not confirmed as part of the stack |
| **Vercel** | Dashboard hosting + deployment protection | Secrets management (use env vars, not code) |
| **Meta / Google Ads** | Paid traffic | Lead storage (they push to Leadster → n8n) |
| **GTM** | Firing conversion tags | Business logic |

## The Tech Stack in Detail

### Frontend
- **Next.js** (App Router)
- **React** with **TypeScript**
- **Tailwind** for styling
- Component library: standard shadcn/ui patterns
- *(Confirm exact Next.js version and component library with Mike — assume these are approximately right, not gospel)*

### Backend
- **Vercel** — hosting, serverless functions, deployment protection
- **Supabase** — possibly in use for dashboard data, possibly not yet. One of your first tasks: confirm with Mike what the dashboard actually queries today. If Supabase is in the stack, treat it as you would any Postgres + RLS setup: assume the database will leak if RLS is wrong.

### Automation / CRM
- **Pipedrive** — primary CRM, active, where the team actually works leads and deals today
- **GoHighLevel (GHL)** — historically in the stack, currently uncertain. Needs an audit to determine if it stays or goes (see Open Questions below).
- **n8n cloud** (`jde.app.n8n.cloud`) — orchestrates data between Leadster, WhatsApp, Pipedrive, and GHL. (The `jde` in the URL is a legacy naming artifact; it's still the Pinho Law n8n instance.)
- **Leadster** — bilingual intake form provider
- **WhatsApp (5 numbers)** — direct inbound channel split across team members by function (4144=Thaisa/immigration intake, 0705=Aline/business intake, 7411=Mike/ops, 8705=William/paralegal, 2726=Clauderlane/finance). See Glossary for the full table.

### Marketing
- **Meta Ads Manager** — Dra. Izi-as-face creative targeting immigration ICP (and, soon, business law ICP)
- **Google Ads** — intent-based; conversion tracking was recently restored after ~8 months of zero-conversion data (Google Tag was disconnected in GTM)
- **GTM (Google Tag Manager)** — tag orchestration; the single most-commonly-broken piece of the stack
- **Start Marketing** (agency) builds and deploys creative; **Joao** manages paid ads

## The Data Flow in Plain English

1. A lead either:
   - Clicks an ad / lands on the site and fills out the **Leadster form** (18 questions, EN or PT, conditional logic), **or**
   - Messages the firm's immigration intake WhatsApp at **4144 (Thaisa)** directly — typically via the website's WhatsApp button or an ad CTA.
   - (Business law referrals follow a different path — those go to **0705 (Aline)**, not through the immigration funnel. See Lead Flow doc for the full split.)
2. If Leadster: on submit, Leadster fires a webhook to **n8n**. n8n normalizes the data, calculates lead score (HOT/WARM/COLD), and passes the contact into the CRM layer.
3. If WhatsApp: the conversation lands in the WhatsApp inbox. Whether it flows into Pipedrive automatically, via n8n, or is logged manually is part of what you'll need to learn and possibly improve.
4. The lead is worked in **Pipedrive** — this is where the team actually lives day-to-day. Deal stages, notes, activities, outcomes.
5. **GHL does not currently trigger any workflows.** It exists in the stack but it is not actively doing work. Whether we keep it, audit and fix it, or sunset it is an open question. **Do not build new things on GHL until Mike gives the go-ahead.**
6. The dashboard (`pinho-law-dashboard.vercel.app`) reads from Pipedrive (primary) and possibly other sources. Confirm with Mike what it actually connects to before assuming.
7. Case-level structured data (deliverables, filing dates, USCIS milestones) — storage is still being figured out. It may end up in Supabase, it may stay in Pipedrive custom fields, it may be a new system entirely. This is a decision, not a given.

## Open Questions (Active, Not Resolved)

These are not things you should "just figure out and fix." They are in-flight decisions that Mike is working through. **Surface what you find and propose options — do not unilaterally pick one.**

1. **GHL — keep, audit and fix, or sunset?** Having Pipedrive *and* GHL may be overkill. Any new dev work that would expand GHL usage needs to wait for this decision. If you're building something and you feel the pull to use GHL, stop and ask.
2. **Supabase — in or out?** The dashboard may or may not use Supabase today. If it doesn't, adding it is a real decision with trade-offs — don't just install it because it's familiar. If it does, understand what tables exist and what RLS looks like before touching them.
3. **Structured case data — where does it live?** Not decided.
4. **WhatsApp → CRM pipeline — what exists, what's missing?** Part of the first-week exploration is tracing this end-to-end and reporting back.

## GHL Custom Field Gotcha (Historical — Still Worth Knowing)

When GHL *was* doing more work, the firm hit this footgun and it cost time: when creating/updating contacts via the GHL API, **you must use the internal field ID, not the template variable key.**

- ❌ `{{contact.eb_category}}` — template syntax, does NOT work in API payloads
- ✅ `custom_field_id: "abc123xyz"` — actual internal ID, works

If GHL is kept in the stack and you end up writing to it, this is the first thing to check when "the workflow ran but the field is empty."

Pipedrive has its own version of this — custom fields there are also referenced by an internal key, not the display label. Different system, same class of bug.

## Conversion Tracking Chain (Fragile)

```
Ad click → Landing page → Leadster form submit → n8n webhook → Pipedrive deal created
    │
    ├─→ Meta Pixel fires (Lead event)
    ├─→ Google Ads conversion fires (via GTM)
    └─→ GA4 event fires
```

If any one of these tags silently breaks — as happened with Google Ads for ~8 months — you lose attribution and optimization data. Always verify conversion tracking end-to-end after any GTM change. Use GTM Preview Mode and Tag Assistant before publishing.

## What "Production" Means Here

- **Production dashboard** is behind Vercel deployment protection. You cannot share a URL publicly.
- **Staging/preview** deployments are automatic on branch push — use them for review
- **Database migrations** (if/when Supabase is confirmed) go through migration files, not ad-hoc SQL
- **Secrets** live in Vercel environment variables and in n8n credentials — never in code, never in committed `.env` files
