# kagura-plugins

> One entry point for the **Kagura Memory Cloud** ecosystem — a memory substrate, a set of
> atomic review **Tools**, and a couple of orchestration **Harnesses**, unified under the
> **kagura-ai** brand.

`kagura-plugins` is a **curated marketplace**, not a code dump. It follows
**reference-don't-vendor**: every plugin lives in its own `kagura-ai` repo and is
*referenced* here. Adding this one marketplace gives you the whole ecosystem, while each
plugin keeps a single canonical source.

## The map

```
┌─ Tier 2 · Harness ── gh-issue-driven · kagura-engineer
│     multi-phase · stateful/resumable · drives loops · creates PRs · HITL gates · costly
│        consumes ↓
├─ Tier 1 · Tools ──── kagura-c-suite · kagura-phd-panel · kagura-code-reviewer · kagura-planner
│     single-purpose · cheap · safe · ~stateless: invoke → result
│        grounded ↓
└─ Substrate ───────── kagura-memory  (Memory Cloud)
      session-start · session-summary · recall · remember · guide · smoke-test
      the shared memory everything grounds in
```

Read it bottom-up: **memory grounds everything**, **tools** give you a single sharp answer,
**harnesses** stand on top of the tools and drive a whole workflow.

## Install

```text
/plugin marketplace add kagura-ai/kagura-plugins
/plugin install kagura-memory@kagura-plugins        # start with the substrate
/plugin install kagura-code-reviewer@kagura-plugins # add the tools you want…
```

Update the catalog later with `/plugin marketplace update`.

## Substrate — start here

| Plugin | Invoke | What it's for |
|---|---|---|
| **kagura-memory** (Memory Cloud) | `/kagura-memory:session-start` · `:session-summary` · `:recall` · `:remember` · `:guide` | Persistent memory that grounds the ecosystem. Bracket every working session: `session-start` resumes context in, `session-summary` saves knowledge out; `recall`/`remember` throughout. |

This is the connective tissue — most other plugins are more useful with memory underneath.

## Tier 1 — Tools (atomic · cheap · safe)

| Plugin | Invoke | What it's for |
|---|---|---|
| **kagura-code-reviewer** | `/kagura-code-reviewer:…` | Cost-free, Ollama-first code review — multi-angle finders, adversarial verify, structured verdict. |
| **kagura-planner** | `/kagura-planner:plan` | Memory-grounded PLAN layer. Thin wrapper over the `kagura-planner` CLI (config discovery → `doctor` → `plan`). |
| **kagura-c-suite** | `/kagura-c-suite:ask` · `:ceo` · `:cto` · … | Executive-team review lenses for any codebase. `ask` routes to the single best CxO; `ceo` synthesizes across roles. |
| **kagura-phd-panel** | `/kagura-phd-panel:cs` · `:db` · `:sec` · … | PhD-level deep technical critique (CS, DB, Distributed Systems, Data Science, PL, Security, Statistics). |

## Tier 2 — Harnesses (multi-phase · stateful · costly)

> ⚠️ **Harnesses are not atomic tools.** They run loops, hold resumable state, **mutate the
> repo, open PRs, and gate on HITL**, and can consume significant tokens. Reach for them
> deliberately.

| Plugin | Invoke | What it's for |
|---|---|---|
| **kagura-engineer** | `/kagura-engineer:run` · `:review` · `:goal` | Autonomous coding loop over Claude Code + Kagura Memory. Drives a task/issue to a PR. |
| **gh-issue-driven** | `/gh-issue-driven:start` · `:ship` · `:goal` · … | Drives GitHub issues to PRs (start → implement → ship → review) with gates and HITL. |

## Brand & canonical source

All plugins are first-party (Kagura AI / JFK) and **kagura-ai is the single canonical
source**. Former JFK-personal plugins (`claude-c-suite`, `claude-phd-panel`,
`gh-issue-driven`) are migrating to `kagura-ai`; the JFK repos are then frozen and archived.
As part of the move, `claude-c-suite → kagura-c-suite` and `claude-phd-panel →
kagura-phd-panel` (the invocation namespace is `<plugin>:<command>`, so the marketplace name
never appears — no "kagura" doubling). Old `~/.claude/claude-*.json` configs keep working
via fallback.

## Rollout status

| Plugin | Tier | Status |
|---|---|---|
| kagura-memory | Substrate | ✅ live (`kagura-ai/memory-cloud`) |
| kagura-code-reviewer | Tool | ✅ live (`kagura-ai/kagura-code-reviewer`) |
| kagura-planner | Tool | 🚧 plugin in preparation in `kagura-ai/kagura-planner` |
| kagura-engineer | Harness | 🚧 plugin in preparation — [kagura-ai/kagura-engineer#28](https://github.com/kagura-ai/kagura-engineer/issues/28) |
| kagura-c-suite | Tool | 🚧 migrating from `JFK/claude-c-suite-plugin` ([#3](https://github.com/JFK/claude-c-suite-plugin/issues/3)) |
| kagura-phd-panel | Tool | 🚧 migrating from `JFK/claude-phd-panel-plugin` ([#5](https://github.com/JFK/claude-phd-panel-plugin/issues/5)) |
| gh-issue-driven | Harness | 🚧 migrating from `JFK/gh-issue-driven` ([#85](https://github.com/JFK/gh-issue-driven/issues/85)) |

Entries marked 🚧 reference their target `kagura-ai` repo; installing them succeeds once that
repo's plugin lands. The catalog listing itself is unaffected in the meantime.

## Design

See [`docs/superpowers/specs/2026-06-08-kagura-plugins-design.md`](docs/superpowers/specs/2026-06-08-kagura-plugins-design.md).
