# The Show Your Work Rubric

This is the portable core. Any AI agent (or human) can follow it. It has two
parts:

- **Part A - The 8 disclosure sections.** The questions a stranger needs
  answered to decide whether to trust your tool. You answer them; the tool just
  asks and formats.
- **Part B - The hygiene signals.** Concrete things to look for in the repo,
  each framed as *fix it or disclose it*.

A hard rule runs through the whole thing: **disclosure, not certification.** The
goal is never to conclude "this is safe." The goal is a complete, honest picture
so the reader can judge for themselves. "I don't know" and "I haven't done that"
are complete answers. Silence and vagueness are the only failing answers.

---

## Part A - The 8 disclosure sections

For each section: the builder gives a substantive answer. An answer is
**complete** if it actually responds - including "no," "not yet," or "I don't
know" *with a sentence of context*. It is **incomplete** only if it's blank,
dodged, or replaced with reassuring noise ("we take security seriously").

The **Disclosure-completeness score** = number of complete sections out of 8.
It measures how much you've disclosed. It does **not** measure whether your app
is good, safe, or trustworthy. Make that distinction loudly in any output, and
never let the bare number travel without it (see the Output contract).

**Optional, unscored: "AI commitments" (the AI lines the builder won't cross).** A
short list of forward-looking promises specifically about **AI usage** the builder
chooses to hold, for example "I will never use your data to train AI models," "I
will never send your data to a third-party AI service," "no AI-generated art or
content," "any AI feature is opt-in and labeled," "no automated AI decisions about
a user without a human in the loop." Keep them AI-specific (general promises like
"I won't sell your data" belong in a privacy policy, not here). They differ from
the 8 sections: the sections look backward at what was built and run, while these
look forward at what the builder promises about AI. They are self-declared
promises, not verified facts (a broken one is on the builder), they sit at the TOP
of the statement as a positive opener, they are entirely optional, and they do
**not** count toward the N-of-8 score. Don't invent them or pressure for them;
surface the idea with examples and let the builder add real ones or skip.

### 1. AI involvement & builder capability
*The thing the whole genre is dodging. Lead with it. This is a top trust signal:
the reader wants to know both how the code was made AND whether the person can
stand behind it.*

**AI involvement:**
- How much of this was built with AI assistance? (Roughly: a little / a lot /
  almost entirely.)
- **Is the AI's authorship visible in your git history or contributors** (a
  `Co-Authored-By` trailer, a bot/agent author), or are all commits under your
  name only? Either is fine to disclose, but an all-human-looking commit log on
  AI-written code is exactly the quiet gap this statement exists to close. See
  BEST-PRACTICES G5 for the scan.

**Builder capability (the behavioral version - what you can do, not your résumé):**
- Could you debug a production issue in this codebase yourself, or would you need
  to ask the AI / someone else?
- Could you maintain it solo - read the code, ship a fix, understand a dependency
  warning - if the AI weren't available?
- What's your relevant background, honestly? (e.g. "professional dev, this stack
  is new to me" / "I can script but I'm not a web dev" / "first project, learning
  as I go.")
- **Did you review the AI's code, and how deeply - never / targeted / line-by-line?**
  This review-depth gradient is the single thing readers most want to know, so
  capture it explicitly. "Vibe-coded" is the umbrella term for the whole audience,
  but it must **not** flatten this distinction: "I trusted the AI and never read
  it" and "I read every line" are very different trust signals and both are valid,
  honest answers. Keep the gradient.
- If something broke at 2am and users were affected, what's your realistic plan?

Honest framing beats false confidence. "Almost entirely AI-built; I understand
the core flows and could patch them, but the real-time sync and some deps are
beyond me right now" is a great answer - it tells the reader exactly how much to
lean on this. We assess *honest, specific* capability disclosure, never raw skill
level, and never penalize a beginner who is upfront.

### 2. Who's behind it (identity & accountability)
- A real name or a consistent handle people can find you under.
- A way to reach you (email, form, GitHub issues).
- How someone reports a bug, a security issue, or a data request - and roughly
  how fast you'll respond.
- Is this a person, a side project, or a registered company?

### 3. What it is, honestly (maturity & expectations)
- How long has it existed, how many real users?
- Is it a hobby/side project, a beta, or a maintained product?
- Should anyone rely on it for anything important yet? Set the expectation.
- Any uptime/SLA promise - or explicitly none?

### 4. Data: what you collect and where it goes
- What user data is collected (accounts, content, analytics, payments)?
- Where is it stored (which provider/region, roughly)?
- **Which third parties touch it** (auth, hosting/DB, analytics, email, payments)?
  This is the core of the section - name the services your users' data flows
  through, even the ones that feel invisible.
- If the app sends data anywhere a user wouldn't expect - and *if* your app
  happens to include an AI feature, that means whatever you send to the model
  provider - name it too. (Most vibe-coded apps don't; skip if it doesn't apply.)
- How long is data kept?

### 5. Data: control the user has
- Can a user export their data?
- Can a user delete their account and data, and does delete actually delete?
- Is there a privacy policy and terms of service? Link them, or say there
  aren't any yet.

### 6. Security posture (what you've done - and haven't)
*Framed as honest disclosure, not a security audit. You are not claiming it's
secure; you're describing what exists.*

- How is auth handled (rolled your own, or a provider like Clerk/Supabase/Auth0)?
- Is data access scoped per-user (e.g. row-level security), or could one user
  reach another's data?
