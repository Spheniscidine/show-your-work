# Short forms - badge, blurb, one-liner

The full `AI-DILIGENCE.md` is the artifact. These are the small, embeddable
pointers to it for places a full document won't fit.

> **What the badge does and doesn't mean.** It is a static markdown image (via
> shields.io) that you add to your own README. It says **"published"** - meaning
> *an AI Diligence Statement exists in this repo, click to read it.* That's all.
> Nobody verifies it, nothing is checked when you add it, and it is **not** a
> "secure" or "approved" stamp. It's a link with a label. The trust comes from
> the reader actually reading the statement - the badge just gets them there.

## 1. Badge (for the top of a README)

```markdown
[![AI Diligence: published](https://img.shields.io/badge/AI_Diligence-published-2ea44f)](./AI-DILIGENCE.md)
```

It says **published**, never "verified," "secure," or "trusted" - on purpose. It
links to the statement; the reader judges. If you'd rather not imply anything at
all, skip the badge and just link the file in plain text:

```markdown
🧾 [AI Diligence Statement](./AI-DILIGENCE.md) - how this app was built and run.
```

> **Never badge the completeness score.** A bare "8 / 8" or "Diligence: 8/8"
> reads as a grade the instant it's screenshotted, torn from its real meaning
> ("sections answered," not "trustworthy"). The badge says *published* for exactly
> this reason. If you ever mention the score in public, keep its definition welded
> on in the same breath - *"8 / 8 sections disclosed (a measure of how much I
> explained, not a safety rating)"* - never the number alone.

## 2. Short blurb (for an app footer or the top of a README)

```markdown
### Built with AI - here's the diligence

This app was built largely with AI assistance. I've published an honest
[AI Diligence Statement](./AI-DILIGENCE.md) covering what it is, what data it
handles, how it's built, and what I have and haven't secured. It describes how
the app works so you can decide whether to trust it - it does not claim to be
audited or guaranteed safe. Questions or concerns: <your contact>.
```

## 3. Forum / Reddit / launch one-liner

```text
Heads up that this is AI-assisted. Full AI Diligence Statement here: <link>.
It covers data handling, third parties, security status (including what I
haven't reviewed), and how to reach me. Happy to answer anything it doesn't.
```
