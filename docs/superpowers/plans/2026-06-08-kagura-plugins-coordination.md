# kagura-plugins Coordination Plan (B-2) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish the `kagura-plugins` umbrella marketplace, verify it installs the two live plugins, and stand up the coordination machinery that flips each pending plugin to "live" as its kagura-ai repo lands — without re-specifying the per-repo work that the GitHub issues already own.

**Architecture:** `kagura-plugins` is a curated, reference-don't-vendor marketplace. This plan covers only what *this repo* owns: repo hygiene, publish, install verification, a `TRACKING.md` coordination map that references the per-repo issues, and a repeatable "go-live" procedure for syncing the rollout table. The actual skill-authoring and JFK→kagura-ai migrations are out of scope here — they live in their own repos/sessions and are tracked by the linked issues.

**Tech Stack:** Claude Code plugin marketplace (`marketplace.json`), `git`, `gh` CLI, `python3` (JSON validation), Markdown.

**Out of scope (owned elsewhere — referenced, not planned here):**
- kagura-planner thin skill — separate kagura-planner session
- kagura-engineer thin skill — `kagura-ai/kagura-engineer#28`
- claude-c-suite → kagura-c-suite migration+rename — `JFK/claude-c-suite-plugin#3`
- claude-phd-panel → kagura-phd-panel migration+rename — `JFK/claude-phd-panel-plugin#5`
- gh-issue-driven migration — `JFK/gh-issue-driven#85`

---

## File Structure

- Create: `LICENSE` — Apache-2.0 (matches the rest of the kagura-ai ecosystem)
- Create: `.gitignore` — minimal, ignore OS/editor cruft
- Create: `TRACKING.md` — the cross-repo coordination map (issues → marketplace entries → go-live checklist)
- Modify: `README.md:` rollout-status table — flipped 🚧→✅ per plugin as it lands
- Reference (never edited here): `.claude-plugin/marketplace.json` (already final), the per-repo issues

---

## Task 1: Repo hygiene — LICENSE + .gitignore

**Files:**
- Create: `LICENSE`
- Create: `.gitignore`

- [ ] **Step 1: Fetch the canonical Apache-2.0 text from a sibling kagura-ai repo**

The ecosystem already standardizes on Apache-2.0 (e.g. `~/works/kagura-engineer/LICENSE`,
`~/works/kagura-planner/LICENSE`). Copy that exact file to keep the header identical.

Run:
```bash
cp ~/works/kagura-engineer/LICENSE ~/works/kagura-plugins/LICENSE
head -2 ~/works/kagura-plugins/LICENSE
```
Expected: prints `                                 Apache License` / `                           Version 2.0, January 2004`.

- [ ] **Step 2: Create `.gitignore`**

```gitignore
# OS / editor
.DS_Store
*.swp
*~
.idea/
.vscode/
```

- [ ] **Step 3: Verify the tree**

Run: `cd ~/works/kagura-plugins && git status --porcelain`
Expected: shows `?? LICENSE` and `?? .gitignore` (untracked).

- [ ] **Step 4: Commit**

```bash
cd ~/works/kagura-plugins
git add LICENSE .gitignore
git -c user.name="Fumikazu Kiyota" -c user.email="dev@kagura-ai.com" \
  commit -m "chore: add Apache-2.0 LICENSE and .gitignore"
```

---

## Task 2: Re-validate marketplace.json before publishing

**Files:**
- Reference: `.claude-plugin/marketplace.json`

- [ ] **Step 1: Assert valid JSON, 7 plugins, every source is a kagura-ai github repo**

Run:
```bash
cd ~/works/kagura-plugins && python3 -c "
import json
d=json.load(open('.claude-plugin/marketplace.json'))
assert d['name']=='kagura-plugins'
assert len(d['plugins'])==7, len(d['plugins'])
for p in d['plugins']:
    s=p['source']; assert s['source']=='github', s
    assert s['repo'].startswith('kagura-ai/'), s['repo']
    assert any(k in p['keywords'] for k in ('substrate','tool','harness')), p['name']
print('OK: 7 plugins, all kagura-ai github sources, all tier-tagged')
"
```
Expected: `OK: 7 plugins, all kagura-ai github sources, all tier-tagged`

- [ ] **Step 2: No commit** (read-only validation gate; proceed only if Step 1 printed OK).

---

## Task 3: Create the GitHub repo and push

**Files:** none (remote operation)

- [ ] **Step 1: Confirm gh auth and org access**

Run: `gh auth status && gh repo view kagura-ai/kagura-plugins 2>&1 | head -1`
Expected: authenticated; and either the repo already exists, or `Could not resolve to a Repository` (meaning we will create it). If it already exists, skip Step 2.

- [ ] **Step 2: Create the public repo under kagura-ai and push `main`**

Run:
```bash
cd ~/works/kagura-plugins
gh repo create kagura-ai/kagura-plugins \
  --public \
  --description "Umbrella marketplace for the Kagura Memory Cloud ecosystem" \
  --source=. --remote=origin --push
```
Expected: prints the new repo URL and pushes the existing commits. (If `gh` lacks org-create rights, STOP and report — do not work around it.)

