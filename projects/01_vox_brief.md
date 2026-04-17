# Project Brief: Vox

**Version:** 0.1 (MVP spec)
**Owner:** Mike Stopperich
**Target ship date:** Within first week of dev start
**Status:** Approved for build

---

## What We're Building

**Vox** — a small internal web app for the Pinho Law dev team (you two + Mike) with two core features:

1. **Projects pipeline** — a list of the projects you're working on, with status
2. **Timekeeping** — start/stop timer tied to a specific project, so we know where time is going

The point is *visibility* — one shared place where everyone on the dev team can see what's being worked on and where time is going.

That's it. No tasks, no sprints, no estimates, no integrations. Those are explicitly out of scope for v1.

## Why We're Building It

Two reasons:

1. **Operational need.** Mike needs visibility into how much time is being spent on which projects. Off-the-shelf tools either don't do timekeeping well (Linear, GitHub Projects) or don't do project management (Toggl, Harvest). We want one shared view.
2. **Learning project.** This is your first project on the Pinho stack. It's bounded, low-risk, and you'll use the tool you build every day. That's the best kind of feedback loop for learning.

Dogfood your own tool. If it's painful to log time, fix the UX before adding features.

## Users and Roles

| User | What they can do |
|---|---|
| Dev (you two) | Add projects, log own time, see own hours, see all projects |
| Admin (Mike) | Everything above + see everyone's hours |

For v1: three users total. Hardcoded. No user signup flow — Mike creates the three accounts.

## Tech Stack

- **Frontend:** Next.js (App Router), React, TypeScript, Tailwind
- **Database:** Vercel Postgres
- **Hosting:** Vercel (behind deployment protection)
- **Auth:** NextAuth with email magic links, OR a simpler approach if you have a better idea (run it by Mike first)
- **Repo:** new repo in the PinhoLaw org — name it `vox` or `pinho-vox`

Why this stack: matches what the main Pinho dashboard will use, so you learn tools you'll use again. Vercel Postgres instead of Supabase because we don't need auth + RLS complexity for a 3-user internal tool.

## Data Model

Three tables. Keep it simple.

```sql
-- users: the three of us
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  role TEXT NOT NULL CHECK (role IN ('dev', 'admin')),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- projects: what you're working on
CREATE TABLE projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  status TEXT NOT NULL DEFAULT 'active',
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- time_entries: the actual hours logged
CREATE TABLE time_entries (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  project_id UUID NOT NULL REFERENCES projects(id),
  description TEXT,
  started_at TIMESTAMPTZ NOT NULL,
  ended_at TIMESTAMPTZ,
  duration_minutes INTEGER GENERATED ALWAYS AS (
    CASE WHEN ended_at IS NOT NULL
    THEN EXTRACT(EPOCH FROM (ended_at - started_at))::INTEGER / 60
    ELSE NULL END
  ) STORED
);

CREATE INDEX idx_time_entries_user ON time_entries(user_id);
CREATE INDEX idx_time_entries_project ON time_entries(project_id);
```

Notes:
- `duration_minutes` is computed from the timestamps, so you never store redundant data.
- `ended_at` is nullable — a null value means "timer is currently running."
- The status values for projects are your call. Start with something like `active` / `done` / `archived`. Don't over-engineer it.

## User Stories (The Actual Requirements)

### As a dev, I can:

1. **Log in** with my email (magic link)
2. **See a list of projects** with their status and total hours logged against each
3. **Add a new project** by entering a name (status defaults to active)
4. **Change a project's status** from a dropdown on each row
5. **Start a timer** by picking a project and (optionally) typing a short description, then hitting Start
6. **See my currently running timer** at all times — it should be visible from any page, showing elapsed time
7. **Stop the timer** — the entry gets saved
8. **See my time entries for today** on the home page
9. **See total hours per project** for the current week

### As an admin (Mike), I additionally can:

1. **See everyone's hours** — per-person breakdown on the reports page
2. **See per-project total hours across all devs**

