# Rollout Tracking — kagura-plugins

This file is the single place to see how the umbrella marketplace becomes fully live.
Each plugin's *implementation* is owned by its own repo/issue (linked below); this file
only tracks **dependency order** and the **go-live checklist** for the marketplace.

## Dependency order

1. **Live already:** `kagura-memory` (kagura-ai/memory-cloud), `kagura-code-reviewer`
   (kagura-ai/kagura-code-reviewer). No action.
2. **Add thin skill in own repo** (makes the reference resolvable):
   - `kagura-planner` — separate kagura-planner session
   - `kagura-engineer` — kagura-ai/kagura-engineer#28
3. **Migrate JFK → kagura-ai** (create repo, rebrand, deprecate, archive):
   - `kagura-c-suite` ← JFK/claude-c-suite-plugin#3 (rename + config fallback)
   - `kagura-phd-panel` ← JFK/claude-phd-panel-plugin#5 (rename + config fallback)
   - `gh-issue-driven` ← JFK/gh-issue-driven#85 (name kept)
4. **Per plugin, on landing:** run the go-live checklist below, flip README rollout 🚧→✅.
5. **Decommission:** archive the 3 JFK repos after their kagura-ai counterparts verify.

## Per-plugin status

| Plugin | Tier | Source repo | Issue | Status |
|---|---|---|---|---|
| kagura-memory | substrate | kagura-ai/memory-cloud | — | ✅ live |
| kagura-code-reviewer | tool | kagura-ai/kagura-code-reviewer | — | ✅ live |
| kagura-planner | tool | kagura-ai/kagura-planner | (planner session) | 🚧 pending |
| kagura-engineer | harness | kagura-ai/kagura-engineer | #28 | 🚧 pending |
| kagura-c-suite | tool | kagura-ai/kagura-c-suite | JFK c-suite #3 | 🚧 pending |
| kagura-phd-panel | tool | kagura-ai/kagura-phd-panel | JFK phd #5 | 🚧 pending |
| gh-issue-driven | harness | kagura-ai/gh-issue-driven | JFK ghid #85 | 🚧 pending |

## Go-live checklist (run when a pending plugin's repo lands)

- [ ] `/plugin install <name>@kagura-plugins` succeeds in a clean Claude Code session
- [ ] The plugin's namespace resolves (e.g. `/kagura-c-suite:ask`)
- [ ] Flip this file's status and `README.md` rollout row 🚧 → ✅
- [ ] Commit: `docs: <name> live in kagura-plugins`
- [ ] If it was a JFK migration, confirm the JFK repo carries its deprecation notice, then archive it
