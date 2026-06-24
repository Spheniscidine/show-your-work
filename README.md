# Show Your Work

A self-assessment skill for people shipping vibe-coded apps — ordinary apps (a
tracker, a PM tool, a CRM) built largely with AI, by builders who may not come
from a traditional dev background. (Not for apps that *have AI features* in them
— that's a narrow subset; this is about how the app was *built*.)

It interviews you, takes a light look at your repo, and produces a clean,
publishable **AI Diligence Statement** (`AI-DILIGENCE.md`) you can paste into your
README, your app footer, your landing page, or the Reddit thread where someone
asks "is this safe to use?"

It does **not** certify that your app is trustworthy, secure, or safe. No tool
can do that from a code scan, and a false stamp of approval is more dangerous
than no stamp at all. What this measures is something real and verifiable:
**how completely you have disclosed how your thing is built and run.** The
reader decides whether to trust it. Your job is to give them enough to decide
honestly.

> Transparency is checkable. Trustworthiness is not. This tool only does the
> checkable part.

---

## Why this exists

There's a wave of AI-assisted tools shipping right now, and a recurring,
reasonable question from the people asked to use them:

1. **Is it solid?** Does it actually work, or fall over?
2. **Is my data safe?** What's collected, where does it go, who can see it?
3. **Does the person behind it understand what they shipped?**

Most builders answer these badly or not at all — not out of malice, but because
nobody handed them the questions. This hands them the questions, helps them
answer honestly (including "I don't know yet"), and formats the result so a
stranger can read it in 30 seconds.

The honest answer "this is a hobby project, data is in Supabase, I haven't had a
security review, here's my email" builds *more* trust than silence or a vague
"we take your privacy seriously."

---

## What it produces

- **`AI-DILIGENCE.md`** — the headline deliverable: a standardized, skimmable
  **AI Diligence Statement** you publish on your repo or app.
- **Short forms to embed it** — a badge, a footer/README blurb, and a forum/launch
  one-liner that all point back to the full statement.
- **A "Transparency Completeness" score** — e.g. *8 / 8 sections disclosed*.
  This scores *whether you answered*, not *whether the answer is good*. "No
  privacy policy, and here's why" counts as a complete disclosure.
- **A "disclose or fix" punch list** — light hygiene signals found in your repo
  (a committed `.env`, no `LICENSE`, hardcoded-looking keys), framed as things
  to either fix or be upfront about. Never a pass/fail grade.
- **A common-vibe-coding-pitfalls pass** — the high-signal one. It looks for the
  *characteristic* ways AI-generated apps break: a wide-open database, a secret
  key shipped to the browser, security that only exists in the frontend, the
  server trusting IDs from the client, placeholder code shipped as real.
- **Prioritized "Recommended next steps"** — the part that actually *guides* you.
  It scans your project against a library of well-established best practices
  (per-user data isolation, committed lockfiles, a privacy policy, export/delete,
  disclosing your third parties, …) and hands you a
  `NOW` / `SOON` / `LATER` list with the concrete action for each — leading with
  the one step that most reduces your risk.

---

## Install (Claude Code)

Copy the skill into your project or your home skills directory:

```bash
# project-local
mkdir -p .claude/skills
cp -r skill/show-your-work .claude/skills/

# or available everywhere
cp -r skill/show-your-work ~/.claude/skills/
```

Then run it:

```
/show-your-work
```

## Use with any other AI tool (Cursor, Windsurf, Copilot, etc.)

The skill is just a wrapper around plain-markdown files. Point your agent at
[`RUBRIC.md`](RUBRIC.md) and tell it:

> Walk me through RUBRIC.md section by section, scan my repo for the hygiene
> signals, best practices (BEST-PRACTICES.md), and pitfalls (PITFALLS.md) it
> references, then fill out templates/AI-DILIGENCE-STATEMENT.md with my answers
> and save it as AI-DILIGENCE.md.

No Claude Code required.

---

## What's in here

| File | What it is |
|------|------------|
| `RUBRIC.md` | The portable core: the 8 disclosure sections + the hygiene signals. Tool-agnostic. |
| `BEST-PRACTICES.md` | The guidance engine: scannable best practices → prioritized next steps. |
| `PITFALLS.md` | Common vibe-coding pitfalls — the characteristic AI-generated-app failures, scannable. |
| `templates/AI-DILIGENCE-STATEMENT.md` | The blank AI Diligence Statement to fill in (publish as `AI-DILIGENCE.md`). |
| `templates/SHORT-FORMS.md` | The badge, embeddable blurb, and forum one-liner that point to it. |
| `skill/show-your-work/SKILL.md` | The Claude Code skill that drives the interview and scan. |
| `examples/EXAMPLE-AI-DILIGENCE.md` | A filled-in example for a fictional vibe-coded app. |

---

## Design principles (so it stays honest)

- **It never says you're trustworthy or secure.** It says what you disclosed.
- **It never invents answers for you.** If you don't know, the output says you
  don't know. That's a valid, honest disclosure.
- **Hygiene findings are signals, not verdicts.** "We found a committed `.env`"
  is a thing to fix or disclose, not a failing grade.
- **It guides, it doesn't bless.** The next-steps engine recommends concrete
  improvements, but doing them earns a better disclosure — never a "secure" stamp.
- **The author is an interested party.** This tool was built by someone who also
  ships an AI-assisted tool. It deliberately can't hand anyone (including its
  author) a "trusted" badge — only a "here's what they told you" one.

---

## License

MIT. Take it, fork it, ship a better one. See [`LICENSE`](LICENSE).
