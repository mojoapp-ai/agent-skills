# Changelog

All notable changes to this repository are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- `.claude/skills/release/` — internal skill that cuts a SemVer-tagged release from main, classifies commits, updates CHANGELOG.md, tags, pushes, and opens a GitHub Release.
- `.claude/skills/nextra-update/` — internal skill that mirrors `mojo-apps/nextra/content/` into `mojo-app-docs/references/`.

### Changed
- `mojo-glp1-knowledge/references/mounjaro-kwikpen.md` — rewritten in English with inline Traditional / Simplified Chinese glosses on first occurrence (brand name, generic name, patient-facing terms). Same factual content, internationalised for English-speaking agents reading the reference.

[Unreleased]: https://github.com/mojoapp-ai/agent-skills/compare/HEAD...HEAD
