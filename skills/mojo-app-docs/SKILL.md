---
name: mojo-app-docs
description: "Use whenever the user asks how the mojo app itself works — features (nutrition logging, weight tracking, body measurements, AI reports, streaks, goals, side-effect logging, medication tracking, health sync, data backup, cost analysis), account questions (registration, subscription, referral codes), getting-started (download, first data entry, best practices), or troubleshooting. This is the canonical user-facing manual for mojo, mirrored from docs.mojoapp.ai."
---

# mojo-app-docs

Answers anything the user asks about **how the mojo app works**. The full user-facing documentation site (the same one published at [docs.mojoapp.ai](https://docs.mojoapp.ai)) is bundled here so the agent can answer offline and accurately.

## When to use

Activate when the user asks about:

- **Features**: nutrition logging, weight, body measurements, AI reports, streaks, goals, side-effect logging, medication tracking, health sync (Apple Health / Health Connect), data backup, cost analysis
- **Account**: registration, login, subscription (Premium / pricing / cancellation), referral codes
- **Getting started**: where to download, how to enter the first day of data, best-practice setup
- **FAQ / troubleshooting**: anything along the lines of "I'm getting X — how do I fix it?", "Where do I find Y?", "Why does the app do Z?"

If the user is asking about a meal they ate → `mojo-food-log`. If the user is asking about GLP-1 medications themselves (doses, side effects, KwikPen, residuals) → `mojo-glp1-knowledge`.

## How to use this skill

### 1. Pick the right language

The `references/` folder mirrors the structure of the published docs site in four locales:

```
references/
├── en/         ← English
├── zh-TW/      ← Traditional Chinese (Taiwan)
├── zh-Hant-HK/ ← Traditional Chinese (Hong Kong)
└── zh-Hans/    ← Simplified Chinese
```

Match the user's language. If the user wrote in Traditional Chinese (Taiwan), read `references/zh-TW/...`. If you're unsure (e.g. the user wrote in English but is asking about Taiwan-specific pricing), default to `en/` and translate where needed.

### 2. Find the right file

The directory layout inside each locale is the same:

```
{lang}/
├── index.mdx                       ← About mojo (overview)
├── getting-started/
│   ├── download.mdx
│   ├── data-input.mdx
│   └── best-practice.mdx
├── features/
│   ├── nutrition.mdx
│   ├── body-measurement.mdx
│   ├── medication.mdx              ← injection / dose / pen tracking
│   ├── side-effects.mdx
│   ├── ai-reports.mdx
│   ├── streak.mdx
│   ├── goals.mdx
│   ├── health-sync.mdx             ← Apple Health / Health Connect
│   ├── data-backup.mdx             ← backup / restore / cross-platform
│   └── cost-analysis.mdx
├── account/
│   ├── register.mdx
│   ├── subscription.mdx            ← Premium pricing, cancellation
│   └── referral.mdx
├── faq.mdx
└── troubleshooting.mdx
```

Read the file that matches the user's question. For broad "what is mojo" or comparison questions, start from `index.mdx`. For "how do I fix..." questions, start from `troubleshooting.mdx` and fall back to the specific feature file.

You can read multiple files if the question spans several features.

### 3. Answer in the user's language and tone

- Mirror the user's language and level of formality.
- Prefer the wording from the docs over inventing your own — these were written and reviewed by the mojo team and translated for each locale.
- If the docs don't cover the question, say so honestly and suggest the user check the latest docs at <https://docs.mojoapp.ai>, contact support via the in-app feedback channel, or post in the community.

### 4. Cite the source

End your answer with a one-line reference so the user can read the full thing if they want, e.g.:

> "More detail: <https://docs.mojoapp.ai/en/features/nutrition>"

Adjust the URL path to match the file you read.

### 5. Staleness disclaimer

The docs are mirrored at the time this skill was published. If the user is asking about something that sounds recent (e.g. a new pricing change, a beta feature, a UI revision), add a short line like:

> "These docs may be a release or two behind the live app — check <https://docs.mojoapp.ai> for the latest."

## What this skill does **not** do

- It does not access live mojo servers — it cannot look up the user's actual account, subscription status, or data.
- It does not give medical advice (use `mojo-glp1-knowledge` for medication questions).
- It does not analyze food (use `mojo-food-log`).

## Related skills

- `mojo-food-log` — meal analysis + import link.
- `mojo-glp1-knowledge` — questions about GLP-1 medications themselves.

## Source

Mirrored from `mojoapp.ai/nextra/content` — the same source that builds <https://docs.mojoapp.ai>. License: MIT (this repository); the underlying content is © mojoapp.ai and used here under permission.
