# Trim user/shared/CLAUDE.md to non-redundant rules only

Removed sections 2 ("Solve Exactly What Was Asked") and 3 ("Change Only What You Must") from the user-scope CLAUDE.md because every rule in those sections duplicates Claude Code's built-in system prompt almost verbatim. Keeping them wastes context budget and, per Anthropic's own guidance, lowers instruction compliance as instruction count grows.

## What changed

56 lines → 31 lines. Two sections removed, two kept:

| Removed | System prompt equivalent |
|---|---|
| "no adjacent features, no preemptive abstractions" | "Don't add features, refactor, or introduce abstractions beyond what the task requires" |
| "Prefer fewer lines" | "Don't over-engineer" |
| "Omit error handling for scenarios that cannot occur" | "Don't add error handling, fallbacks, or validation for scenarios that can't happen" |
| "Follow the existing style, naming, and patterns" | Edit tool built-in + "Avoid backwards-compatibility hacks" |
| "Clean up only what YOUR changes orphaned" | "A bug fix doesn't need surrounding cleanup" |
| "When you spot unrelated issues, mention them — leave untouched" | "Only make changes that are directly requested or clearly necessary" |

Kept: Section 1 "Surface Reasoning First" (escalation protocol) and Section 4 "Define Done Before Starting" (acceptance criteria) — neither has a system prompt counterpart.

## Why

- Anthropic best practices: "For each line, ask: 'Would removing this cause Claude to make mistakes?' If not, cut it."
- Research (dev.to): instruction compliance decays linearly as count grows; ~89% for specific rules, ~35% for vague ones.
- Official failure pattern: "The over-specified CLAUDE.md — important rules get lost in the noise."
- User-level CLAUDE.md purpose: personal preferences that differ from defaults, not restatements of defaults.

## References

- [Anthropic CLAUDE.md docs](https://code.claude.com/docs/en/memory)
- [Anthropic Best Practices](https://code.claude.com/docs/en/best-practices)
- [Claude Code System Prompts (Piebald-AI)](https://github.com/Piebald-AI/claude-code-system-prompts)
- [Streamlining user-level CLAUDE.md (dzombak.com)](https://www.dzombak.com/blog/2025/12/streamlining-my-user-level-claude-md/)
- [200 Lines of Rules Ignored (dev.to)](https://dev.to/minatoplanb/i-wrote-200-lines-of-rules-for-claude-code-it-ignored-them-all-4639)
