---
name: nextra-update
description: "Use when the user says \"sync docs\", \"update nextra\", \"同步文件\", \"docs 同步\", \"refresh app docs\", or otherwise wants to pull the latest user-facing docs from the mojo-apps nextra site into mojo-app-docs/references/. Compares, diff-previews, commits and pushes — does not change any text on the way through."
---

# nextra-update

Mirror the user-facing docs from the `mojo-apps` repo's `nextra/content/` into this repo's `mojo-app-docs/references/`, so the bundled skill reflects what `docs.mojoapp.ai` is currently publishing.

## Prerequisites

- You are at the repo root: `agent-skills/` with `mojo-app-docs/references/` visible.
- The mojo-apps repo is checked out locally. By default it's expected at `/Users/Hana/Agents/mojo/repos/mojo-apps`. Override with `MOJO_APPS_PATH=/some/other/path` if needed.
- Working tree of `agent-skills` is clean.

If anything's missing, stop and tell the user.

## Step 1 — Make sure mojo-apps is up to date

```bash
MOJO_APPS="${MOJO_APPS_PATH:-/Users/Hana/Agents/mojo/repos/mojo-apps}"
NEXTRA="$MOJO_APPS/nextra/content"

if [ ! -d "$NEXTRA" ]; then
  echo "ERROR: $NEXTRA does not exist. Set MOJO_APPS_PATH or clone mojo-apps."
  exit 1
fi

# Bring nextra/content to the tip of its branch
git -C "$MOJO_APPS" fetch
git -C "$MOJO_APPS" status --short
```

If the mojo-apps working tree is dirty, ask the user whether they want to:

(a) sync from whatever's on disk right now (use as-is),
(b) `git stash` + `git pull` first, then sync from the freshly-pulled state.

Don't decide for them — local dirty trees often have in-progress edits.

## Step 2 — Record the source SHA (for the commit message)

```bash
NEXTRA_SHA=$(git -C "$MOJO_APPS" rev-parse --short HEAD)
echo "Source SHA: $NEXTRA_SHA"
```

## Step 3 — Mirror with rsync

```bash
TARGET="$(git rev-parse --show-toplevel)/mojo-app-docs/references"

rsync -av --delete \
  "$NEXTRA/" \
  "$TARGET/"
```

Notes on the flags:

- `--delete` removes files in `$TARGET` that no longer exist in `$NEXTRA`. This is what you want — the references should be an exact mirror, not an accumulation.
- The trailing slash on `$NEXTRA/` matters. With it, rsync copies the *contents* into `$TARGET/`. Without it, rsync creates `$TARGET/content/`.

## Step 4 — Diff preview

```bash
cd "$(git rev-parse --show-toplevel)"
git status --short mojo-app-docs/references
git diff --stat mojo-app-docs/references | tail -20
```

Show the user the file-count and line-count summary. If they want to see specific changes:

```bash
git diff mojo-app-docs/references/en/faq.mdx
```

## Step 5 — Confirm with the user before committing

The diff may be huge or zero. Two paths:

- **Zero changes** → `git diff --quiet` returns 0 → exit. Tell the user "already in sync, nothing to do."
- **Has changes** → show the user the summary, ask if they want to proceed.

## Step 6 — Commit

```bash
git add mojo-app-docs/references
git commit -m "docs(app-docs): sync from nextra @ $NEXTRA_SHA

Mirrored $MOJO_APPS/nextra/content/ → mojo-app-docs/references/.
$(git diff --cached --stat | tail -1)"
```

Use the user's normal author identity for this commit.

## Step 7 — Push (needs mojoapp-ai)

```bash
gh auth switch --user mojoapp-ai
git push origin main
gh auth switch --user hanamizuki
```

## Step 8 — Tell the user what happened

Report:

- The source SHA you synced from.
- File change count (e.g. "12 files changed, 84 insertions(+), 31 deletions(-)").
- Whether any whole files were added or removed.
- The new HEAD SHA on `agent-skills`.

## What this skill does NOT do

- It does not transform, translate, or edit the docs in any way. What's in `nextra/content/` lands in `references/` byte-for-byte.
- It does not run a release. The release skill handles versioning and changelog separately.
- It does not modify any other skill (`mojo-food-log`, `mojo-glp1-knowledge`, `.claude/`).

## When to stop and ask

- `MOJO_APPS_PATH` doesn't exist or doesn't look like the mojo-apps repo.
- Working tree of mojo-apps is dirty.
- Working tree of agent-skills is dirty.
- The diff is unexpectedly large (e.g. > 50 files), suggesting a structural change in nextra that may need its own design discussion.
- A whole language directory has disappeared (e.g. `zh-Hant-HK/` got dropped upstream) — confirm this is intentional before propagating.