- [ ] **Step 3: Verify remote and pushed HEAD match local**

Run:
```bash
cd ~/works/kagura-plugins
git remote get-url origin
git rev-parse HEAD && git ls-remote origin -h refs/heads/main | cut -f1
```
Expected: origin is `https://github.com/kagura-ai/kagura-plugins.git` (or SSH equivalent); the two hashes are identical.

---

## Task 4: Verify the marketplace installs the two LIVE plugins

> These are Claude Code slash commands (run interactively in a Claude Code session), not bash — verify by observation. The two live targets are `kagura-memory` (`kagura-ai/memory-cloud`) and `kagura-code-reviewer` (`kagura-ai/kagura-code-reviewer`).

- [ ] **Step 1: Add the marketplace**

In Claude Code run: `/plugin marketplace add kagura-ai/kagura-plugins`
Expected: marketplace `kagura-plugins` is added; listing shows 7 plugins grouped by their descriptions.

- [ ] **Step 2: Install the substrate**

Run: `/plugin install kagura-memory@kagura-plugins`
Expected: install succeeds; `/kagura-memory:guide` becomes available.

- [ ] **Step 3: Install the live tool**

Run: `/plugin install kagura-code-reviewer@kagura-plugins`
Expected: install succeeds; the `kagura-code-reviewer` skill becomes available.

- [ ] **Step 4: Confirm a pending entry fails cleanly (expected, not a bug)**

Run: `/plugin install kagura-c-suite@kagura-plugins`
Expected: fails because `kagura-ai/kagura-c-suite` does not exist yet — confirming pending entries are inert until their repo lands, while the catalog and live installs are unaffected.

- [ ] **Step 5: Record the result**

No commit. Note in the session which installs succeeded; this is the acceptance gate for "marketplace is live."

---

## Task 5: Author `TRACKING.md` — the cross-repo coordination map

**Files:**
- Create: `TRACKING.md`

- [ ] **Step 1: Write the coordination map** (references the issues; does NOT re-spec their work)

````markdown
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
````

- [ ] **Step 2: Commit**

```bash
cd ~/works/kagura-plugins
git add TRACKING.md
git -c user.name="Fumikazu Kiyota" -c user.email="dev@kagura-ai.com" \
  commit -m "docs: add cross-repo rollout TRACKING map"
git push origin main
```
Expected: push succeeds (origin exists after Task 3).

---

## Task 6: Document the rollout-sync procedure in README

**Files:**
- Modify: `README.md` — append a short "Keeping this in sync" note under the rollout table.

- [ ] **Step 1: Append the operating note**

Add immediately after the rollout-status table in `README.md`:

```markdown
### Keeping this in sync

When a 🚧 plugin's `kagura-ai` repo lands, run the go-live checklist in
[`TRACKING.md`](TRACKING.md): verify `/plugin install <name>@kagura-plugins`, then flip the
row here and in `TRACKING.md` to ✅ and commit. No `marketplace.json` change is needed — its
entries already point at the final `kagura-ai` repos.
```

- [ ] **Step 2: Verify the link resolves and table still renders**

Run: `cd ~/works/kagura-plugins && grep -n "TRACKING.md" README.md`
Expected: at least one match (the new note links to `TRACKING.md`).

- [ ] **Step 3: Commit and push**

```bash
cd ~/works/kagura-plugins
git add README.md
git -c user.name="Fumikazu Kiyota" -c user.email="dev@kagura-ai.com" \
  commit -m "docs: document rollout-sync procedure"
git push origin main
```

---

## Self-Review

**Spec coverage (against `2026-06-08-kagura-plugins-design.md`):**
- §2 reference-don't-vendor → Tasks 2/3 verify all sources are kagura-ai github refs; nothing vendored. ✅
- §7 repo structure (`.claude-plugin/marketplace.json`, `README.md`, `docs/`) → already present; LICENSE/.gitignore/TRACKING added (Tasks 1, 5). ✅
- §8 README as map → already written; Task 6 adds the sync procedure. ✅
- §9 build sequence → reflected as dependency order in `TRACKING.md` (Task 5), with per-repo work referenced by issue, not re-specified (B-2 scope). ✅
- §9 step 1–2 (skills/migrations) → intentionally out of scope here; owned by linked issues. ✅
- §9 step 4 (archive JFK) → captured in the TRACKING go-live checklist. ✅

**Placeholder scan:** No TBD/TODO; every step has exact commands or exact file content. ✅

**Type/identifier consistency:** Plugin names, repo slugs, issue numbers (#28, #3, #5, #85), and namespaces (`/kagura-c-suite:…`) match `marketplace.json`, the README, and the spec. ✅

**Note:** This is a coordination plan, so several verifications are interactive `/plugin`
slash commands rather than unit tests — that is intentional and the expected observation is
stated for each.
