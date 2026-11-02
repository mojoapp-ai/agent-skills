# Changelog

All notable changes to this repository are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- `skills/mojo-ai-agent/` — turns photos, InBody / smart-scale screenshots, and descriptions into mojo import links for all seven record types (meals, body measurements, visceral fat, waist, water, GLP-1 injections, symptoms). Each link is given twice, as a URL in a code block to paste into mojo and as a tappable link, with Gemini-specific hint lines and free-text encoding.
- `.claude/skills/release/` — internal skill that cuts a SemVer-tagged release from main, classifies commits, updates CHANGELOG.md, tags, pushes, and opens a GitHub Release.
- `.claude/skills/nextra-update/` — internal skill that mirrors `mojo-apps/nextra/content/` into `skills/mojo-app-docs/references/`.

### Changed
- `skills/mojo-food-log` — encoding rules hardened against LLM mojibake: URL-encode `foodName` with a real code tool (or write non-ASCII raw as fallback), never hand-write `%XX` bytes from memory; `foodName` moved to the last query parameter so numeric params survive link truncation. Same root cause as fixed in mojo-apps prompts (PR #623 there).
- `skills/mojo-glp1-knowledge/references/mounjaro-kwikpen.md` — rewritten in English with inline Traditional / Simplified Chinese glosses on first occurrence (brand name, generic name, patient-facing terms). Same factual content, internationalised for English-speaking agents reading the reference.

### Added (knowledge references)
- `skills/mojo-glp1-knowledge/references/ozempic-knowledge.md` — Ozempic (胰妥讚 / 诺和泰) consolidated reference (semaglutide, T2D + CV/renal). Consolidated across FDA DailyMed, EMA SmPC, TFDA records, and community pattern review; reconciled US vs EMA storage windows.
- `skills/mojo-glp1-knowledge/references/wegovy-knowledge.md` — Wegovy consolidated reference (semaglutide for chronic weight management; single-dose pens).
- `skills/mojo-glp1-knowledge/references/rybelsus-knowledge.md` — Rybelsus (瑞倍適) consolidated reference (oral semaglutide tablet; strict empty-stomach + 30-min wait protocol).
- `skills/mojo-glp1-knowledge/references/zepbound-knowledge.md` — Zepbound consolidated reference (tirzepatide for chronic weight management + OSA; same active as Mounjaro but distinct label).
- `skills/mojo-glp1-knowledge/references/saxenda-knowledge.md` — Saxenda (善纖達) consolidated reference (liraglutide, once-daily multi-dose pen; ~13h half-life vs weekly agents).

[Unreleased]: https://github.com/mojoapp-ai/agent-skills/compare/HEAD...HEAD
