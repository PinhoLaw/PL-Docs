# Project Brief: Pinho Law Dashboard Triage Audit

**Version:** 0.1
**Owner:** Mike Stopperich
**Target:** 4–6 hours of combined dev time (triage pass)
**Follow-up decision:** Based on triage findings, Mike decides whether a deeper audit is warranted
**Status:** Approved for build (starts after Vox ships)
**Prerequisite:** Vox must be shipped and in use first

---

## What We're Doing

A **triage pass** of the existing Pinho Law Business Dashboard (`pinho-law-dashboard.vercel.app`) to answer one question:

> **How broken is this, actually?**

Not a comprehensive audit. Not a rebuild. A focused 4–6 hour investigation that produces a prioritized findings document, fixes a handful of quick wins, and gives Mike enough information to decide what to do next.

## Why "Triage" Instead of "Full Audit"

Full audits are expensive. A comprehensive functional audit of a production web app is 2–3 days of work minimum, and often longer. You might spend 20 hours auditing and then discover the answer was findable in 4.

Triage is different. The goal is: get enough signal, fast, to make an informed decision about where to invest time. Clinicians triage patients in ERs for the same reason — you don't run every test on every patient, you quickly figure out who needs attention first.

After the triage pass, Mike decides:
1. Everything is mostly fine → no deeper audit needed, just handle the quick-win fixes and move on
2. Specific area is concerning → deeper audit scoped to that area
3. It's worse than we thought → full comprehensive audit scheduled as its own project

## Prerequisite: Vox Must Ship First

Don't start this until Vox (the PM/timekeeping dashboard) is shipped and in active use. Two reasons:

1. You need to be *using* Vox to track your time on this project — that's what it's for.
2. Context-switching between two in-progress projects burns more time than it saves. Finish Vox, ship it, then come back here with a clear head.

## Scope: What's In, What's Out

### In scope (what you WILL do):

- Get the dashboard running locally
- Click through every page, every feature, every button
- Read the code for any area that looks concerning
- Document every finding (more on this below)
- Implement 2–3 clear quick-win fixes as PRs
- Write up a structured findings report

### Out of scope (what you will NOT do in this pass):

