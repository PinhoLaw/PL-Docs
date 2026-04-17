# Pinho Law — Code Standards & PR Review Checklist

What gets looked for in PR review at Pinho Law. Run yourself through this list before requesting review.

## Before You Even Open a PR

- [ ] You understood the problem before writing code. If you can't explain what the feature does in one sentence, you don't understand it yet.
- [ ] You picked the smallest change that solves the problem. Resist "while I'm in here" refactors unless the PR is specifically a refactor PR.
- [ ] You ran it locally and verified it works
- [ ] You tested at least one failure case (bad input, network error, missing data)
- [ ] You checked the preview deployment if applicable

## Code Quality

- [ ] **TypeScript:** No `any` without a comment explaining why. Prefer `unknown` + narrowing. No `@ts-ignore` without a linked GitHub issue.
- [ ] **Error handling:** Every `await` is inside a try/catch or handled with `.catch()`. Every fetch call handles non-2xx responses. Every DB call handles the "no rows" and "DB down" cases.
- [ ] **Naming:** Variable and function names describe what they are / do, not how they're implemented. `leadScore` good. `calculateScoreFromFormSubmissionPayload` usually too long. `x` never.
- [ ] **Comments:** Explain *why*, not *what*. Code shows what; comments should explain the reasoning, trade-off, or gotcha.
- [ ] **No dead code:** Delete commented-out code. Git remembers.
- [ ] **No console.log in shipped code:** Use structured logging or remove.

## Security (Non-Negotiable — Re-read PII Postmortem if in doubt)

- [ ] No secrets in the diff. Check for API keys, tokens, webhook URLs, anything that looks suspicious.
- [ ] No PII in log statements
- [ ] API routes check auth server-side — not just on the client
- [ ] Every new Supabase table has an RLS policy
- [ ] Every new query uses parameterized inputs, never string concatenation (prevents SQL injection)
- [ ] If this adds a new public route, Vercel deployment protection coverage is verified

## Data Handling

- [ ] Inputs are validated before use (use Zod or similar)
- [ ] Database constraints match application assumptions (NOT NULL where it matters, foreign keys where relationships exist)
- [ ] Migrations are reversible where possible — or you've explicitly decided they're not and said so
- [ ] No `SELECT *` in production queries — pick the columns you actually need

## Performance

- [ ] No N+1 queries (loop that runs a DB query each iteration)
- [ ] Indexes exist for columns you filter or join on
- [ ] Large lists are paginated, not dumped client-side
- [ ] Images are optimized (Next.js `<Image />` component, not raw `<img>`)

## UX

- [ ] Loading states exist for every async action
- [ ] Error states are user-visible — not silent failures
- [ ] Forms are keyboard-navigable
- [ ] Text is not hardcoded in English if this area of the app is bilingual

## PR Description Must Include

- **What** this PR does, in one paragraph
- **Why** it needs to exist (link to ticket / message / incident)
- **How to test** — step-by-step reproduction on the preview deployment
- **What could break** — what existing functionality touches this code path
- **Rollback plan** — if this breaks in prod, how do you undo it

## Red Flags In Your Own PR

If you catch yourself thinking any of these, stop and reconsider:

- "It works, I'll add tests later" → now is later. Add them.
- "This is probably fine" → investigate until you know it's fine.
- "Nobody else touches this code, I don't need to document it" → somebody else will in 3 months. That somebody might be you.
- "I'll just quickly hardcode this" → hardcoding grows. Put it in config or env from the start.
- "The linter is wrong here" → the linter is usually right. If it's genuinely wrong, disable the rule on that line with a comment explaining why.
- "I'll commit this secret, it's just for testing" → rotate the secret, then commit.

## When Your PR Gets Reviewed

The reviewer will push back. That's the job. Pushback on a PR is not a personal judgment — it's the system working. The goal is the code, not your ego. Answer the question, make the change, or explain why you disagree. Don't take it personally and don't go silent.

## When You Review (Yes, You'll Review Too)

- Read the whole diff before commenting
- Distinguish "this is wrong" from "I would do it differently" — only block on the first
- If you don't understand something, ask — don't guess
- Approve when it's good enough to ship, not when it's perfect. Perfect is the enemy of shipped.
