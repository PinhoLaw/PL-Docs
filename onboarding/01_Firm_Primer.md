# Pinho Law — Firm Primer

## What Pinho Law Does

Pinho Law is a Florida-based firm with two practice areas:

1. **Immigration** — employment-based and family-based green cards
2. **Business Law** — full-service business legal support for founders and operators

The firm serves both practice areas with separate internal departments led by different attorneys.

### Immigration Practice

- **Employment-based green cards** — including EB-1 ("Extraordinary Ability") and EB-2 NIW ("National Interest Waiver"), both self-petition categories where the client does not need a U.S. employer sponsor
- **Family-based green cards** — petitions through U.S. citizen or permanent resident family members

### Business Law Practice

Full-service business legal work — entity formation, contracts, compliance, transactional matters — for business owners operating in Florida.

## Who the Clients Are

The firm has two distinct Ideal Customer Profiles depending on the practice area:

### Immigration ICP
- **Age:** 30–55
- **Profession:** Heavy skew toward tech professionals — software engineers, data scientists, ML researchers, product managers, founders
- **Origin:** Predominantly Brazilian, with growing English-speaking segments
- **Budget readiness:** ~89% of leads have budget ready (from lead analysis of 2,926 Leadster leads over 12 months)
- **Pain point:** They want to live and work in the U.S. long-term without being tied to a single employer or a multi-year H-1B lottery process

### Business Law ICP
- **Brazilian business owners living in Florida**, or
- **Any Brazilian living in Florida with a business service need** — B2B or B2C
- Typical needs: entity formation, contract work, compliance, ongoing legal counsel for an operating business

## Why This Matters for Devs

Everything you build serves one of three goals:

1. **Get more qualified leads into the pipeline** for either practice area (marketing automation, ads, Leadster funnel)
2. **Help the legal team convert and serve them efficiently** (dashboards, case tracking, document workflows)
3. **Keep client data safe and compliant** (PII protection, access control)

If a feature doesn't move one of those needles, question whether it should exist.

Also: because the firm runs two practice areas, some systems need to cleanly distinguish between immigration leads and business law leads. When building intake flows, CRM fields, or reports, always ask which practice area the data belongs to — don't assume.

## The Team

| Name | Role |
|---|---|
| Name | Role | Location |
|---|---|---|
| **Dra. Izi Pinho** | Lead attorney, owner, face of the firm in creative/marketing | Orlando, FL (HQ) |
| **Gregori Pretto** | Head of Immigration department | Orlando, FL (HQ) |
| **Guilherme** | Attorney, Head of Business department | London, UK |
| **Thomaz** | Immigration operations | Cuiabá, Brazil |
| **William** | Paralegal (immigration) | Orlando, FL (HQ) |
| **Maneola** | Junior paralegal (business team) | Brazil |
| **Aline** | Executive Assistant / Paralegal / business intake | Orlando, FL (HQ) |
| **Maria Luiza** | Assistant Executive Assistant | Recife, Brazil |
| **Thaisa** | Front desk / immigration intake specialist | Orlando, FL (HQ) |
| **Hannah** | Case management / legal ops | Natal, Brazil |
| **Gabriel** | Case management / legal ops | Natal, Brazil |
| **Clauderlane** | Head of Finance department | Recife, Brazil |
| **Start Marketing** | Builds and deploys marketing creative (agency) | Brazil (city unknown) |
| **Joao** | Paid ads | São Paulo, Brazil |
| **Mike Stopperich** | Marketing, automation, and operational systems lead (not a lawyer) | Florida, US |

The firm operates bilingually — **English and Brazilian Portuguese**. Many internal docs, WhatsApp messages, and UI strings will be in Portuguese. Do not "correct" them to English.

**Pinho Law is a distributed firm with Orlando, Florida as HQ.** The client-facing core — Dra. Izi, Gregori, William, Aline, Thaisa — is based in Orlando. The immigration legal support team is partially based in Brazil (Thomaz in Cuiabá, Maria Luiza in Recife, Hannah and Gabriel in Natal). Finance is in Recife. Paid ads is in São Paulo. The Head of the Business department (Guilherme) works from London, UK. This means:
- Time zones span four regions: Orlando (ET), Brazil (1–2 hours ahead of ET depending on daylight savings), London (4–5 hours ahead of ET)
- Async-first communication is the norm — WhatsApp and email, not hallway conversations
- Portuguese is the day-to-day working language for most of the team; English for client-facing Orlando work and coordination with London

## How the Firm Makes Money

Clients pay for legal representation — retainers on immigration petitions, engagement fees and hourly/flat fees on business law matters. The sales workflow is five stages for both practice areas:

1. **Lead** — someone submits interest (Leadster form, Meta ad, Google ad, referral)
2. **Qualified lead** — scored HOT/WARM/COLD based on intake answers
3. **Consultation** — booked with the legal team (immigration or business, depending on need)
4. **Contract** — client signs engagement letter, pays retainer / engagement fee
5. **Case work** — petition drafted and filed, or business matter executed

Every system you touch is serving one of these five stages. Know which stage you're affecting before you ship — and know which practice area.

## Competitive Landscape

Primary competitors for the immigration practice (useful context when reviewing ad copy or positioning):

- **Colombo & Hurd**
- **Linhares Law**
- **Lopes Law**

Pinho's differentiator is Dra. Izi's personal brand, Portuguese-first communication, two practice areas under one roof (clients who come in for immigration often need business help later, and vice versa), and a productized intake + qualification flow.

## Live URLs You Will Actually Touch

- `pinho-law-dashboard.vercel.app` — internal ops dashboard (protected behind Vercel deployment protection)
- Leadster intake form — bilingual 18-question funnel
- n8n cloud instance — workflow orchestration
- GoHighLevel (GHL) — CRM of record
- Meta Ads Manager, Google Ads, GTM — marketing stack

## One Rule Above All Others

**This firm handles sensitive immigration and business data.** Passport numbers, family member info, employment history, sometimes financial details, business formation documents, contracts. PII protection is not a nice-to-have. We had an exposure incident that was fixed via Vercel deployment protection — read the PII Incident Postmortem before touching anything that renders lead or client data.
