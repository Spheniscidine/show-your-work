# Common Vibe-Coding Pitfalls

These are the *characteristic* ways AI-generated apps go wrong - the traps an AI
coding agent walks a non-traditional builder into again and again. They're worth
their own pass because they're (a) common, (b) often high-severity, and (c)
invisible to someone who can't yet read the code critically.

This is the educational heart of the tool: even when nothing is scannable, naming
these traps teaches the builder what to look for and what to disclose.

> **Guardrail.** Finding a pitfall is **not** a verdict that the app is unsafe;
> *not* finding one is **not** a clean bill of health (detection is shallow and
> heuristic, and several of these can't be scanned at all). Frame every hit as
> "here's a known trap - go check this specific thing," with the fix and which
> disclosure section it touches. Recommend; never bless. Never tell the builder
> their app is secure.

Each pitfall: **what it is → why AI causes it → how to check → what to do (§ it
touches)**. Severity-ordered.

---

## Security traps (these leak or expose data)

### P1. The database is wide open (no row-level security)
- **What:** every user can read or write every other user's data.
- **Why AI causes it:** agents scaffold a database and leave the default policies
  open (`allow ... if true`, RLS disabled) because that makes the demo "just work."
- **Check:** Supabase - is RLS enabled and are there real policies, or
  `using (true)`? Firestore/Storage - `rg -n 'allow read, write: if true'`.
  Custom API - do reads/writes filter by the *authenticated* user?
- **Do:** enable RLS / write per-user rules / add `where owner = <session user>`.
  This is the #1 cause of vibe-coded data leaks. (§6) - usually a **NOW**.

### P2. A secret key is shipped to the browser
- **What:** a server-only key (DB service-role key, payment secret, admin token)
  ends up in client-side code, where anyone can read it in devtools.
- **Why AI causes it:** agents prefix vars to make them "available on the
  frontend" (`NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`) without realizing that bundles
  them publicly, or hardcode a key to get past an error.
- **Check:** `rg -n -e '(NEXT_PUBLIC_|VITE_|REACT_APP_|EXPO_PUBLIC_)[A-Z_]*(SECRET|SERVICE_ROLE|PRIVATE|ADMIN|TOKEN)' -e 'service_role'`
  and look for secret keys referenced in client/components folders.
- **Do:** move it server-side, **rotate the exposed key**, never expose a
  service-role/admin secret to the client. (§6) - **NOW** if real.

### P3. Security lives only in the frontend
- **What:** the UI hides a button or a route, but the API behind it has no check -
  so anyone who calls the endpoint directly gets in.
- **Why AI causes it:** agents implement the visible behavior (conditional
  rendering, route guards) and skip the invisible server-side authorization.
- **Check:** for each protected action, is there a server-side check, or only a
  React/route guard? Look for API handlers with no auth/session check.
- **Do:** enforce auth and ownership on the server for every sensitive action;
  treat the client as untrusted. (§6) - **NOW/SOON**.

### P4. The server trusts IDs from the client (IDOR)
- **What:** an endpoint reads `userId`/`teamId` from the request body or URL and
  returns that record - so changing the number returns someone else's data.
- **Why AI causes it:** it's the most obvious way to wire a handler, and the demo
  works because you only ever pass your own id.
- **Check:** `rg -n -e 'body\.(userId|user_id|accountId|teamId)' -e 'params\.(userId|id)'`
  then see whether the handler uses that value for access *instead of* the
  session identity.
- **Do:** derive the user from the session, never from client input; scope every
  query to it. (§6) - **NOW/SOON**.

### P5. Committed or hardcoded secrets
- **What:** a `.env` is tracked in git, or a key is pasted into source.
- **Why AI causes it:** agents create `.env` files and don't always gitignore
  them, or inline a key to unblock themselves.
- **Check:** `git ls-files | rg -i '(^|/)\.env'` and the secret-pattern grep from
  `BEST-PRACTICES.md` A1.
- **Do:** untrack, **rotate**, gitignore, use the host's secret store. (§6) - **NOW**.

### P6. Wide-open CORS or a public storage bucket
- **What:** `Access-Control-Allow-Origin: *` on authed APIs, or a file bucket set
  to public, exposing uploads.
- **Why AI causes it:** the permissive setting is the fastest way past a CORS or
  upload error.
- **Check:** `rg -n -e 'Access-Control-Allow-Origin.{0,5}\*' -e 'public: true' -e 'makePublic'`
- **Do:** restrict origins; make buckets private with signed access. (§6) - **SOON**.

### P7. Debug mode, verbose errors, or secrets in logs in production
- **What:** stack traces and internal details shown to users, or secrets/PII
  printed to logs.
- **Why AI causes it:** debugging output is left on after "getting it working."
- **Check:** `rg -n -e 'DEBUG\s*=\s*true' -e 'console\.log' -e 'printStackTrace' -e 'NODE_ENV.*development'`
  in server code; look for logging of tokens, emails, passwords.
- **Do:** disable debug in prod, return generic errors, strip sensitive logging. (§6) - **SOON**.

### P8. Hallucinated or sketchy dependencies
- **What:** packages an AI invented or guessed at - non-existent, typo-squatted,
  or unmaintained - pulled in and trusted.
- **Why AI causes it:** models confidently import plausible-sounding package names
  ("slopsquatting"), and unused/oversized deps accumulate.
- **Check:** skim the manifest for packages you don't recognize; confirm each
  exists and is maintained; run `npm audit`/`pip-audit` if available; ensure a
  lockfile is committed.
- **Do:** remove unknown/unused deps, pin versions, commit the lockfile. (§6) - **SOON**.

---

## Honesty & durability traps (these mislead the user or you)

### P9. Placeholder / mock code shipped as if it's real
- **What:** stubbed functions, fake/seeded data presented as live, default admin
  credentials, test backdoors, or half-built features that look finished.
- **Why AI causes it:** agents scaffold with mocks and placeholders to make a
  flow demoable, and they don't always get replaced.
- **Check:** `rg -n -e 'TODO' -e 'FIXME' -e 'NotImplemented' -e 'mock' -e 'placeholder' -e 'dummy' -e 'password123' -e 'admin@admin' -e 'changeme' -e 'test@test'`
- **Do:** replace mocks with real logic, remove default creds and backdoors, and
  honestly disclose what's still stubbed. (§3, §8) - **SOON**.

### P10. No separation between dev and production
- **What:** one database and one set of keys for everything; you test against real
  user data; a mistake in dev corrupts prod.
- **Why AI causes it:** a single environment is simpler to scaffold, so that's
  what gets built.
- **Check:** mostly ask - is there a separate prod DB/keys? Any `.env.production`
  vs `.env` distinction?
- **Do:** separate prod from dev data and credentials before real users arrive. (§6) - **SOON**.

### P11. Personal data flowing into logs or analytics
- **What:** emails, names, tokens, or message contents sent to analytics or
  written to logs without anyone deciding that on purpose.
- **Why AI causes it:** broad event logging is added for "observability" without
  data-minimization.
- **Check:** look at what's passed to analytics calls and loggers; flag PII.
- **Do:** minimize what's logged/tracked; disclose what analytics collects. (§4) - **SOON**.

### P12. Believing the AI when it says "this is secure"
- **The meta-pitfall.** The agent reassured the builder the code was safe, and -
  with no way to check - they believed it. Every pitfall above is one an AI may
  have *told you* it handled.
- **Why it matters:** an AI's confidence is not evidence. The whole point of this
  tool is to replace "the AI said it's fine" with "here's what I actually checked
  and what I haven't."
- **Do:** treat AI safety claims as unverified; get the high-severity items above
  reviewed by a person or tool; and in §6, disclose honestly that no independent
  review has happened (if true). This sentence builds more trust than any
  reassurance. - always relevant.

---

## How to use this pass

Run it alongside the Part C best-practice scan. Hits usually become the top of the
**`NOW`** list (P1, P2, P5 especially). For pitfalls that can't be scanned (P10,
P12, parts of P3/P4), raise them as questions in the interview and reflect the
answer in the disclosure.

### When the scan finds nothing - say this, every time (do not skip it)

A clean result is the single most dangerous output, because a naive builder reads
"no findings" as "I'm secure." So whenever this pass turns up nothing, the output
**must** spell out, in plain language and right next to the result, all of:

> ✅ Checked for a set of **common** vibe-coding holes (open database rules,
> secrets in the browser, frontend-only auth, server trusting client-supplied IDs,
> committed secrets) and didn't find them.
>
> This is **not** a security review and it is **not** exhaustive - it only looks
> for a few specific, well-known mistakes. It's a **best-effort read of the code
> by an AI tool**: it misses things, and it can give different results on
> different runs. **Finding nothing here does not mean the app is safe.**

Carry the same language into the published statement's §6 self-check line. Never
present a clean pitfalls result without it - a quiet scan is not proof of safety,
and the user only knows that if you tell them at the point they read the result.
