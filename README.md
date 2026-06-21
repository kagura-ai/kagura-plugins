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
├─ Tier 1 · Tools ──── claude-c-suite · claude-phd-panel · kagura-cli · kagura-code-reviewer · kagura-planner
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
| **kagura-cli** | `/kagura-cli:setup` · `:auth` · `:doctor` · `:ingest` · `:files` · `:resource` | Thin skills over the Kagura Memory **CLI** (`kagura`) — auth/setup/doctor, document ingestion, resource tokens, R2 file uploads. The substrate's operational companion (distinct from the `kagura-memory` MCP plugin). |
| **kagura-code-reviewer** | `/kagura-code-reviewer:…` | Cost-free, Ollama-first code review — multi-angle finders, adversarial verify, structured verdict. |
| **kagura-planner** | `/kagura-planner:plan` | Memory-grounded PLAN layer. Thin wrapper over the `kagura-planner` CLI (config discovery → `doctor` → `plan`). |
| **claude-c-suite** | `/claude-c-suite:ask` · `:ceo` · `:cto` · … | Executive-team review lenses for any codebase. `ask` routes to the single best CxO; `ceo` synthesizes across roles. |
| **claude-phd-panel** | `/claude-phd-panel:cs` · `:db` · `:sec` · … | PhD-level deep technical critique (CS, DB, Distributed Systems, Data Science, PL, Security, Statistics). |

## Tier 2 — Harnesses (multi-phase · stateful · costly)

> ⚠️ **Harnesses are not atomic tools.** They run loops, hold resumable state, **mutate the
> repo, open PRs, and gate on HITL**, and can consume significant tokens. Reach for them
> deliberately.

| Plugin | Invoke | What it's for |
|---|---|---|
| **kagura-engineer** | `/kagura-engineer:run` · `:review` · `:goal` | Autonomous coding loop over Claude Code + Kagura Memory. Drives a task/issue to a PR. |
| **gh-issue-driven** | `/gh-issue-driven:start` · `:ship` · `:goal` · … | Drives GitHub issues to PRs (start → implement → ship → review) with gates and HITL. |

## Brand & ownership

All plugins are first-party. The `kagura-*` tools live under the **kagura-ai** org;
`claude-c-suite`, `claude-phd-panel`, and `gh-issue-driven` are currently **JFK-maintained**
(`github.com/JFK/...`) and the marketplace references them there — `reference-don't-vendor`
works regardless of which org hosts the source, so these install today exactly like the rest.

**Migration to kagura-ai is deferred, not cancelled.** It is treated as two independent steps:

1. **Ownership move** (JFK → kagura-ai org) — cheap, reversible.
2. **Prefix rename** — *if/when* the move happens, the `claude-*` tools would be renamed into
   the `kagura-*` namespace. A breaking namespace change plus a config-path migration — the
   expensive part, and the reason it stays deferred.

Both deliver brand coherence but **zero user-facing functionality**, so they are gated on an
external trigger (public launch / external contributors), not a self-imposed deadline. Until
then, JFK-maintained is the supported state.

## Rollout status

| Plugin | Tier | Status |
|---|---|---|
| kagura-memory | Substrate | ✅ live (`kagura-ai/memory-cloud`) |
| kagura-cli | Tool | ✅ live (`kagura-ai/kagura-memory-python-sdk`) |
| kagura-code-reviewer | Tool | ✅ live (`kagura-ai/kagura-code-reviewer`) |
| kagura-engineer | Harness | ✅ live (`kagura-ai/kagura-engineer`) |
| claude-c-suite | Tool | ✅ live (`JFK/claude-c-suite-plugin`) — migration deferred |
| claude-phd-panel | Tool | ✅ live (`JFK/claude-phd-panel-plugin`) — migration deferred |
| gh-issue-driven | Harness | ✅ live (`JFK/gh-issue-driven`) — migration deferred |
| kagura-planner | Tool | ✅ live (`kagura-ai/kagura-planner`) |

All eight plugins install today.

## Related — not Claude Code plugins

Some Kagura components are **runnable products, not `/plugin install`-able plugins**, so they
are intentionally *not* listed in the marketplace above. They ship no `.claude-plugin/`
manifest; you install and run them from their own repos.

| Component | What it is |
|---|---|
| **kagura-agent** | Memory-backed autonomous agent on the Claude Agent SDK / kagura-brain — Docker **security membrane**, per-task **credential leasing**, capability graduation, and a Slack/Discord cockpit. Runnable, Apache-2.0. Run from [`kagura-ai/kagura-agent`](https://github.com/kagura-ai/kagura-agent), **not** via `/plugin install`. |
