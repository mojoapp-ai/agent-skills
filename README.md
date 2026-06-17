<p align="center">
  <img src="assets/mojo-icon.png" alt="mojo" width="120" />
</p>

<h1 align="center">mojo agent skills</h1>

<p align="center">
  Drop-in skills that teach any AI agent to be useful for <a href="https://mojoapp.ai">mojo</a> users on a GLP-1 weight loss journey.
</p>

---

[mojo](https://mojoapp.ai) is a weight-loss tracker purpose-built for GLP-1 medications (Mounjaro, Ozempic, Wegovy, Zepbound). This repository ships three small **skills** you can drop into your own AI agent (Claude Code, OpenClaw, or any runtime that loads `SKILL.md` files).

Each skill is plain text. No API calls. No telemetry. Nothing happens to your mojo account unless you tap a link the skill generates.

## The skills

### [`/mojo-food-log`](skills/mojo-food-log/) — analyze a meal, log it to mojo

When you upload a food photo or describe what you ate, the agent acts as a registered dietitian — estimates calories + macros + fiber, presents a nutrition table, and produces a one-tap **Import to mojo** link.

> **You:** I had a chicken bento for lunch — rice, fried thigh, green vegetables.
>
> **Agent:** *[nutrition table with per-item breakdown, confidence range, cooking method adjustments…]*
> [Import to mojo](https://api.mojoapp.ai/nutrition/add?calories=735&...)

The skill handles language detection, meal type inference (from explicit mention or time-of-day fallback), hidden calorie checks, and link generation — all in one flow.

### [`/mojo-glp1-knowledge`](skills/mojo-glp1-knowledge/) — your knowledgeable peer for GLP-1 questions

When you ask about Mounjaro, Ozempic, Wegovy, Zepbound, Saxenda, Rybelsus, or GLP-1 in general (瘦瘦針 / 減肥針 / 瘦瘦筆), the agent answers thoughtfully — drawing on a curated knowledge base plus its own medical training.

> **You:** Is the 10 mg Mounjaro KwikPen really only 10 mg total?
>
> **Agent:** No — 10 mg is per *weekly* dose. The pen holds 4 × 10 mg doses = 40 mg total. *[…explains pen geometry, corrects the common misconception, and adds one safety reminder…]*

Includes corrections for common misconceptions (dose vs. pen total, needle reuse, residual medication) and always pairs actionable advice with a clear disclaimer.

### [`/mojo-app-guide`](skills/mojo-app-guide/) — the mojo app user guide

The full user-facing documentation site (the same one at [docs.mojoapp.ai](https://docs.mojoapp.ai)) is bundled in four languages (`en`, `zh-TW`, `zh-Hant-HK`, `zh-Hans`). When you ask "how do I cancel my subscription", "how does the streak counter work", or "where do I enter my body fat", the agent reads the matching page and answers in your language.

---

## Install

Copy one of the prompts below and paste it to your AI agent (Claude Code, OpenClaw, or any agent that loads `SKILL.md` files).

**Install all three skills:**

```
Install the mojo agent skills from https://github.com/mojoapp-ai/agent-skills
```

**Or install one at a time:**

```
Install the mojo food log skill from https://github.com/mojoapp-ai/agent-skills/tree/main/skills/mojo-food-log
```

```
Install the mojo GLP-1 knowledge skill from https://github.com/mojoapp-ai/agent-skills/tree/main/skills/mojo-glp1-knowledge
```

```
Install the mojo app guide skill from https://github.com/mojoapp-ai/agent-skills/tree/main/skills/mojo-app-guide
```

---

## Privacy

- ❌ No API calls.
- ❌ No telemetry. No usage reporting.
- ❌ No data leaves your agent, ever.
- ✅ Each skill is one or more markdown files. You can read every byte before you install.

---

## Get mojo

- iOS: <https://apps.apple.com/app/id6751704399>
- Android: <https://play.google.com/store/apps/details?id=com.hanamizuki.mojo>
- Web: <https://mojoapp.ai>
- Docs: <https://docs.mojoapp.ai>

---

## License

[MIT](LICENSE). Use it, fork it, embed it, modify it. Underlying user-docs content (in `skills/mojo-app-guide/references/`) is © mojoapp.ai and bundled here under permission.
