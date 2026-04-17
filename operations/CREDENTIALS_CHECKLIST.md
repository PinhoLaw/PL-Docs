# Pinho Law — New Developer Credentials Checklist

Use this to provision access for each new developer. Check off each item as you hand it over. Most of these should go into a shared password manager (1Password or equivalent) and invited by email, not sent over WhatsApp or Slack.

**Before you start:** confirm each developer has a personal password manager set up and a secure email they'll use for invitations. Get those two things first.

---

## TIER 1 — Must Have Day One (Cannot Work Without)

### Development & Deployment

- [ ] **GitHub** — add to the Pinho Law org/repos. Grant write access on relevant repos. Confirm they've enabled 2FA on their GitHub account before inviting.
- [ ] **Vercel** — invite to the Pinho Law team. Grants them access to deployments, env vars, deployment protection, preview URLs, and build logs. Role: Developer or Member.
- [ ] **Local dev environment credentials** — the `.env.example` file template, plus the actual dev values for:
  - Database connection string (Supabase if confirmed in stack)
  - Pipedrive API key (or OAuth app credentials)
  - Any third-party dev API keys

### Core Business Systems

- [ ] **Pipedrive** — add as a user. This is the primary CRM and source of truth for immigration leads. Confirm seat availability first (Pipedrive charges per seat). Role: depends on plan — give them what they need to read and write but not delete pipelines.
- [ ] **n8n cloud** (`jde.app.n8n.cloud`) — add as a team member. They need this to build and debug workflows.
- [ ] **Google Workspace** — create a `@pinholaw.com` email (or equivalent) for each. Grants access to Drive, Sheets, team calendar, shared docs. Also needed as the identity they'll use to log into GTM, Google Ads, GA4.

### Communication

- [ ] **Primary team chat** (Slack or WhatsApp — pick one and tell them which) — invite to the dev channel + general team channel. Don't put them into every channel; they'll ask if they need more.
- [ ] **Email distribution list** (if you have one) — `dev@pinholaw.com` or similar

---

## TIER 2 — Needed In First Week

### Lead Intake

- [ ] **Leadster** — log in via admin. Grants access to form builder, submission history, webhook settings. Confirm whether Leadster supports multiple users on your plan; if not, share credentials via password manager only.
- [ ] **Pinho Law WhatsApp (ending 4144)** — this is trickier. The firm's WhatsApp number isn't a per-user login. Options: (a) give them WhatsApp Web access via Aline/intake team when they need to trace flows, or (b) if you've got WhatsApp Business API, that has proper user management. Flag this one for manual coordination rather than a credential handoff.

### Marketing Stack

- [ ] **Meta Business Manager** — add to the Pinho Law Business account. Grant access to: Ads Manager, Pixel, Conversions API. Role: Employee access (not Admin) unless they specifically need to manage billing or users.
- [ ] **Google Ads** — invite via Google Ads user management. Role: Standard access is usually right. They need to read campaigns and debug conversions, not change budgets.
- [ ] **Google Tag Manager (GTM)** — grant workspace access on the Pinho Law container. Role: Edit (or Publish, if you trust them to publish — I'd default to Edit, Publish for Mike only).
- [ ] **Google Analytics 4 (GA4)** — grant Viewer or Editor role on the Pinho Law property.

### CRM (Uncertain Status)

- [ ] **GoHighLevel (GHL)** — read-only access for historical context. Do NOT grant workflow edit permissions yet — GHL's future is an open question, and you don't want them building on a system that might get sunset. Mark this as "read-only for context, do not build new workflows here."

---

## TIER 3 — Needed When They Touch That Specific System

Grant these on request, not proactively. Reduces surface area.

### Infrastructure

- [ ] **Supabase** — only if/when confirmed as part of the stack. If yes: invite to the Pinho Law project, grant role based on what they're doing (Developer for schema changes, Read-only for debugging). Confirm Supabase is actually in use before granting.
- [ ] **Domain registrar** (GoDaddy, Cloudflare, Namecheap — wherever `pinholaw.com` is registered) — usually only Mike needs this. Skip for new devs unless they're touching DNS.
- [ ] **Cloudflare** (if used for DNS/CDN/WAF) — same as above. Only if needed.

### Third-Party Integrations

- [ ] **Stripe / payment processor** — only if they're building billing-related features. Grant View-only unless they need more.
- [ ] **Calendly or booking system** — if you use one for consultations, grant access when they're touching the booking flow.
- [ ] **Email/SMS provider APIs** — Twilio, SendGrid, or whatever is sending transactional comms. Grant only when needed.

---

## Things That Are NOT Credentials But You Still Need To Hand Over

- [ ] **Link to the GitHub repo(s)** (even after they have GitHub access, tell them which repos to start with)
- [ ] **The `.env.example` file** or equivalent, showing which env vars the app needs
- [ ] **The Pinho Law knowledge files** (the 8 onboarding docs)
- [ ] **Your contact info** and preferred escalation path for emergencies
- [ ] **The Pipedrive deal stage map** — what each stage means, what triggers advancement, which fields matter. This is tribal knowledge that needs to be written down somewhere or walked through in a meeting.

---

## Security Ground Rules (Say This Out Loud When Handing Over)

Before you send credentials, tell each developer explicitly:

1. **2FA is required on every account** that supports it. No exceptions. GitHub, Google, Vercel, Meta, Pipedrive — all of it.
2. **Nothing gets shared over WhatsApp, Slack, or email.** Shared secrets go into the password manager. One place, encrypted.
3. **If they lose a device** (laptop, phone) they tell you same-day so you can rotate.
4. **Personal accounts only.** They log in with a credential that belongs to them. No shared logins except where there's no alternative (Leadster, the WhatsApp number).
5. **Offboarding is a real process.** When/if they leave, every access above gets revoked within 24 hours. Start a note now for each dev listing what you've granted, so the offboarding list writes itself.

---

## Your Provisioning Workflow (Suggested Order)

If you're doing this in one sitting:

1. Set up their `@pinholaw.com` email first — other invites get sent to it
2. Add to password manager, share the "Dev Credentials" vault
3. GitHub, Vercel invite (they can start cloning and running things locally)
4. Pipedrive, n8n, Leadster (they can trace the lead flow)
5. Meta, Google Ads, GTM, GA4 (they can see marketing)
6. GHL (read-only)
7. Everything in Tier 3 — wait until asked

Total time to provision one dev fully: about 60–90 minutes if you're moving through systematically. Budget two hours per dev.

---

## Copy-Paste Questions For Each Vendor If You're Not Sure About Seats/Pricing

Send these ahead of time if you need to check before inviting:

- **Pipedrive:** "Do we have open seats on our current plan, or will adding a user trigger a billing change?"
- **Leadster:** "Does our plan support multiple users, or do we need to share a single login?"
- **Vercel:** "Are we on Pro / Enterprise? Adding team members may affect billing."
- **GHL:** "How many user seats do we have on our current plan?"

---

## What To Do If You Can't Find A Credential

For any system in this list where you're not sure who owns the admin login or where the credentials live:

1. Check the password manager first
2. Check email for the original signup confirmation
3. Last resort: password reset via the admin email

Don't grant "temporary" access by giving a new dev your personal login. It creates a mess you'll have to untangle later.
