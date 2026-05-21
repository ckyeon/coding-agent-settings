# coding-agent-settings — Agent operations

This repo manages Claude Code settings across User and Project scopes, with provenance tracking for items adopted from external repos. Most changes happen through Claude Code / agent sessions — the conventions below codify what's converged on so far. Read these before making changes.

## Repo at a glance

- `user/{shared,mac,linux}/` — User-scope settings; symlinked into `~/.claude/` by `./install.sh`.
- `user/shared/plugins/` — Provenance-only for plugins installed via Claude Code's `/plugin install`. Not symlinked; see `docs/PROVENANCE.md` § "Tracking officially-installed plugins".
- `project-templates/{_base,nodejs,python,go,phaser}/` — Templates copied into new projects via manual `cp -r` (no scaffold script).
- `bin/adopt`, `bin/sources-index` — Provenance tooling. JSON sidecars + auto-generated `SOURCES.md`.
- `docs/PROVENANCE.md` — Schema and edge cases for provenance.

## Workflows

### Adopt an external item

1. `bin/adopt --from <url> [--commit <sha>] --path <p> --to <dest> --mode copied|inspired-by --license <SPDX> [--notes "..."]`. Omitting `--commit` pins to upstream HEAD. Auto-runs `bin/sources-index` unless `--no-index`.
2. Review the diff and the new `.provenance.json` sidecar.
3. Commit using the printed `git commit ... --trailer ...` form (include `SOURCES.md`), or pass `--commit-now` to step 1 to skip the copy-paste.
4. Push only on explicit user request.

### Track an officially-installed plugin

Claude Code marketplace plugins (installed via `/plugin install <name>@<marketplace>`) are activated by the plugin CLI, not by this repo's symlinks. To record provenance:

1. `mkdir -p user/shared/plugins/<name> && $EDITOR user/shared/plugins/<name>/README.md`
2. `bin/adopt --from <marketplace-repo-url> --path plugins/<name> --to user/shared/plugins/<name> --mode inspired-by --license <SPDX>`

`install.sh` / `uninstall.sh` print `/plugin install` reminders listing tracked plugins. Canonical examples: `user/shared/plugins/hookify/`, `user/shared/plugins/claude-md-management/`.

### Add a self-authored item

Just create the file or folder under `user/shared/{skills,commands,agents,hooks,rules,output-styles}/`. No sidecar — opt-in scope means "no provenance metadata" = "original work".

### Update an adoption to a newer upstream commit

Re-run `bin/adopt` against the same destination with the new SHA. The sidecar's `provenance[]` list gets a new entry appended; the older entry stays as history.

## Guardrails

- **No auto-commit.** Wait for an explicit commit request from the user.
- **`./install.sh` needs explicit confirmation.** It backs up existing `~/.claude/` content (including `skills/`, which the user has many of) before symlinking. Always explain the impact and confirm before running.
- **No `git config` changes** through tooling — including `--set-upstream`. The user manages remote configuration themselves.
- **No `git push` without an explicit request.**
- **Dependencies stay minimal**: Python 3 stdlib + bash 3.2 only. Don't introduce `yq`, `PyYAML`, `jq`, or other external deps.
- **Provenance lives in sidecars only.** Never inline in `SKILL.md` / agent / command / `CLAUDE.md` frontmatter — those files load into Claude's context when invoked, so embedded metadata becomes context noise.
- **Two `plugins/` directories — don't confuse them.** `~/.claude/plugins/` is Claude Code's own plugin CLI state (registry, marketplaces, cache); never symlinked from here. `user/shared/plugins/` in this repo is provenance-only — no source vendored; activation is via `/plugin install`.

## References

- [`README.md`](README.md) — install workflow, scope model, OS handling
- [`docs/PROVENANCE.md`](docs/PROVENANCE.md) — provenance schema, conventions, edge cases
- `bin/adopt --help` — adoption CLI reference
