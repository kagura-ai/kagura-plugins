# Rollout Tracking — kagura-plugins

This file is the single place to see how the umbrella marketplace becomes fully live.
Each plugin's *implementation* is owned by its own repo/issue (linked below); this file
only tracks **dependency order** and the **go-live checklist** for the marketplace.

> **Migration deferred (2026-06-08, post-`/ceo`).** A PM × CMO × CTO synthesis concluded the
> JFK→kagura-ai migration delivers zero user-facing value, and `reference-don't-vendor` works
> regardless of host org. So JFK-maintained plugins are referenced at their real `JFK/...`
> repos and **install today**. Migration (ownership move + the breaking `claude-* → kagura-*`
> rename, two independent steps) is **frozen until an external-launch trigger**. Issues
> #3 / #5 / #85 stay open as deferred — do **not** archive the JFK repos meanwhile.

## Current state

**All seven plugins install today.** No action is required to keep the marketplace working.

## Per-plugin status

| Plugin | Tier | Source repo (referenced) | Issue | Status |
|---|---|---|---|---|
| kagura-memory | substrate | kagura-ai/memory-cloud | — | ✅ live |
| kagura-code-reviewer | tool | kagura-ai/kagura-code-reviewer | — | ✅ live |
| kagura-engineer | harness | kagura-ai/kagura-engineer | #28 | ✅ live (PR #30 merged) |
| kagura-planner | tool | kagura-ai/kagura-planner | (planner session) | ✅ live |
| claude-c-suite | tool | JFK/claude-c-suite-plugin | #3 | ✅ live · migration deferred |
| claude-phd-panel | tool | JFK/claude-phd-panel-plugin | #5 | ✅ live · migration deferred |
| gh-issue-driven | harness | JFK/gh-issue-driven | #85 | ✅ live · migration deferred |

## When the migration trigger fires (deferred)

Two independent steps, smallest-blast-radius first:

1. **#85 gh-issue-driven** — ownership move only (no rename). Cheapest.
2. **#3 / #5** — ownership move **+** breaking `claude-* → kagura-*` rename + config-path
   fallback (`~/.claude/kagura-*.json`, falling back to `claude-*.json`). Update this
   marketplace's `source` + `name` for those entries, then archive the JFK repos after verify.
