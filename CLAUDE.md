# coding-agent-settings — Agent operations

This repo manages Claude Code settings across User and Project scopes, with provenance tracking for items adopted from external repos. Most changes happen through Claude Code / agent sessions — the conventions below codify what's converged on so far. Read these before making changes.

## Repo at a glance

- `user/{shared,mac,linux}/` — User-scope settings; symlinked into `~/.claude/` by `./install.sh`.
- `project-templates/{_base,nodejs,python,go}/` — Templates copied into new projects via manual `cp -r` (no scaffold script).
- `bin/adopt`, `bin/sources-index` — Provenance tooling. JSON sidecars + auto-generated `SOURCES.md`.
- `docs/PROVENANCE.md` — Schema and edge cases for provenance.

## Workflows

### Adopt an external item

1. `bin/adopt --from <url> --commit <sha> --path <p> --to <dest> --mode copied|inspired-by --license <SPDX> [--notes "..."]`
2. `bin/sources-index`
3. Review the diff and the new `.provenance.json` sidecar.
4. Commit using the `git commit ... --trailer ...` form `bin/adopt` printed (include `SOURCES.md` in the same commit).
5. Push only on explicit user request.

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

## References

- [`README.md`](README.md) — install workflow, scope model, OS handling
- [`docs/PROVENANCE.md`](docs/PROVENANCE.md) — provenance schema, conventions, edge cases
- `bin/adopt --help` — adoption CLI reference
