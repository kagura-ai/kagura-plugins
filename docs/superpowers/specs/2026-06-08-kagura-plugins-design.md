# kagura-plugins — Design Spec

**Date:** 2026-06-08
**Status:** Draft (awaiting author review)
**Repo:** **`~/works/kagura-plugins`** (the umbrella marketplace; supersedes the initial
empty `~/works/kagura-skills`)

---

## 1. Goal

Make the **Kagura Memory Cloud ecosystem comfortable to adopt and use** by giving it a
single umbrella entry point: **`kagura-plugins`**, a marketplace repo that bundles the
ecosystem's plugins so they can be discovered and installed from one place — and unifies
them under the **kagura-ai brand**.

This is *not* a "skills box" and *not* a vendored monorepo of everyone's code. It is a
thin **curated marketplace** plus the small amount of genuinely-missing first-party work.

## 2. Core principle — reference, don't vendor

> `kagura-plugins` holds **no plugin source of its own**. Each tool owns its plugin inside
> its own kagura-ai repo; `kagura-plugins` only **references** those repos via
> `marketplace.json`.

Rationale: every plugin in scope is first-party (authored by JFK / Kagura AI), and several
already exist as independent, well-formed kagura-ai repos with their own versioning and CI
(e.g. `kagura-code-reviewer`, `kagura-memory`). Physically copying them in would create a
second canonical source and a permanent version-sync debt. Referencing keeps a single
source of truth per plugin while still giving users "add one thing, get the whole map."

## 3. Ecosystem taxonomy (2 tiers + substrate)

The distinction below is **documentation + lightweight metadata**, not a physical split
(no separate folders / marketplaces). It matters because the two tiers differ in mental
model, cost, and risk.

```
┌─ Tier 2 — Harness ── gh-issue-driven · kagura-engineer
│      (multi-phase, stateful/resumable, drives loops, creates PRs, HITL gates, costly)
│        consumes ↓
├─ Tier 1 — Tools ──── claude-c-suite · claude-phd-panel · kagura-code-reviewer · kagura-planner:plan
│      (single-purpose, cheap, safe, ~stateless: invoke → result)
│        grounded ↓
└─ Substrate ───────── kagura-memory  (Memory Cloud)
       guide · recall · remember · session-start · session-summary · smoke-test
       (the shared memory layer everything grounds in)
```

- **Tier 1 — Tools:** atomic skills. Invoke → get an answer. Cheap, safe.
- **Tier 2 — Harness:** orchestrators. They *consume* Tier-1 tools, hold resumable state,
  run loops, mutate the repo, open PRs, and gate on HITL. Their descriptions must carry
  cost / repo-mutation / PR / HITL warnings so users never mistake them for atomic tools.
- **Substrate — `kagura-memory` (Memory Cloud):** the memory backbone the rest grounds in.
  It is not a passive store — it ships the **workflow skills that make the ecosystem
  comfortable**: `session-start` and `session-summary` bracket every working session
  (resume context in, save knowledge out), while `recall` / `remember` / `guide` are used
  throughout. These skills are the connective tissue of the whole map and must be featured
  prominently, not buried as "just the backend."

`kagura-planner` is classified **Tier-1 Tool** today (thin CLI wrapper). It may graduate to
Tier-2 Harness if it later drives multi-stage plan→engineer orchestration.

## 4. Plugin inventory & per-plugin plan