- Rebuilding anything
- Adding new features
- Refactoring for aesthetics
- Performance optimization beyond the obvious
- Security audit (that's its own project if needed)
- UX redesign
- Any fix that takes more than ~30 minutes

If you find something that would take more than 30 minutes to fix, **don't fix it — document it.** The whole point of triage is to surface findings, not to start fixing everything you see.

## What You're Looking For

The original request was: *"find where it's broken or leaking."*

Specifically, pay attention to:

1. **Broken functionality** — pages that error, buttons that don't work, data that doesn't load
2. **Half-built features** — features that exist in the UI but don't actually do anything, or only do half of what they claim
3. **Data issues** — reports showing wrong numbers, missing records, stale data, duplicate entries
4. **Integration failures** — Pipedrive connection, GHL connection, n8n webhooks — anything that should be flowing in and isn't
5. **Deployment or config issues** — preview deployments exposing data, env vars missing, deployment protection gaps
6. **Obviously stale code** — TODO comments from 6 months ago, commented-out code blocks, functions that look orphaned

**Things you should NOT spend time on in a triage pass:**

- Minor styling issues
- Inconsistent naming
- "Could be refactored to be cleaner"
- Opinions on architecture choices (save for a deeper audit if one happens)

## The 30-Minute Rule

For every finding: if it takes more than 30 minutes to fix, **stop and document it instead**. This is the core discipline of triage.

Why: it's very tempting to think "I'm already in this file, let me just fix it." That's how a 4-hour triage becomes an 8-hour unfocused fix session that didn't actually produce a usable report.

Quick wins only. Everything else goes in the document.

## Documentation Discipline (Non-Negotiable)

**Document every finding BEFORE fixing anything. Every one. No exceptions.**

This is the most important rule of the whole project. The audit document is the primary deliverable — more valuable than the fixes themselves. Six months from now, someone (possibly you, possibly Mike, possibly another dev) needs to be able to ask "what was the state of the dashboard on April 25, 2026?" and get an answer.

If you fix something without documenting it first, that finding is lost forever. Git history shows *what changed* but not *what was wrong*.

Workflow for every finding:

1. Notice something
2. Write it down in the findings doc (template below)
3. *Then* decide: quick win fix, or flag for later?
4. If fixing: reference the finding ID in your PR

## Findings Document Format

Create `AUDIT_FINDINGS.md` in the dashboard repo. Structure:

```markdown
# Dashboard Triage Audit — Findings

**Date:** 2026-04-XX
**Auditors:** [dev names]
**Time spent:** XX hours total
**Summary:** [2–3 sentence overall take]

## Findings

### Finding #001: [Short descriptive name]
- **Area:** [page / feature / system]
- **Severity:** Critical / High / Medium / Low
- **Effort to fix:** Quick (<30min) / Medium / Large
- **What's wrong:** [description]
- **How to reproduce:** [steps]
- **Screenshot or code reference:** [if helpful]
- **Status:** Documented / Fix in PR #X / Fixed in commit XYZ / Flagged for later
- **Recommendation:** [what should happen next]

### Finding #002: ...
```

Number findings sequentially. Never renumber. If a finding turns out to be a duplicate, mark it "Duplicate of #XXX" but keep it in the list.

## Severity Definitions

Use these exactly. Don't invent new categories.

- **Critical:** Data exposure, auth broken, or prod outage. Stop triage and tell Mike immediately.
- **High:** Core feature broken for users, or actively producing wrong results. Document, don't fix unless quick win.
- **Medium:** Feature partially broken, workaround exists, or cosmetic issue with data impact.
- **Low:** Cosmetic only, stale code, minor inconsistencies.

## Final Report Requirements

At the end of the triage, the findings doc should include a **Summary section at the top** with:

1. **Overall assessment** in 2–3 sentences — one human's take on how the dashboard actually is
2. **Top 3 things Mike should know** — the most important findings, ranked
3. **Quick wins fixed** — list of what got fixed during triage (with PR links)
4. **Recommendations** — one of:
   - "No further audit needed, just handle X/Y/Z findings when convenient"
   - "Deeper audit recommended, focused on [specific area]"
   - "Full audit warranted — we found significant issues"
5. **Time spent** — actual hours each of you put in (from Vox)

## Acceptance Criteria

Before calling this done:

- [ ] Dashboard runs locally for both devs
- [ ] Every page has been clicked through
- [ ] Every interactive element has been tried
- [ ] `AUDIT_FINDINGS.md` exists in the dashboard repo
- [ ] All findings use the format above, with severity and effort labeled
- [ ] Summary section at the top with the 5 required items
- [ ] 2–3 quick wins have PRs (merged or ready for review)
- [ ] Total time tracked in Vox doesn't exceed 6 hours combined
- [ ] Final recommendation is clear enough that Mike can make a yes/no decision on deeper audit

## Process

1. **Kickoff (15 min):** Both devs meet with Mike. Mike grants any missing access, answers questions, confirms priorities.
2. **Individual exploration (1–2 hrs):** Each dev clicks through the app solo, takes notes. **Do not coordinate who looks at what during this phase — you want two independent takes.**
3. **Compare notes (30 min):** Merge findings, deduplicate, decide who tackles which quick wins.
4. **Quick-win fixes (1–2 hrs):** Each dev fixes what they can, PRs for review.
5. **Write up final report (1 hr):** One of you owns the final findings doc. Pair on it if needed.
6. **Present to Mike (30 min):** Walk him through the findings. This is as important as the document.

## Handoff to Mike

When you present findings to Mike, lead with the recommendation. Not the methodology, not the list of 40 things, not the screenshots. **"Based on what we found, we recommend X."** Then support it with the top 3 things. Everything else lives in the document for reference.

This is a skill. Practice it here. Senior engineers learn to lead with recommendation + rationale instead of data-dumping.

## Hard Rules

1. **Don't fix things that take more than 30 minutes to fix.** Document them.
2. **Document every finding before fixing.** No silent fixes.
3. **Critical findings (data exposure, PII, security) — stop and tell Mike same-day.** Don't wait until the write-up.
4. **Don't add new features.** This is triage, not expansion.
5. **Don't redesign.** UX concerns go in the findings doc, not in commits.
6. **Stay within the timebox.** If you hit 6 hours combined and the report isn't done, that's a finding itself — it means the app has more issues than triage can surface.

## When You're Stuck

Same rules as always (see `onboarding/08_Things_Juniors_Get_Wrong.md`, rule #14). 30-minute time-box, then ask Mike. Don't burn 2 hours debugging why the dashboard won't boot locally — that's exactly the kind of thing a 2-minute question solves.

## What Happens After

Based on the triage report, Mike decides one of three paths:

- **Path A — "It's fine, just handle the quick wins":** The findings doc becomes the backlog. Issues get fixed opportunistically. No separate audit project.
- **Path B — "Deeper dive on [area]":** A follow-up project gets scoped with specific focus. Brief is written, probably 8–12 hours.
- **Path C — "This needs a comprehensive audit":** A full audit is scheduled as its own project. 2–3 days of dedicated time.

You don't need to know which path it'll be. Your job is to produce the triage report that makes the decision obvious.

---

**Last updated:** 2026-04-17
**Next review:** After triage ships — Mike reads findings, decides on follow-up path
