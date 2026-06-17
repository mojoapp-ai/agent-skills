---
name: mojo-app-guide
description: "Use whenever the user asks how the mojo app works — features like nutrition logging, weight tracking, body measurements, AI reports, streaks, goals, medication tracking, health sync, or data backup. Also covers account questions (subscription, pricing, referral codes), getting started, and troubleshooting. Use this for any question about the mojo app's interface, functionality, or settings, even if the user doesn't say 'mojo' by name but is clearly asking about a GLP-1 tracking app."
---

# mojo-app-guide

Answer questions about the [mojo app](https://mojoapp.ai) — a weight-loss tracker built for GLP-1 journeys. The full user-facing documentation (the same content published at [docs.mojoapp.ai](https://docs.mojoapp.ai)) is bundled in `references/` in four languages.

## Step 1: Match the user's language

Read from the locale folder that matches how the user wrote:

| User language | Folder |
|---------------|--------|
| English | `references/en/` |
| 繁體中文（台灣） | `references/zh-TW/` |
| 繁體中文（香港） | `references/zh-Hant-HK/` |
| 简体中文 | `references/zh-Hans/` |

## Step 2: Find the right file

| Question about | File |
|----------------|------|
| What is mojo / overview | `index.mdx` |
| Download / install | `getting-started/download.mdx` |
| First data entry / setup | `getting-started/data-input.mdx` |
| Best practices | `getting-started/best-practice.mdx` |
| Nutrition logging | `features/nutrition.mdx` |
| Body measurements | `features/body-measurement.mdx` |
| Medication / injection tracking | `features/medication.mdx` |
| Side effects logging | `features/side-effects.mdx` |
| AI reports | `features/ai-reports.mdx` |
| Streaks | `features/streak.mdx` |
| Goals | `features/goals.mdx` |
| Apple Health / Health Connect sync | `features/health-sync.mdx` |
| Backup / restore / cross-platform | `features/data-backup.mdx` |
| Cost analysis | `features/cost-analysis.mdx` |
| Registration / login | `account/register.mdx` |
| Subscription / pricing / cancel | `account/subscription.mdx` |
| Referral codes | `account/referral.mdx` |
| General FAQ | `faq.mdx` |
| Troubleshooting | `troubleshooting.mdx` |

Read multiple files if the question spans several topics. For broad "what is mojo" questions, start from `index.mdx`.

## Step 3: Answer using the docs

- Prefer the wording from the docs over inventing your own — these were written and reviewed by the mojo team for each locale.
- If the docs don't cover the question, say so honestly and suggest checking [docs.mojoapp.ai](https://docs.mojoapp.ai) or using the in-app feedback channel.

## Step 4: Cite the source

End with a one-line reference so the user can read the full article:

> More detail: https://docs.mojoapp.ai/en/features/nutrition

Adjust the URL path to match the file you read.

## Staleness note

These docs are bundled at publish time. If the user asks about something that sounds very recent (a new feature, pricing change, UI update), add:

> "These docs may be a release behind the live app — check docs.mojoapp.ai for the latest."

## Scope

This skill answers questions about **using the app**. It cannot access the user's actual account, subscription, or data. It does not analyze meals or estimate nutrition. It does not give medical advice about GLP-1 medications.
