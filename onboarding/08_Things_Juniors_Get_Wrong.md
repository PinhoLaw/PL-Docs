# Things Juniors Get Wrong

A personal list from Mike. Not criticism — every senior dev has done most of these themselves at some point. The goal is to skip a few years of painful lessons.

Read it now. Re-read it every few months. You'll notice different items landing differently as you grow.

---

## 1. Solving Before Understanding

**The mistake:** You read the ticket, your brain jumps to "I can just do X," and you start typing.

**Why it's wrong:** Half the time, X doesn't actually solve the problem the person wanted solved. You've built the wrong thing faster.

**Do instead:** Spend the first 10 minutes asking "what is the person actually trying to accomplish?" Write it down in plain English. If you can't, go ask. The question "just so I'm clear, the goal here is X, right?" has saved more engineering hours than any framework.

---

## 2. Over-Engineering From Day One

**The mistake:** Adding abstractions, config flags, plugin systems, and flexibility for problems you don't have yet.

**Why it's wrong:** Every abstraction has a cost — cognitive load for you, for reviewers, for the future person who reads this code. Abstractions you don't need are pure cost with zero benefit.

**Do instead:** Write the dumbest version that works. When you need flexibility, *then* add it — and you'll know exactly what shape the flexibility needs to be because you've seen the second use case. "You Aren't Gonna Need It" (YAGNI) is one of the most important rules in software.

---

## 3. No Error Handling Because "It Won't Fail"

**The mistake:** `const data = await fetch(url).then(r => r.json())` — no try/catch, no status check.

**Why it's wrong:** Networks fail. APIs return 500s. JSON.parse throws on bad data. Services go down. Every one of these things happens in production.

**Do instead:** Assume every external call fails. Every one. Handle the failure case explicitly, even if "handling" just means showing a clear error message to the user instead of a blank white screen.

---

## 4. Shipping Without Types

**The mistake:** `any` everywhere, `@ts-ignore` to make errors go away, untyped function signatures.

**Why it's wrong:** TypeScript's whole value is catching bugs at compile time instead of at 2am when a lead doesn't flow into GHL. Using `any` is voluntarily turning that off.

**Do instead:** Type everything. When the compiler complains, it's usually right. If you truly need an escape hatch, use `unknown` and narrow it, or leave a comment explaining why `any` is acceptable here.

---

## 5. Hardcoding Configuration

**The mistake:** API URLs, timeouts, feature flags, retry counts — all hardcoded in the middle of business logic.

**Why it's wrong:** When prod breaks at 9pm because the API URL changed, you want to update one env var and redeploy, not hunt through code, find the string, change it, test it, PR it, ship it.

**Do instead:** Anything environment-specific goes in env vars from the first commit. Anything that might need to be tuned without a deploy goes in config. The rule: if you can imagine wanting to change this without editing code, don't hardcode it.

---

## 6. Committing Secrets

**The mistake:** You're testing something, you paste an API key directly into the code, "I'll take it out before I push." You forget.

**Why it's wrong:** Git remembers forever. Even if you delete the commit, the key is in history. Rotating the key is the only real fix.

**Do instead:** From the very start, put every secret in `.env.local` (gitignored) or the shared vault. Never type a secret directly into a file that lives in the repo. Your future self will thank you.

---

## 7. Skipping Tests On PII-Touching Code

**The mistake:** "It's a simple endpoint, I'll just ship it."

**Why it's wrong:** "Simple" endpoints that touch PII are exactly the ones you need tests on. The PII incident happened partly because some code paths weren't exercised. Tests are your check that the thing you think is true about the code is actually true.

**Do instead:** For any endpoint that reads or returns lead / client data, write at least: (1) a test that confirms an unauthorized caller gets denied, (2) a test that confirms the shape of the response matches what the caller expects, (3) a test for at least one error case.

---

## 8. Premature Optimization

**The mistake:** Spending three hours squeezing 50ms out of a query that runs 10 times a day.

**Why it's wrong:** You have limited hours in a day. Those hours should be spent on the thing that actually matters to the business right now. Performance usually doesn't matter until there's a user complaint or a scaling bottleneck — and even then, you should measure before optimizing.

**Do instead:** Build the thing. Measure. If it's slow enough to matter, profile and fix the real bottleneck. Don't pre-optimize based on vibes.

