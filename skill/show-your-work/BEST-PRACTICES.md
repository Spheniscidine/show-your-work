# Best Practices & Next Steps

This is the guidance layer. Part A of the rubric asks the builder questions;
Part B flags hygiene problems; **this file turns the project's current state into
concrete, prioritized next steps.**

Each practice below is written so an agent running *inside the project* can:

1. **Detect** the current state (a real command or file check).
2. Decide **good / partial / missing**.
3. Emit a **next step** if it's not already good.
4. Tag a **priority**: `NOW` (do before sharing this with anyone), `SOON`
   (within the next iteration), `LATER` (matters as it grows).

> **Guardrail - read this every time.** These are widely-accepted hygiene
> practices, not a security audit, and not a checklist that ends in "certified."
> Doing them genuinely improves your posture *and* gives you honest things to put
> in your disclosure. It never makes the tool "safe," and the agent must never
> claim it does. Recommend; don't bless. Detection is shallow and best-effort -
> a missing signal can be a false negative (you did it a way the scan didn't see),
> so phrase findings as "I didn't detect X" not "you don't have X."

Detection commands assume `rg` (ripgrep) and `git`; fall back to `grep -r` if
`rg` is absent. Run audit commands (`npm audit`, `pip-audit`) only if the tool is
present; never install anything.

---

## A. Secrets & configuration

**A1. No secrets in version control.**
- Detect: `git ls-files | rg -i '(^|/)\.env'` and
  `rg -n --no-heading -e 'sk-[A-Za-z0-9]{20,}' -e 'AKIA[0-9A-Z]{12,}' -e '(api[_-]?key|secret|token|password)\s*[:=]\s*["'\''][^"'\'']{16,}' -g '!*.md'`
- Good: nothing tracked or hardcoded. Missing: a tracked `.env` or a real key in source.
- Next step: untrack it (`git rm --cached`), **rotate the exposed key immediately**,
  add to `.gitignore`, move to the host's secret store.
- Priority: **NOW** if a real secret is committed - this is the one true emergency.

**A2. `.gitignore` covers env files.**
- Detect: `.gitignore` exists and contains a `.env` pattern.
- Next step: add `.env` and `.env.*` to `.gitignore`.
- Priority: NOW.

---

## B. Authentication & access control

**B1. Auth via a vetted provider (not hand-rolled).**
- Detect: `rg -n -e 'clerk' -e '@supabase' -e 'next-auth' -e 'authjs' -e 'lucia' -e 'firebase/auth' -e 'auth0' -e 'passport' package.json src 2>/dev/null`
- Good: a known auth lib is present. Partial: custom session/JWT code with no provider.
- Next step: if the app has accounts and you rolled your own, strongly consider a
  managed provider (Clerk, Supabase Auth, Auth.js). If you keep custom auth, plan
  to have it reviewed and disclose that in §6.
- Priority: SOON (NOW if you found custom password handling with no hashing lib).

**B2. Per-user data isolation enforced server-side.**
- Detect (Supabase): look for `enable row level security` / policy files;
  flag `using (true)` / `with check (true)`. (Firestore): read `firestore.rules`,
  flag `allow read, write: if true`. (Custom API): check that handlers filter by
  the authenticated user id, not a client-supplied id.
- Next step: enable RLS / write real rules / add `where user_id = <session user>`
  on every read and write. This is the #1 vibe-coded data-leak cause.
- Priority: NOW if you find `if true` / open rules on user data; otherwise SOON.

**B3. HTTPS only.**
- Detect: hosting platform default (Vercel/Netlify/etc. are HTTPS by default);
  look for any hardcoded `http://` API base or disabled TLS verification.
- Next step: enforce HTTPS / HSTS; remove `http://` endpoints and
  `rejectUnauthorized: false`.
- Priority: SOON.

---

## C. Data protection & privacy

**C1. Privacy policy and terms exist.**
- Detect: `rg -li 'privacy' --files | rg -i 'privacy'` for a policy file; check
  for a linked policy route; same for terms.
- Next step: publish a short, honest privacy policy (a generator is fine for a
  v1) and link it. Even one paragraph that names what you collect beats nothing.
- Priority: SOON (NOW if you collect personal data and have none).

**C2. User can export and delete their data.**
- Detect: routes/handlers matching `export`, `download`, `delete-account`, `gdpr`.
- Next step: add an export endpoint and a real account-deletion path (and confirm
  delete actually removes rows + rolls off backups).
- Priority: SOON.

**C3. Third-party data flows are disclosed.**
- Detect: enumerate external services the app's data touches - auth, DB/hosting,
  analytics, email, payments - from dependencies and config
  (`rg -n -e 'supabase' -e 'firebase' -e 'planetscale' -e 'neon' -e 'stripe' -e 'plausible' -e 'posthog' -e 'segment' -e 'sendgrid' -e 'resend' -e 'mailgun'`).
  *(If the app happens to have an AI feature, the model provider - `openai`,
  `@anthropic`, `ai-sdk`, etc. - is just one more entry on this list, not a
  special case.)*
- Good: §4 of the disclosure names the services data actually flows through.
- Next step: list every third party that sees user data, so the reader isn't
  surprised by an invisible one.
- Priority: SOON.

---

## D. Dependencies & supply chain

**D1. Lockfile committed.**
- Detect: manifest present (`package.json`, `requirements.txt`, `pyproject.toml`,
  `go.mod`) AND matching lockfile tracked in git.
- Next step: commit the lockfile (`package-lock.json` / `pnpm-lock.yaml` /
  `poetry.lock` / `uv.lock`).
- Priority: SOON.

**D2. No known-vulnerable dependencies.**
- Detect: if available, run `npm audit --omit=dev` or `pip-audit` (read-only).
  Summarize high/critical counts only.
- Next step: update or patch high/critical advisories; enable Dependabot/Renovate.
- Priority: SOON (NOW for a critical with a known exploit in an exposed path).

---

## E. Input validation

**E1. Inputs validated at trust boundaries.**
- Detect: `rg -n -e 'zod' -e 'yup' -e 'valibot' -e 'pydantic' -e 'joi'` and check
  that forms / API handlers validate what users submit before storing it.
- Next step: validate and type user input at every save/submit path - protects
  both your data and your users.
- Priority: SOON.

---

## F. Reliability & durability

*(This is where "weekend project vs durable thing" gets evidence, §3.)*

**F1. At least a smoke test, ideally CI.**
- Detect: test files (`*.test.*`, `*_test.py`, `tests/`), a test script in the
  manifest, and CI config (`.github/workflows`, `.gitlab-ci.yml`).
- Next step: add one smoke test of the critical path + a CI job that runs it on push.
- Priority: LATER (but a strong durability signal - call it out).

**F2. Error tracking / monitoring.**
- Detect: `rg -n -e 'sentry' -e 'datadog' -e 'logtail' -e 'highlight.run' -e 'posthog'`
- Next step: wire up error tracking so you learn about failures before users tell you.
- Priority: LATER.

**F3. Backups exist and have been tested.**
- Detect: not reliably scannable - ask. Managed DBs (Supabase/Neon/etc.) often
  have automatic backups.
- Next step: confirm backups are on and that you've actually restored once.
- Priority: LATER.

---

## G. Transparency artifacts (cheap, high trust-ROI)

**G1. LICENSE present.** Detect: `LICENSE`/`LICENSE.md` at root. Next: add one. Priority: NOW.

**G2. SECURITY.md / a way to report issues.**
- Detect: `SECURITY.md`. Next step: add one line on how to report a vuln and your
  response expectation. Priority: SOON.

**G3. AI Diligence Statement published.**
- Detect: `AI-DILIGENCE.md` at root. Next step: generate it (that's this tool's
  job) and link it from the README. Priority: NOW.

**G4. README sets honest expectations.**
- Detect: README mentions maturity (beta/hobby) or AI involvement.
- Next step: add the badge + short blurb from `templates/SHORT-FORMS.md`.
- Priority: SOON.

---

## H. Builder capability (disclosure, not scan)

Not scannable - but the agent can surface a **compound-risk note** when several
signals stack up, gently and without shaming:

- If §1 says *fully AI-built* **and** the builder says they couldn't debug it solo
  **and** F1 found no tests **and** B2 found open data rules → that's a high-risk
  combination worth naming plainly to the builder, with the most load-bearing next
  step first (usually: lock down data access, then add one test).
- The recommendation is always *"here's the next step that most reduces your
  risk,"* never *"you're not qualified."* Capability gaps are disclosed and
  supported, not graded.

---

## How to present the results

Sort all detected next steps by priority, then by trust impact:

```
Recommended next steps

NOW
  - [B2] Firestore rules allow read/write if true - anyone can read every user's
    data. Replace with per-user rules before sharing this. (#1 priority)
  - [A1] A live-looking API key is committed in src/config.ts - rotate it now and
    move it to an env var.

SOON
  - [C3] Data flows to Supabase and Stripe but §4 lists neither - name the third
    parties so users aren't surprised.
  - [D1] No lockfile committed - run `npm install` and commit package-lock.json.

LATER
  - [F1] No tests found - a single smoke test + CI would make this read as durable.
```

Always close with the guardrail line: *these steps improve posture and give you
more to disclose honestly; they don't certify the app as safe.*

And label the scan honestly, right under the list - this is a **best-effort read
of the code by an AI tool**: it is non-exhaustive, misses things, and can return
different results on different runs. It's **illustrative, not an audit.** If the
list is short or empty, say so explicitly: *"I only checked the practices listed
here; a short list is not a clean bill of health."*
