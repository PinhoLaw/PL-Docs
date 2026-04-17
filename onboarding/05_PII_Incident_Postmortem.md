# Pinho Law — PII Incident Postmortem

**Status:** Resolved
**Severity:** Critical
**Data at risk:** Lead and client PII accessible via public dashboard URL

Read this carefully. This is the single most important teaching artifact in this folder. Every new dev reads it. It will save you from making the same mistake.

---

## What Happened

The Pinho Law Business Dashboard (`pinho-law-dashboard.vercel.app`) was deployed on Vercel without **deployment protection** enabled. This meant that anyone who knew or guessed the URL could load the dashboard and see:

- Lead names, phone numbers, emails
- Intake form responses (including profession, timeline, sometimes passport info)
- Internal pipeline stages
- Notes and communication history

Immigration case data. Fully exposed. On a publicly indexable URL.

The app had authentication on the login page, but because the frontend was client-rendered and the API routes weren't uniformly gated, portions of the data leaked through preview deployments and unauthenticated routes.

## Why It Happened

Several overlapping failures, not any one of them catastrophic on its own:

1. **Preview deployments on Vercel are public by default** unless deployment protection is turned on. Nobody had turned it on.
2. **Client-side auth was treated as a security boundary.** It is not. A client-side auth gate only hides the UI — the API underneath still responds to anyone.
3. **Supabase RLS policies were incomplete.** Some tables had row-level security, others didn't. The ones that didn't returned data to any authenticated (or in some cases, unauthenticated) request.
4. **Conversion tracking scripts** were also loading on pages that rendered PII, creating a secondary concern about third-party scripts seeing data they shouldn't.

## How It Was Fixed

- **Vercel deployment protection** was enabled on all environments (production and preview). Access now requires logging in to the Vercel team.
- **Supabase RLS** was audited and tightened — every table now has explicit RLS policies, default-deny
- **API route auth** was uniformly enforced server-side — no relying on client-side gates
- **Logging** was scrubbed of PII — no lead names or emails in server logs

## What This Means For You

Before you ship anything that renders or returns lead or client data, answer these questions out loud:

1. **Who can reach this URL?** If the answer isn't "only authenticated team members," it's wrong.
2. **Does the API route itself check auth?** Or does it trust that the client won't call it directly? If the latter, fix it.
3. **Is there an RLS policy on every table this query touches?** If not, add one before shipping.
4. **What does the server log when this runs?** Does any of it include PII? If yes, redact.
5. **If this goes to a preview deployment, who can see it?** If the answer is "anyone with the URL," Vercel deployment protection must be on.

## The Broader Lesson

**Security is not a feature you add. It is a property of every line of code you ship.**

It is far, far easier to build something secure from the start than to audit and retrofit later. The instinct "I'll ship it and fix security in a follow-up PR" is how incidents happen.

Related instincts to watch for in yourself:

- "It's just a preview, it doesn't matter" → preview URLs are public by default
- "The frontend hides it, so it's fine" → the frontend is not a security boundary
- "Nobody would think to hit this endpoint directly" → everybody can, and some do
- "I'll add logging to debug and take it out later" → you will forget

## When To Escalate

If you realize **at any point** that you have shipped, are about to ship, or have discovered existing code that exposes PII — stop what you are doing and tell Mike immediately. No blame. The right thing to do is flag it fast so it gets fixed fast.

It is never a career-ending mistake to surface a risk. It is a career-ending mistake to stay quiet about one.