## Out of Scope (v1)

Explicitly NOT building in this version:
- Task breakdown within projects
- Kanban board / drag-and-drop
- Time estimates vs actuals
- Editing past time entries (if you mess up, tough luck for now — we'll add this in v2)
- CSV export
- Slack / email notifications
- Mobile app (responsive web only)
- Invoicing / billing
- Categories, tags, or labels on time entries

Resist scope creep. If you think a feature is needed, ask Mike before building it.

## Non-Functional Requirements

**Logging time must take under 5 seconds.** This is the single most important UX rule. If I have to click 4 times and type 3 fields to start a timer, I won't log time, and the data will be worthless. One dropdown, one button, done.

**Timer survives page refresh.** If I reload the browser, my running timer is still running. (Hint: check the DB for a time_entry with no `ended_at` on page load.)

**Only one timer per user at a time.** If I start a second timer while one is running, the app should either auto-stop the first one or block me with a clear message. You decide which — document the choice.

**Deployment protection on.** This app is behind Vercel deployment protection even though it's internal. Same rule as the main dashboard.

## How to Split the Work

You two are working together. Rough suggested split (not rigid):

- **External dev (full-name):** backend — schema, API routes, auth setup, data access
- **Nephew:** frontend — timer component, project list UI, reports page

Pair on the integration points. Review each other's code before tagging Mike.

For PRs:
- Branch per feature
- Open PR when ready
- Tag Mike for review
- Don't self-merge to main

## Acceptance Criteria (How We Know It's Done)

Before calling this shipped:

- [ ] All three of us can log in
- [ ] Each of us can add a project
- [ ] Each of us can start a timer, pick a project, and stop it
- [ ] Running timer is visible and survives page refresh
- [ ] Home page shows today's entries
- [ ] Reports page shows hours per project (weekly)
- [ ] Mike's reports view shows per-dev breakdown
- [ ] Deployed to Vercel, behind deployment protection
- [ ] README.md in the repo explaining how to run it locally
- [ ] No secrets in the repo
- [ ] No PII (though there shouldn't be any here anyway)

## First Steps

Day 1:
1. Read the PL-Docs onboarding files if you haven't
2. One of you creates the repo (`vox` or `pinho-vox`), adds the other two as collaborators
3. Bootstrap Next.js + Tailwind, commit, push
4. Provision Vercel Postgres from the Vercel dashboard, connect it
5. Write the schema migration (SQL above is a starting point, not gospel — critique it)
6. First PR: empty scaffolded app deployed to Vercel, showing "hello world"

After that you've proven the pipeline works end-to-end. Everything else is just building features on top.

## Open Questions (Answer Before You Start Building)

1. Which status values do projects have? (decide and document in the PR)
2. What happens when a user tries to start a second timer while one is running? (auto-stop the old, or block?)
3. How do we handle timezones? (All entries in UTC in the DB, displayed in the user's local time — standard approach, confirm)
4. How should the pipeline UI actually look? (Your call — list, Kanban, whatever you think works best. Run a quick mockup past Mike before building.)

## Things That Will Probably Go Wrong

Heads-up on predictable snags:

- **Timer state on page refresh** — easy to get wrong. Source of truth is the DB (a time_entry with null `ended_at`), not React state.
- **Multiple tabs open** — if you open two tabs with a timer running, which one controls it? Think it through before building.
- **Magic link auth in dev** — you need to configure an email provider even for dev. Resend or SendGrid free tier both work. Ask Mike for API key access.
- **Vercel Postgres local dev** — you can connect to the production DB locally for dev, or use a local Postgres via Docker. Both fine, pick one and document it in the README.

## When You're Stuck

See `onboarding/08_Things_Juniors_Get_Wrong.md`, rule #14. Time-box to 30 minutes, then ask Mike. Don't burn 4 hours on something a 2-minute question would unstuck.

---

**Last updated:** 2026-04-17
**Next review:** After MVP ships — we'll write a v2 brief based on what you learned