| Plugin | Current home | Tier | Action | In `kagura-plugins` |
|---|---|---|---|---|
| `kagura-memory` (Memory Cloud) | kagura-ai · plugin ✅ (guide/recall/remember/session-start/session-summary/smoke-test) | **Substrate** | none | **reference (featured)** |
| `kagura-code-reviewer` | kagura-ai · plugin ✅ | Tier 1 | none | **reference** |
| `kagura-planner` | kagura-ai · CLI, **no skill** | Tier 1 | add thin skill-plugin **in its own repo** | reference |
| `kagura-engineer` | kagura-ai · CLI, **no skill** | Tier 2 | add thin skill-plugin **in its own repo** | reference |
| `claude-c-suite` | **JFK** personal | Tier 1 | **migrate + rename → `kagura-c-suite`**, then JFK archive | reference |
| `gh-issue-driven` | **JFK** personal | Tier 2 | **migrate** (name kept) → kagura-ai, then JFK archive | reference |
| `claude-phd-panel` | **JFK** personal | Tier 1 | **migrate + rename → `kagura-phd-panel`**, then JFK archive | reference |

**Out of scope** (not core to the memory-cloud ecosystem; revisit later):
`expert-craft`, `claude-statusline-builder`, `claude-harness-loop`.

### 4.1 Plugin naming / namespace policy

**Invocation namespace is `<plugin-name>:<command>`, not `<marketplace>:<plugin>`.** The
marketplace name (`kagura-plugins`) never appears at call time. Evidence: marketplace
`kagura-memory-cloud` ships plugin `kagura-memory`, invoked as `/kagura-memory:recall` — the
marketplace name is invisible. So there is **no "kagura" doubling**; `kagura-` appears once,
in the plugin name.

Given that, the brand is unified at the **plugin-name prefix** (the user-facing namespace),
**rename done now as part of migration** (cheapest moment — the metadata/repo is already
being rewritten; deferring means paying the same churn plus a second breaking change later):

| Old plugin | New plugin / namespace | Config path |
|---|---|---|
| `claude-c-suite` | **`kagura-c-suite`** (`/kagura-c-suite:ceo`) | `~/.claude/kagura-c-suite.json` (fall back to old) |
| `claude-phd-panel` | **`kagura-phd-panel`** (`/kagura-phd-panel:cs`) | `~/.claude/kagura-phd-panel.json` (fall back to old) |
| `gh-issue-driven` | unchanged (`gh` = GitHub, functional not brand noise) | — |
| `kagura-*` (memory/code-reviewer/planner/engineer) | unchanged (already on-brand) | — |

Rename mechanics (measured blast radius): claude-c-suite ≈ **229 refs / 30 files**,
claude-phd-panel ≈ **66 refs / 15 files**. The bulk is mechanical (`/claude-…:…`
cross-references, README, docs, CHANGELOG) → a single scripted `sed` sweep in the migration
commit. The one non-mechanical piece is the **config-file path** (user state): the migrated
plugin reads the new `~/.claude/kagura-*.json` and **falls back to the old `claude-*.json`**
for backward compatibility.

## 5. Canonical-source policy (JFK → kagura-ai)

- **kagura-ai is the single canonical source** for every plugin going forward.
- JFK personal versions (`claude-c-suite`, `gh-issue-driven`, `claude-phd-panel`):
  **frozen — no further development**, carry a "moved to kagura-ai/…" deprecation notice,
  and are **archived** once migration is verified.
- During migration, plugin metadata is rebranded: `author → Kagura AI`,
  **`author email → dev@kagura-ai.com`**, `repository`/`homepage` URLs → kagura-ai.

This removes the double-maintenance risk: there is never more than one actively-developed
copy of a plugin.

## 6. New first-party work — thin skill wrappers (planner & engineer)

Both `kagura-planner` and `kagura-engineer` ship CLIs but no skill. Add a **thin skill-plugin
wrapper inside each tool's own kagura-ai repo** (consistent with reference-don't-vendor).

**Thin means** the skill only:
- discovers config (e.g. `~/.kagura/planner.yaml`; guide to `doctor` if missing),
- runs the `doctor` precondition,
- invokes the existing CLI verb,
- surfaces output readably and hands off to/from neighboring skills (incl. `kagura-memory`
  `recall`/`remember` for grounding).

**Thin explicitly excludes** reimplementing any planning / coding / orchestration logic —
that intelligence stays in the CLI.

