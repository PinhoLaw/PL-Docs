# Pinho Law — Access, Environments, and Getting Started

## Accounts You'll Need

Ask Mike to provision (or confirm access to) each of these on day one:

| System | What you do there | Who grants access |
|---|---|---|
| **GitHub** | Repo access for dashboard + related projects | Mike |
| **Vercel** | Dashboard deployments, env vars, deployment protection settings | Mike |
| **Supabase** | Database access, RLS policies, migrations | Mike |
| **n8n cloud** (`jde.app.n8n.cloud`) | Workflow editing | Mike |
| **GoHighLevel (GHL)** | CRM access, custom field IDs | Mike |
| **Leadster** | Form builder, submission history | Mike |
| **Meta Business Manager** | Ads, Pixel, Conversions API | Mike |
| **Google Ads + GTM + GA4** | Ads, tags, analytics | Mike |
| **Google Workspace** (Drive, Sheets) | Shared docs, intake Sheet, team calendar | Mike |
| **1Password or equivalent** | Shared credentials vault | Mike |

## Environments

### Production
- `pinho-law-dashboard.vercel.app`
- Behind **Vercel deployment protection** — you need to be logged into the Vercel team to access it
- Connected to **production Supabase**
- Production secrets live in Vercel env vars
- Never run migrations directly against production DB — always go through the migration workflow

### Preview / Staging
- Auto-deployed on every branch push in Vercel
- Connected to production Supabase by default (⚠️ be careful — mutations hit real data)
- If you need an isolated data environment, ask Mike about a Supabase branch database

### Local Dev
- Clone repo, `npm install`, copy `.env.example` → `.env.local`, fill in dev credentials from the shared vault
- Supabase local CLI is available if you want to run against a local Postgres
- Never commit `.env.local`

## First Week Checklist

Do these in order. Don't skip ahead.

1. [ ] Read **01_Firm_Primer.md**
2. [ ] Read **02_Tech_Stack_Map.md**
3. [ ] Read **03_Lead_Flow.md**
4. [ ] Read **05_PII_Incident_Postmortem.md** — this is the most important doc in this folder
5. [ ] Get accounts provisioned (use the list above, ping Mike)
6. [ ] Clone the dashboard repo, get it running locally, make a tiny cosmetic change on a branch, open a PR, get it reviewed
7. [ ] Watch a Leadster submission flow end-to-end: submit a test lead, watch it flow through n8n, watch it appear in Pipedrive, watch it appear in the dashboard
8. [ ] Open one n8n workflow in read-only mode and trace what it does without changing anything
9. [ ] Book a 30-min walkthrough with Mike on the dashboard codebase and architecture
10. [ ] Book a 30-min walkthrough with Mike on marketing stack priorities

## How We Ship Code

- **Branch** per feature / fix — name it `feature/short-description` or `fix/short-description`
- **Open a PR** — Mike reviews
- **Don't merge your own PRs** unless it's a pure docs change or trivial copy fix
- **Deploy previews** auto-generate — share the preview URL in the PR description
- **Merging to `main`** triggers production deploy
- **Rollback plan** — know what you'd revert if it breaks before you merge

## Secrets Handling

Baseline rule: **if it has the word "key," "token," "secret," or "password" in it, it does not go in the repo.**

- ✅ Vercel env vars
- ✅ n8n credentials (stored encrypted in n8n)
- ✅ 1Password (or equivalent shared vault)
- ❌ `.env` files committed to git
- ❌ Hardcoded in source
- ❌ Pasted in Slack / WhatsApp / comments
- ❌ Sent in email

If you accidentally commit a secret, **rotate the secret immediately** — don't just delete the commit. Git history preserves everything.

## PII Handling

The firm handles immigration data. Treat every piece of lead or client data as if the person's ability to stay in the country depends on it (because sometimes it does).

- **Never** render lead/client PII on a publicly accessible URL
- **Never** log PII to console/server logs in production (use redacted identifiers)
- **Always** check RLS policies when adding new Supabase tables — default-deny, explicitly grant
- **Always** verify Vercel deployment protection is active for any route that exposes lead data
- When in doubt, ask Mike before shipping

## Who to Ping For What

| Question | Person |
|---|---|
| Dashboard code, architecture, deployments | **Mike** |
| Marketing stack, ads, n8n workflows, business priorities | **Mike** |
| Legal team workflows, what the team actually needs | **Dra. Izi** or direct with the team member |
| Case-specific questions | Legal team — never assume, always ask |
| Billing, contracts, retainer flow | **Mike** or **Dra. Izi** |

## Communication Norms

- Day-to-day: WhatsApp or Slack (ask Mike which the team is using this week)
- Docs and decisions: Google Drive
- Code discussion: GitHub PR comments and issues
- Urgent / production down: phone call to Mike
