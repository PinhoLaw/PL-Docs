# Pinho Law — Glossary

Terms you will hear in meetings, docs, Slack, Pipedrive, and the codebase. If someone uses one you don't know, look here first.

## Immigration / Legal

| Term | Meaning |
|---|---|
| **EB-1** | Employment-Based First Preference green card. "Extraordinary Ability" category. Applicant self-petitions. |
| **EB-2 NIW** | Employment-Based Second Preference, National Interest Waiver. Applicant self-petitions by arguing their work serves the U.S. national interest. A core offering in Pinho's immigration practice. |
| **I-140** | The petition form filed for EB-1 and EB-2 NIW |
| **Adjustment of Status (AOS)** | Applying for the green card while inside the U.S. |
| **Consular Processing** | Applying for the green card from outside the U.S. via a U.S. consulate |
| **USCIS** | U.S. Citizenship and Immigration Services — the agency we file with |
| **RFE** | Request for Evidence — USCIS asks for more documentation before deciding |
| **Priority Date** | Effectively your place in line for a green card number |
| **Dra.** | Portuguese honorific for "Doctor" — used for attorneys (Dra. Izi = Dr. Izi) |
| **Três prongs** / **three prongs** | The three-part test for EB-2 NIW: (1) substantial merit and national importance, (2) well-positioned to advance the endeavor, (3) on balance beneficial to waive the labor cert |
| **Family-based petition** | Green card petition filed by a U.S. citizen or permanent resident on behalf of a family member (spouse, parent, child, sibling, depending on category). The other half of Pinho's immigration practice. |

## Marketing / Sales

| Term | Meaning |
|---|---|
| **ICP** | Ideal Customer Profile — two distinct ICPs at Pinho. **Immigration:** tech professionals, 30–55, Brazilian-heavy, budget ready. **Business Law:** Brazilian business owners living in Florida, or any Brazilian in Florida with a business service need (B2B or B2C). |
| **Leadster** | The form-builder platform used for the 18-question bilingual intake form |
| **HOT / WARM / COLD** | The three-tier lead scoring used in n8n to prioritize legal team effort |
| **Pipedrive** | The CRM of record for immigration leads. Source of truth for active deals, stage progression, and team activity. |
| **GHL** | GoHighLevel — historically part of the stack. Currently not triggering workflows. Status is an open question (keep, audit and fix, or sunset). Do not build new workflows on GHL until Mike gives the go-ahead. |
| **Pipeline stage** | Where a lead sits in the Pipedrive workflow (e.g., "New Lead," "Consultation Scheduled," "Retainer Paid") |
| **Conversion event** | A user action we track in Meta / Google Ads to optimize on (Lead, Consultation Booked, Contract Signed) |
| **Retainer** | Initial legal fee payment that starts case work |
| **Lead magnet** | A free resource offered in exchange for contact info |
| **Hormozi framework** | Mike applies `$100M Offers` and `$100M Leads` frameworks. The "value equation" (dream outcome ÷ perceived likelihood × time delay × effort) comes up often in ad copy and offer structuring |

## Technical

| Term | Meaning |
|---|---|
| **n8n** | Workflow automation platform (like Zapier but self-hostable and more powerful). Hosted at `jde.app.n8n.cloud`. |
| **GTM** | Google Tag Manager — fires analytics / ad tags based on rules |
| **GA4** | Google Analytics 4 |
| **Meta Pixel** | Meta's tracking script for web events |
| **Conversions API (CAPI)** | Server-side event sending to Meta (more reliable than the Pixel alone) |
| **RLS** | Row-Level Security — Postgres/Supabase feature that filters which rows a user can see at the database level |
| **Deployment protection** | Vercel feature that requires authentication to access a deployment URL |
| **Preview deployment** | Vercel auto-generates a unique URL for each git branch / PR |
| **Supabase migrations** | Versioned SQL files that describe DB schema changes |
| **Custom field ID** | The actual internal ID of a custom field in a CRM — needed for API calls. In both Pipedrive and GHL, template syntax like `{{contact.field}}` does NOT work in API payloads. You must reference the internal field key/ID. |

## Firm-Specific

| Term | Meaning |
|---|---|
| **Dra. Izi** | Izi Pinho, lead attorney and firm owner. Face of marketing creative. |
| **The firm WhatsApp numbers** | Pinho runs five WhatsApp numbers, each owned by a specific team member. See the WhatsApp Numbers section below for the full table — get this right, it affects how you route leads and who gets notified. |
| **The dashboard** | Usually means `pinho-law-dashboard.vercel.app` in Pinho conversations |
| **The Sheet** | A Google Sheet that n8n writes to as an audit/backup layer for Leadster submissions |

## WhatsApp Numbers

Pinho Law operates across five WhatsApp numbers. Each has a specific owner and a specific purpose. When someone says "4144" or "0705" in conversation, they're using the last four digits as shorthand for the number and its owner.

| Last 4 digits | Owner | Role | Typical use |
|---|---|---|---|
| **4144** | **Thaisa** | Front desk / immigration intake | Primary inbound immigration number — web button and ad CTAs deep-link here |
| **7411** | **Mike Stopperich** | Operations | Internal ops, vendor coordination, dev/marketing work |
| **8705** | **William** | Paralegal | Case-level document coordination with immigration clients |
| **0705** | **Aline** | Executive Assistant / Paralegal / business intake | Business law leads (referrals and repeat business), plus executive support for Dra. Izi |
| **2726** | **Clauderlane** | Head of Finance (Recife, Brazil) | Billing, retainers, payment coordination |

### Implications for Devs

- **"WhatsApp" is not one inbox.** When you build or debug anything that touches WhatsApp, always ask *which number*. The routing matters — a dev change that dumps all messages into one inbox is a real operational bug.
- **Immigration lead inbound = 4144 (Thaisa).** The website WhatsApp button and ad CTAs should deep-link here. If you're building landing pages or ad creative and the WhatsApp link isn't 4144, that's almost certainly wrong.
- **Business law lead inbound = 0705 (Aline).** Referrals and repeat business land here directly. There's no website funnel pointing to this number today.
- **Case/client communication splits across 8705 (William) and 2726 (Clauderlane)** depending on whether it's document work or billing.
- **Mike's number (7411) is not a client-facing number.** Don't route any client comms to it.

If you're tracing a lead flow and you see messages landing in an unexpected inbox, the first question is which number received the message, not which person saw it first.