---

## 9. Not Reading The Existing Code

**The mistake:** Building your feature "fresh" without checking how similar features already work in the codebase.

**Why it's wrong:** You end up with two ways to do the same thing — now the codebase is inconsistent and future devs (including you) have to choose which pattern to follow.

**Do instead:** Before starting a new feature, spend 15–30 minutes reading how similar things are built. Match the existing style unless you have a specific reason to deviate, and if you do deviate, say so in the PR.

---

## 10. Silent Failures

**The mistake:** `try { ... } catch (e) {}` — swallow the error, move on.

**Why it's wrong:** Now you have a bug that doesn't show up. The app keeps running, reports "success," but the thing didn't actually happen. These are the worst bugs — they burn trust with users and take forever to diagnose.

**Do instead:** Either handle the error meaningfully (log, retry, show to user, fall back) or let it propagate. A loud failure is almost always better than a silent one.

---

## 11. Assuming The Data Is Clean

**The mistake:** Your code works beautifully on the three test records you made. It blows up on real production data because somebody entered their name as `     JOHN SMITH ♥` with weird characters and leading spaces.

**Why it's wrong:** Real data is messy. Always. People use emojis in name fields. Phone numbers come in 15 formats. Dates are sometimes strings. Numbers are sometimes strings that look like dates. JSON fields are sometimes empty strings instead of null.

**Do instead:** Validate and normalize inputs at the edge (where data enters your system). Trim strings. Parse numbers. Check for null / undefined / empty string explicitly. Assume every input will eventually contain the worst thing you can imagine.

---

## 12. Merging Your Own PRs

**The mistake:** Small PR, "nobody needs to review this," merge.

**Why it's wrong:** Code review isn't a formality. It's a safety net. Second set of eyes catches bugs, security issues, architectural mismatches. Also, you're a junior — getting reviewed is how you learn faster.

**Do instead:** Open the PR. Tag Mike. Wait for review. If it's genuinely urgent, ping him in Slack. Don't self-merge to production code.

---

## 13. Treating AI As An Oracle

**The mistake:** Claude / Copilot spits out code. You paste it in. It "works" so you ship.

**Why it's wrong:** AI is a tool, not an authority. It will confidently produce code that's subtly wrong, out of date, or doesn't match this codebase's conventions. If you ship AI code you don't fully understand, you are shipping a mystery that you will have to debug later.

**Do instead:** Use AI to get unstuck, to explore approaches, to draft. Then **read every line** and make sure you understand what it does and why. If you can't explain the code to Mike, you shouldn't ship it. This applies to this very assistant too.

---

## 14. Not Asking

**The mistake:** Spending four hours stuck on something you could have unstuck by asking a 2-minute question.

**Why it's wrong:** Your time has value. The team's time has value. The calculus is not "don't bother people." It's "is my 4 hours worth more or less than 2 minutes of Mike's time?" Almost always, it's less.

**Do instead:** Time-box. If you've been stuck for 30 minutes and aren't making progress, ask. Good question format: "I'm trying to do X. I tried Y and Z. Y failed because [reason], Z failed because [reason]. What am I missing?" That's a great question — easy to answer, respectful of the other person's time, shows you did the work.

---

## 15. Pretending To Understand

**The mistake:** Someone explains something, you nod, you don't actually get it, but you don't want to look dumb so you say "got it."

**Why it's wrong:** Now you're going to go make a decision based on something you didn't understand. The mistake you're about to make will look a lot dumber than asking for clarification would have.

**Do instead:** "Can you say that again in different words?" or "I want to make sure I'm tracking — is what you're saying [your paraphrase]?" There is zero shame in asking. The most senior engineers on the planet ask clarifying questions constantly. That's part of why they're senior.

---

## The Meta-Lesson

You're 21. You have surface context on this firm and this stack. That means you know *just enough to be dangerous* — enough to start writing code, not enough to know when you're about to break something.

The single most valuable habit at this stage of your career: **slow down before acting.** Read first. Ask first. Understand first. Code second.

The devs who burn out or get stuck are the ones who race to produce output. The devs who grow are the ones who take the extra 15 minutes to understand the system before touching it.

You've been given real access and real responsibility here. Don't treat it casually. Treat it like the opportunity it is.