- `kagura-planner` → `plan` (+ `doctor`). Tier-1 Tool.
- `kagura-engineer` → `run` / `review` / `goal` (+ `doctor` / `setup`). Tier-2 Harness —
  descriptions must warn about cost / repo-mutation / PR / HITL.

## 7. Repo structure

```
~/works/kagura-plugins/
├── .claude-plugin/
│   └── marketplace.json     # references all in-scope plugins by source; no local plugin bodies
├── README.md                # the ecosystem MAP (see §8)
└── docs/                    # this spec and future design docs
```

`marketplace.json`: each entry references its plugin's own kagura-ai repo as the source and
carries a `category: "tool" | "harness" | "substrate"` (or equivalent keywords) tag so the
tiers are queryable. No plugin source is hosted here.

## 8. README as the ecosystem map

The README is a core deliverable, not boilerplate. It must:
- render the Tier-2 / Tier-1 / Substrate stack diagram from §3,
- group plugins by tier with one-line "what it's for",
- **feature `kagura-memory` (Memory Cloud) prominently as the substrate**, including the
  `session-start` → work → `session-summary` loop that brackets every session,
- make the Harness cost/risk warnings visible,
- give the one-command install / discovery path for the whole ecosystem.

"Comfortable to use" is achieved largely *here*: a reader sees at a glance which pieces are
safe single-shot tools, which are heavy harnesses that drive loops, and how memory grounds
all of it.

## 9. Build sequence (dependency order)

1. **Make reference targets exist first.**
   - `kagura-planner`: add thin skill-plugin in its repo. *(Tracked in a separate
     kagura-planner session — not in this repo's issues.)*
   - `kagura-engineer`: add thin skill-plugin in its repo — **kagura-ai/kagura-engineer#28**.
2. **Migrate JFK plugins to kagura-ai** (create kagura-ai repo, rebrand metadata incl.
   `dev@kagura-ai.com`, deprecation notice on JFK, archive after verify):
   - claude-c-suite — **JFK/claude-c-suite-plugin#3**
   - gh-issue-driven — **JFK/gh-issue-driven#85**
   - claude-phd-panel — **JFK/claude-phd-panel-plugin#5**
3. **Build `kagura-plugins`**: author `marketplace.json` referencing all 7 plugins (with
   tier tags) + the README map (memory substrate featured).
4. **Archive JFK repos** once their kagura-ai counterparts are verified canonical.

## 10. Confirmed decisions

- Name & home: **`~/works/kagura-plugins`** (umbrella marketplace, not "skills").
- **Reference, don't vendor.**
- kagura-ai is canonical; **JFK versions frozen → archived** (no dual maintenance).
- planner & engineer get **thin** skill wrappers **in their own repos**.
- **Tool / Harness 2-tier taxonomy + kagura-memory substrate**, surfaced via README + marketplace metadata.
- **kagura-memory (Memory Cloud) is featured**, not treated as a passive backend.
- Rebrand includes **`author email → dev@kagura-ai.com`**.
- **Prefix unified now (hybrid):** `claude-c-suite → kagura-c-suite`,
  `claude-phd-panel → kagura-phd-panel`; `gh-issue-driven` kept. Invocation namespace is
  `<plugin>:<command>` (marketplace name never appears), so no "kagura" doubling. Config
  paths migrate to `~/.claude/kagura-*.json` with fallback to the old `claude-*.json`.

## 11. Open questions

- Exact `marketplace.json` schema for referencing external repos + the precise field used
  for the `tool`/`harness`/`substrate` category tag (confirm against current Claude Code
  marketplace spec during build).
- Migrated repo names follow the plugin names with no `-plugin` suffix
  (`kagura-ai/kagura-c-suite`, `kagura-ai/kagura-phd-panel`, `kagura-ai/gh-issue-driven`),
  matching the existing `kagura-code-reviewer` convention. (Confirm at create time.)