- Is traffic HTTPS-only?
- How are secrets/API keys stored (env vars/secret manager vs in the repo)?
- Has anyone reviewed it for security - you, a tool, a person? Or no one yet?
- **It is completely fine to say "no formal security review has been done."**
  That sentence is worth more than a vague safety claim.

### 7. Source & cost transparency
- Is the source open? Under what license? Or closed - and that's fine, just say so.
- Is it free, paid, freemium? Any surprise or usage-based costs?

### 8. Known limitations & what's next
- The honest "here's what I know is weak / unfinished / untested."
- A roadmap line or two, or explicitly "no roadmap, maintained as-is."
- This section is where credibility is won. A builder who names their own gaps
  reads as more trustworthy than one who claims none exist.

---

## Part B - Hygiene signals (fix it or disclose it)

These are *light, static* signals an agent can check by looking at the repo.
**None of them is a verdict.** A clean scan does not mean the tool is secure; a
flagged signal does not mean it's unsafe. Each maps to an action: **fix it**, or
**disclose it** in the statement.

Report each as: `signal → what it suggests → fix it / disclose it`.

| Signal | How to check | Maps to |
|--------|--------------|---------|
| Committed secrets file | `.env`, `.env.local` etc. tracked in git (`git ls-files \| grep -i '\.env'`) | **Fix** (untrack + rotate keys), then disclose §6 |
| Hardcoded-looking keys | grep for `sk-`, `AKIA`, `api_key =`, long base64/hex literals in source | **Fix** if real, then §6 |
| No `.gitignore` env coverage | `.gitignore` missing or doesn't cover `.env*` | **Fix**, §6 |
| No `LICENSE` | absent at repo root | **Disclose** §7 (and decide a license) |
| No privacy policy / terms | no `PRIVACY*`, `TERMS*`, or linked policy | **Disclose** §5 |
| No dependency lockfile | manifest present (`package.json`, `requirements.txt`, …) but no lockfile | **Disclose** §6 (reproducibility/supply chain) |
| Auth provider unclear | no obvious auth lib/config, but app has accounts | **Disclose** §6 (how is auth done?) |
| Third-party data services | imports/config for DB, auth, analytics, email, payments (and an AI provider only if the app has an AI feature) | **Disclose** §4 (name where data flows) |
| Wide-open CORS | `Access-Control-Allow-Origin: *` on authed endpoints | **Fix or disclose** §6 |
| Public data store hints | DB rules like `allow read, write: if true`, public buckets | **Fix**, §6 |
| Debug/verbose in prod | `DEBUG = true`, stack traces exposed, console of secrets | **Fix**, §6 |

Keep the scan honest about its own limits. State plainly in the output:

> This is a shallow static scan. It cannot evaluate runtime security, auth
> correctness, access-control logic, or threat models. Absence of flags is not a
> clean bill of health.

---

## Part C - Best practices & next steps

Part B flags problems. **Part C is the guidance layer**: a library of
well-established best practices, each one *scannable* in a project, each mapped to
a concrete next step and a priority. It lives in [`BEST-PRACTICES.md`](BEST-PRACTICES.md).

When run inside a project, the agent scans for the current state of each
practice and emits a **prioritized "Recommended next steps" list** -
`NOW` (do before sharing), `SOON`, `LATER`. This is where "you're not meeting the
bar" becomes "here's the specific thing to do next."

**The guardrail still holds:** completing these steps improves your security
posture *and* gives you more to disclose honestly. It does **not** earn a
"secure" or "certified" stamp. The tool recommends; it never blesses.

---

## Part D - Common vibe-coding pitfalls

A focused pass over the *characteristic* ways AI-generated apps break - open
databases, secret keys shipped to the browser, frontend-only security, trusting
client-supplied IDs, placeholder code shipped as real. It lives in
[`PITFALLS.md`](PITFALLS.md). These are higher-signal than generic hygiene because
they're the exact traps an AI agent walks a non-traditional builder into, and
hits here usually belong at the top of the `NOW` list. Same guardrail: a hit is a
"go check this," not a verdict; a quiet scan is not proof of safety.

---

## Output contract

Whatever drives this rubric should produce, in order:

1. A one-line, bold disclaimer: *this measures disclosure completeness, not
   trustworthiness or security.*
2. The score - **always written with its definition welded on**, never as a bare
   number: *"Disclosure completeness: N of 8 sections answered - how much was
   disclosed, not whether the app is safe or good."* A decontextualized `8/8` gets
   screenshotted and waved around as a quality grade, so the definition must be
   inseparable everywhere the number appears (statement, output, any short form).
   Never put the score in a badge.
3. The filled-in **AI Diligence Statement** (see `templates/AI-DILIGENCE-STATEMENT.md`),
   to be published as `AI-DILIGENCE.md`.
4. The **disclose-or-fix punch list** from Part B + the **pitfalls** found in
   Part D. **Every scan result - especially a clean one - carries, inline, the
   absence-and-best-effort disclaimer:** *we checked for these specific common
   holes and didn't find them; this is a best-effort AI read of the code, not a
   security review, not exhaustive, varies run to run, and finding nothing is not
   a clean bill of health.* This must appear in the output AND in the statement's
   §6 self-check line - at the point of use, not only in these docs.
5. The **prioritized "Recommended next steps"** from Part C and Part D
   (`NOW` / `SOON` / `LATER`), each with the concrete action.
6. The **short forms** - badge, embeddable blurb, forum one-liner (see
   `templates/SHORT-FORMS.md`).

Never emit a sentence that asserts the tool is safe, secure, or trustworthy.
Only ever assert what the builder disclosed.
