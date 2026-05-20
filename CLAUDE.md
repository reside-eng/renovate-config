# CLAUDE.md

## What this repo is

`@side/renovate-config` holds Side's shared Renovate preset JSON files at the repo root (`default.json`, `library.json`, `service.json`, `ui.json`, `functions.json`, etc.). Every Side service, library, UI, and Cloud Function repo extends one of these via `extends: ["github>reside-eng/renovate-config:<preset>"]`, so any change here ripples out to dozens of repos. Edit accordingly.

## Working in this repo

- `yarn check` — Biome lint + format (single source of truth; no ESLint or Prettier).
- Every `*.json` (except `package.json`) must remain a valid Renovate config. CI runs `renovate-config-validator` against each preset; replicate locally with:
  ```bash
  for f in *.json; do
    [[ "$f" != "package.json" ]] && npx --yes --package renovate -- renovate-config-validator "$f"
  done
  ```
- Conventional Commits are enforced by Lefthook's `commit-msg` hook. If a hook fails, fix the issue rather than skipping with `--no-verify`.

## Atlassian

Use cloud ID `residenetwork.atlassian.net` for all Atlassian MCP calls.
