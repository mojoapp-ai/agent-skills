---
name: release
description: "Use when the user says \"release\", \"ship\", \"發版\", \"打 tag\", \"發新版本\", \"cut a release\", or wants to publish a new version of this repo. Cuts a SemVer-tagged release from the current main: classifies commits since the last tag, proposes a bump, updates CHANGELOG.md, creates the tag, pushes, and opens a GitHub Release."
---

# release

Cut a new release of `mojoapp-ai/agent-skills`. Designed to be run from inside the repo with Claude Code.

## Prerequisites

- You are at the repo root: `agent-skills/` with `skills/`, `.claude/`, `README.md`, `CHANGELOG.md` visible.
- Working tree is clean — no uncommitted changes (`git status` shows nothing).
- `gh` CLI is logged in for both `hanamizuki` (default) and `mojoapp-ai` accounts.

If any of the above fails, stop and tell the user what's wrong.

## Step 1 — Inspect what's changed

```bash
git fetch --tags
LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")
if [ -z "$LAST_TAG" ]; then
  echo "No previous tag. This will be the initial release."
  COMMITS=$(git log --pretty=format:"%h %s")
else
  echo "Last tag: $LAST_TAG"
  COMMITS=$(git log "$LAST_TAG..HEAD" --pretty=format:"%h %s")
fi
echo "$COMMITS"
```

## Step 2 — Classify commits and propose a bump

Walk through each commit subject:

| Pattern in subject | Category | Bump signal |
|---|---|---|
| `feat:` / `feat(...):` | Added | minor |
| `fix:` / `fix(...):` | Fixed | patch |
| `docs:` / `docs(...):` | Documentation | patch |
| `chore:` / `chore(...):` | (omit from changelog unless notable) | patch |
| `refactor:` | Changed | patch |
| `BREAKING CHANGE:` or subject contains `!:` | **breaking** | **major** |
| anything else | Other | patch |

**Proposed bump rule:**

- Any breaking change present → **major** (X.0.0)
- Else any `feat:` present → **minor** (0.X.0)
- Else → **patch** (0.0.X)

For pre-1.0 releases (`0.x.y`), demote one level:
- breaking → minor (0.X.0)
- feat → minor (0.X.0)
- fix/docs → patch (0.0.X)

Show the user the classification + proposed version and ask to confirm before continuing.

## Step 3 — Update CHANGELOG.md

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

Insert a new section just below the `## [Unreleased]` header (create both if missing). Example for `v0.2.0` on 2026-06-15:

```markdown
## [0.2.0] — 2026-06-15

### Added
- mojo-food-log: support importing fiber-only entries (#42)

### Fixed
- mojo-glp1-knowledge: corrected Wegovy half-life from 7d to 165h

### Documentation
- mojo-app-docs: synced from nextra @ a1b2c3d
```

Use the commit messages directly — don't editorialize. One bullet per commit. Strip the conventional-commit prefix (`feat:`, `fix:`, etc.) since the section already implies the category.

At the bottom of the file, maintain link references:

```markdown
[Unreleased]: https://github.com/mojoapp-ai/agent-skills/compare/v0.2.0...HEAD
[0.2.0]: https://github.com/mojoapp-ai/agent-skills/compare/v0.1.0...v0.2.0
[0.1.0]: https://github.com/mojoapp-ai/agent-skills/releases/tag/v0.1.0
```

## Step 4 — Commit the changelog

```bash
git add CHANGELOG.md
git commit -m "chore(release): v<X.Y.Z>"
```

Author email/name is whatever the user normally commits as. **Do not override with mojoapp-ai** for this commit — release commits in changelog history want a real human author.

## Step 5 — Tag

```bash
NEW_VERSION="v<X.Y.Z>"
git tag -a "$NEW_VERSION" -m "Release $NEW_VERSION

$(awk "/^## \[$NEW_VERSION/,/^## /" CHANGELOG.md | sed '$d')"
```

## Step 6 — Push (needs mojoapp-ai)

```bash
# Switch active account
gh auth switch --user mojoapp-ai

# Push main + tags
git push origin main
git push origin "$NEW_VERSION"

# Create GitHub Release page
gh release create "$NEW_VERSION" \
  --title "$NEW_VERSION" \
  --notes "$(awk "/^## \[$NEW_VERSION/,/^## /" CHANGELOG.md | sed '$d')"

# Switch back to user's default account
gh auth switch --user hanamizuki
```

## Step 7 — Verify

```bash
gh release view "$NEW_VERSION" --web    # optional: open in browser
gh api "/repos/mojoapp-ai/agent-skills/releases/latest" --jq '.tag_name, .html_url'
```

Report the release URL to the user.

## What this skill does NOT do

- It does not push features. The work should already be on `main` before you cut a release.
- It does not yank releases or rewrite tags. If you tagged the wrong commit, that's a separate fix-forward: cut the next patch immediately.
- It does not change any code outside `CHANGELOG.md`.

## When to stop and ask

- Working tree is not clean.
- Last tag's diff is empty (nothing to release).
- A commit message is ambiguous and you can't classify it confidently.
- The user wants a major bump but there's no breaking change in the log — push back.
