<!--
  Example output: a completed AI DILIGENCE STATEMENT for a FICTIONAL tool
  ("TeamTrack"), a vibe-coded project/task management app with NO AI features in
  the product itself - the typical case. Several answers are "no" or "not yet"
  with context; those still count as complete. Honesty, not perfection.
-->

# AI Diligence Statement - TeamTrack

> **What this is:** an honest account of how this app was built (largely with AI)
> and how it's operated - written so you can decide whether to trust it. It is
> **not** a guarantee of security or safety.
>
> **Disclosure completeness: 8 of 8 sections answered.** This counts how much I
> disclosed - *not* whether the app is good, safe, secure, or trustworthy. "8 / 8"
> means I answered every question honestly, **not** that the app passed anything.
> Please don't read the number as a grade.

_Last updated: 2026-06-24_

## 1. AI involvement & builder capability
- **How much AI assistance:** Almost entirely. I described the features and an AI
  agent wrote nearly all of the code.
- **Could I debug / maintain this solo?** Partly. I can read the code and fix the
  main screens (tasks, boards, login) with some effort. The database setup and a
  couple of the libraries are still over my head.
- **My relevant background, honestly:** I'm a project manager, not a developer. I
  can follow code and use the terminal, but this is my first real app.
- **Did I review the AI's code?** Targeted. I read the auth and data-saving parts
  closely because they felt important; I skimmed the rest and trusted the AI on it.
- **If it broke and users were affected:** I'd pause new signups, ask the AI to
  help me diagnose it, and post status in our Discord. I don't have an on-call
  setup.

## 2. Who's behind it
- **Who:** Sam Rivera (@sambuildsteamtrack on GitHub and X).
- **Contact / report an issue:** sam@teamtrack.example or GitHub issues.
- **Typical response time:** A few days. This is a nights-and-weekends project.
- **Entity:** An individual. No company yet.

## 3. What it is, honestly
- **Age & scale:** ~5 months old, around 300 users across ~40 small teams.
- **Maturity:** Beta. It's stable for daily use but I still ship rough edges.
- **Should you rely on it for anything important?** Fine for tracking work;
  please don't make it the only place critical data lives yet. Export regularly.
- **Uptime/SLA:** None promised. It runs on a hobby-tier host.

## 4. Data: what's collected and where it goes
- **Collected:** Email + password hash for accounts, your projects/tasks/comments,
  and basic page-view analytics.
- **Stored where:** Supabase (Postgres), EU region.
- **Third parties that touch it:** Supabase (database + auth), Plausible
  (privacy-friendly analytics), Stripe (payments for the paid plan), Resend
  (transactional email like invites and password resets).
- **Anything sent somewhere unexpected?** No. TeamTrack has no AI features; your
  data is not sent to any model provider.
- **Retention:** Kept until you delete your account.

## 5. Data: your control
- **Export your data:** Yes - Settings → Export gives you a JSON of all your
  projects and tasks.
- **Delete account & data:** Yes - Settings → Delete account removes your rows
  immediately. Backups roll off within 30 days.
- **Privacy policy:** https://teamtrack.example/privacy
- **Terms of service:** None yet. Planned.

## 6. Security posture (what's been done - and not)
- **Auth:** Supabase Auth (I did not roll my own).
- **Per-user data isolation:** Supabase row-level security is on; each team can
  only read/write its own data. I've tested this manually but not exhaustively.
- **HTTPS only:** Yes (platform default).
- **Secrets handling:** Environment variables on the host. No keys in the repo.
- **Security review:** None formal. No professional has reviewed it.
- **Automated self-check:** Ran the Show Your Work scan. It looked for a handful
  of common vibe-coding holes - open database rules, secrets in the client,
  frontend-only auth, the server trusting client-supplied IDs, committed secrets -
  and flagged none.

> _About that self-check: it looked only for a few specific, common mistakes.
> **Finding none does not mean the app is secure.** It is a best-effort automated
> read of the code, not a security review, and it is not exhaustive._

_This section describes what exists. It is not a claim that the app is secure._

## 7. Source & cost
- **Source:** Closed for now; I may open it later.
- **Cost:** Free for up to 3 projects; a paid plan above that, billed via Stripe.

## 8. Known limitations & what's next
- **Known weak / unfinished / untested areas:** No automated tests yet. The
  invite flow occasionally double-sends emails. Mobile layout is rough.
- **Roadmap:** Add tests, write terms of service, then recurring/scheduled tasks.

---

<sub>This statement measures completeness of disclosure, not trustworthiness. Any
automated scan referenced here is a best-effort read of the code by an AI tool: it
is non-exhaustive, can miss real problems, varies from run to run, and is
illustrative - not an audit. It cannot evaluate runtime security, auth
correctness, or access-control logic. <strong>Absence of flagged issues is not a
clean bill of health.</strong></sub>
