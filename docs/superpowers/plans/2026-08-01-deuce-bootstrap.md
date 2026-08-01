# deuce Bootstrap Implementation Plan (ace freeze + Chapter 0)

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Freeze ace, and take deuce from an empty repo to a ratified, built Chapter 0 (Identity & governance) with the founding issue queue at #2–#8.

**Architecture:** Two repos. ace gets its freeze mechanics on the already-committed charter branch (`docs/deuce-reboot-charter`). deuce gets: labels + founding epics first (gh operations consume no PR numbers, epics land at #2–#8), then the Chapter 0 SDS text + Glossary on a PR, an HC ratification gate, then the governance artifacts (ADRs, CLAUDE.md, AGENTS.md, hooks, templates, README) on a second PR, then tag `v0.0`.

**Tech Stack:** Markdown, `gh` CLI, bash (git hooks only — TypeScript tooling arrives with Chapter 3 per the charter; a `labels.yml` declaration file ships now, its sync *script* waits for Ch 3).

**Spec:** `docs/superpowers/specs/2026-08-01-deuce-reboot-design.md` (approved 2026-08-01)

## Global Constraints

- Every commit ends with both trailers:
  `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>` and
  `Claude-Session: https://claude.ai/code/session_018R8pEVrRaYu31jeDmdXtUN`
- Commit with `git -c commit.gpgsign=false` (1Password ssh-signing hangs non-interactively on this machine); note "unsigned" in the final report.
- **Reference grammar everywhere:** bare `#N` = issue; a pull request is always written `PR #N`.
- **No closing keyword adjacent to an epic reference, even negated.** Children/PRs use `Part of #N`.
- **Issue bodies are dual-register:** `## Summary (HC)` (required, non-technical, bulleted) then `## Technical detail (HC+AC)` (optional, labeled).
- **One label per axis per issue:** exactly one `type:`, one `status:`, one `area:`.
- **HC GATE steps:** stop and wait for the human. Never simulate, skip, or self-approve them.
- deuce work before Chapter 0 ratification is covered by the charter's bootstrap exception; say so in PR bodies rather than hiding it.
- ace changes must keep `ruby scripts/parity_check.rb` green.

## File Structure

```
ace (branch docs/deuce-reboot-charter — spec + this plan already on it):
  README.md                          # + freeze banner (top of file)

deuce:
  sds/00-identity-and-governance.md  # Chapter 0 text (the thing ratified)
  GLOSSARY.md                        # founding vocabulary
  adr/0001..0006-*.md                # founding ADRs
  adr/archive/                       # empty dir with .gitkeep (consolidating-supersession target)
  CLAUDE.md                          # the AC's canonical config (no import indirection)
  AGENTS.md                          # ~20-line contractor role-boundary file
  README.md                          # v0 — stranger's front door
  labels.yml                         # label declaration (source of truth; synced by hand until Ch 3)
  .github/ISSUE_TEMPLATE/{epic,task,bug,spike,chore}.yml + config.yml
  .githooks/guard-protected-branch   # bash; ported/trimmed from ace
  bin/setup                          # installs hooks (core.hooksPath)
```

---

### Task 1: ace — freeze banner on the charter branch, open the charter PR

**Files:**
- Modify: `README.md` (ace, branch `docs/deuce-reboot-charter`) — insert banner after the `# ace` heading
- Already on branch: `docs/superpowers/specs/2026-08-01-deuce-reboot-design.md`, `docs/superpowers/plans/2026-08-01-deuce-bootstrap.md`

**Interfaces:**
- Produces: the charter PR URL (Task 2's announcement issue links it)

- [ ] **Step 1: Add the freeze banner** — insert directly under the `# ace` H1 in ace's `README.md`:

```markdown
> [!IMPORTANT]
> **ace is frozen (2026-08-01).** Development continues in
> [deuce](https://github.com/wrburgess/deuce), its successor — see the
> [reboot charter](docs/superpowers/specs/2026-08-01-deuce-reboot-design.md).
> Only critical host-breaking fixes land here, labeled `must-port`.
> Hosts remain on ace until the deuce cutover (charter §11).
```

- [ ] **Step 2: Verify the quality gate**

Run: `ruby scripts/parity_check.rb`
Expected: `parity_check: OK - Canonical Source, 3 Adapters, Project Config, 13 Skills, and links all resolve.`

- [ ] **Step 3: Commit**

```bash
git add README.md
git -c commit.gpgsign=false commit -m "docs: freeze ace — development continues in deuce

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_018R8pEVrRaYu31jeDmdXtUN"
```

- [ ] **Step 4: Push and open the PR**

```bash
git push -u origin docs/deuce-reboot-charter
gh pr create --repo wrburgess/ace \
  --title "docs: deuce reboot charter + ace freeze" \
  --body "Adds the approved deuce reboot charter (design spec), its bootstrap implementation plan, and the README freeze banner.

ace is frozen on this merge: no new feature work; critical host-breaking fixes only, labeled \`must-port\`. Development continues in [deuce](https://github.com/wrburgess/deuce).

🤖 Generated with [Claude Code](https://claude.com/claude-code)

https://claude.ai/code/session_018R8pEVrRaYu31jeDmdXtUN"
```

Record the PR URL for Task 2.

### Task 2: ace — announcement issue, pin, dissolve #127

**Files:** none (gh operations only)

**Interfaces:**
- Consumes: charter PR URL from Task 1

- [ ] **Step 1: Create and pin the announcement issue**

```bash
gh issue create --repo wrburgess/ace \
  --title "ace is frozen: the reboot continues as deuce" \
  --body "## Summary (HC)

- ace stops taking new feature work as of 2026-08-01.
- Its successor is **deuce** — a rebuild of this system around the Software Development System standard: https://github.com/wrburgess/deuce
- The full rationale and design is the reboot charter: <CHARTER-PR-URL>
- Existing host repos (bryce, nadal, mpi-ace) stay on ace, untouched, until deuce's distribution chapter cuts them over one at a time.
- The only work that may still land here: a critical fix a host cannot live without, labeled \`must-port\` so deuce inherits it.

## Technical detail (HC+AC)

- Freeze mechanics, migration sequencing, and the issue-queue rebuild policy: charter §11.
- deuce re-authors issues rather than transferring them; each deuce issue cites its ace original by URL. This issue is the umbrella reference for that migration.
- Dissolved on freeze: #127 (Ruby removal — deuce is TypeScript from birth)."
```

Replace `<CHARTER-PR-URL>` with Task 1's PR URL. Then pin:

```bash
gh issue pin <NEW-ISSUE-NUMBER> --repo wrburgess/ace
```

- [ ] **Step 2: Close #127 with a pointer**

```bash
gh issue close 127 --repo wrburgess/ace --comment "Dissolved by the deuce reboot: deuce is TypeScript from birth, so the bash-vs-Ruby analysis is moot there and no port will happen here. The runtime decision and its rationale (including why this issue's bash conclusion no longer binds): reboot charter §2, <CHARTER-PR-URL>. Recorded as deuce founding ADR 0003 in Task 7 of the bootstrap plan."
```

- [ ] **Step 3: HC GATE — merge**

Ask the HC to review and merge the charter PR (ace's merge gate is human). The freeze takes legal effect on that merge. Do not proceed to deuce tasks that *depend on the freeze being announced*; Tasks 3–4 may run in parallel with this gate since they touch only deuce.

### Task 3: deuce — label system (gh operations; no issue numbers consumed)

**Files:** none yet (`labels.yml` ships in Task 7's PR; this task creates the live labels so Task 4's epics can carry them)

**Interfaces:**
- Produces: the exact label names Tasks 4–8 attach to issues/PRs

- [ ] **Step 1: Delete GitHub's default labels**

```bash
for l in bug documentation duplicate enhancement "good first issue" "help wanted" invalid question wontfix; do
  gh label delete "$l" --repo wrburgess/deuce --yes
done
```

Expected: each deletes (or "not found" — fine).

- [ ] **Step 2: Create the three axes**

```bash
# type axis
gh label create "type:epic"  --repo wrburgess/deuce --color 5319E7 --description "Umbrella with children; eight-field brief; closes when last child merges"
gh label create "type:task"  --repo wrburgess/deuce --color 0E8A16 --description "One branch, one PR of feature/improvement work"
gh label create "type:bug"   --repo wrburgess/deuce --color D93F0B --description "Defect with a reproduction; fix ships with the test that failed first"
gh label create "type:spike" --repo wrburgess/deuce --color 1D76DB --description "Research feeding a decision; terminal artifact is a readout"
gh label create "type:chore" --repo wrburgess/deuce --color FBCA04 --description "Mechanical maintenance; minimal ceremony"
# status axis
gh label create "status:ready"               --repo wrburgess/deuce --color C2E0C6 --description "Unblocked; may be picked up"
gh label create "status:in-progress"         --repo wrburgess/deuce --color FEF2C0 --description "Actively being worked"
gh label create "status:blocked"             --repo wrburgess/deuce --color E99695 --description "Waiting on something named in the issue"
gh label create "status:review"              --repo wrburgess/deuce --color BFDADC --description "PR open; in review"
gh label create "status:done-pending-merge"  --repo wrburgess/deuce --color BFD4F2 --description "Work complete; awaiting merge/close"
# area axis (one per chapter initially)
gh label create "area:identity"     --repo wrburgess/deuce --color 006B75 --description "Ch 0 — identity & governance"
gh label create "area:lifecycle"    --repo wrburgess/deuce --color 0052CC --description "Ch 1 — lifecycle & skills"
gh label create "area:review"       --repo wrburgess/deuce --color 8250DF --description "Ch 2 — Review System + Findings System"
gh label create "area:tooling"      --repo wrburgess/deuce --color 76428A --description "Ch 3 — quality gate & TypeScript tooling"
gh label create "area:learning"     --repo wrburgess/deuce --color 2E7D32 --description "Ch 4 — Learning System"
gh label create "area:distribution" --repo wrburgess/deuce --color B60205 --description "Ch 5 — distribution & fleet cutover"
gh label create "area:factory"      --repo wrburgess/deuce --color 555555 --description "Ch 6 — factory automation"
```

- [ ] **Step 3: Verify**

Run: `gh label list --repo wrburgess/deuce --limit 30`
Expected: exactly the 17 labels above, none of GitHub's defaults.

### Task 4: deuce — founding epics at #2–#8

**Files:** none (gh operations)

**Interfaces:**
- Consumes: labels from Task 3
- Produces: issues #2–#8; later PRs reference `Part of #2` (Chapter 0's epic)

**Numbering rule:** PR #1 (the `.gitignore` PR) consumed number 1, so the next seven numbers are 2–8 — but only if nothing else is created first. Create these seven issues in exactly this order, immediately, and verify each number as it returns. If any number is off (someone filed an issue), continue creating, then fix cross-references to match reality — the numbers are a nicety, not a contract.

- [ ] **Step 1: Create the seven chapter epics in order** (each command: fill the body with the per-epic content below)

```bash
gh issue create --repo wrburgess/deuce --title "<TITLE>" \
  --label "type:epic" --label "<STATUS>" --label "<AREA>" --body "<BODY>"
```

**#2 — Title:** `EPIC: Establish deuce's identity and governance (Chapter 0)` — labels `status:in-progress`, `area:identity`

```markdown
## Summary (HC)
- Write and ratify the first chapter of the Software Development System: who acts, who reviews, what the rules of the repo are, and how decisions get recorded.
- Ratification is a working session between the human and the AC; nothing else in deuce may be built until this chapter sanctions it.
- Done when: the chapter text is merged after ratification, the founding vocabulary (GLOSSARY.md), decision records (ADRs), agent config, git hooks, issue templates, and README exist, and release v0.0 is tagged.

## Technical detail (HC+AC)
- Chapter text: `sds/00-identity-and-governance.md`, drafted from the reboot charter §§1–9.
- Unlocks on ratification: CLAUDE.md, AGENTS.md (contractor role file), .githooks, labels.yml, issue templates, founding ADRs 0001–0006, README v0.
- Children reference this issue as `Part of #2`. No closing keywords near this issue's number.
- Source: ace reboot charter (ace repo, docs/superpowers/specs/2026-08-01-deuce-reboot-design.md), §10 row Ch 0.
```

**#3 — Title:** `EPIC: Define the development lifecycle and rebuild its skills (Chapter 1)` — labels `status:blocked`, `area:lifecycle`

```markdown
## Summary (HC)
- Write and ratify the chapter that defines how work moves from idea to merged code (the five lifecycle stages and their gates), then rebuild the working skills that drive it.
- Existing ace skills are audited: proven ones port trimmed to the standard; redundant ones are dropped; ideas from external skill families may be absorbed (re-authored, attributed) but never copied in.
- Done when: the lifecycle chapter is ratified, the surviving skills exist in deuce, and deuce's own remaining build-out runs through its own lifecycle (self-hosting).

## Technical detail (HC+AC)
- Blocked by: Chapter 0 (#2 — reference only, not a closing relation).
- Includes per-stage model/effort defaults and the capacity discipline as adaptive config, and the zero-based rules port (a rule enters only with findings receipts).
- Absorbs from ace: #163 (Readout), #110 residue, #164 workstream 1 (handoff delivery), #158 (skill audit), #77 (declaration only).
- Children reference this issue as `Part of #3`.
```

**#4 — Title:** `EPIC: Build the Review System and Findings System (Chapter 2)` — labels `status:blocked`, `area:review`

```markdown
## Summary (HC)
- Write and ratify the chapters governing how work gets independently reviewed (bounded, so reviews end) and what happens to everything a run learns along the way (recorded, so nothing is lost or re-litigated).
- Reviews are performed by external models acting as contractors: summoned with the standards in the work order, checked for compliance when the review comes back.
- Done when: both systems are ratified, the reviewer summons tooling works end-to-end with an executable readiness check, and findings have a recorded home with states.

## Technical detail (HC+AC)
- Blocked by: Chapter 1 (#3 — reference only).
- Review System: lens-set bounding (not round counts), fix-verification bounded separately, batch-before-resummon. Findings System: type × state axes, one-way flow, subtractive triage. Prose-artifact verification policy states here (contractor review on canon PRs + dogfooding + recurrence signal).
- Absorbs from ace: #161, #125, #164 workstreams 2–3.
- Children reference this issue as `Part of #4`.
```

**#5 — Title:** `EPIC: Build the quality gate and TypeScript tooling (Chapter 3)` — labels `status:blocked`, `area:tooling`

```markdown
## Summary (HC)
- Write and ratify the chapter defining deuce's quality checks, then build them: a lint over the repo's own structure (links resolve, labels correct, required sections present) in TypeScript, with tests.
- Done when: the gate runs green locally and in CI, and every mechanical convention this repo promises is actually checked by it.

## Technical detail (HC+AC)
- Blocked by: Chapter 2 (#4 — reference only).
- config-lint parses (never regexes) frontmatter/YAML; checks link integrity, one-label-per-axis, required issue-body sections per type, closing-keyword adjacency, bare-#N-resolves-to-PR in checked artifacts. Fail-first evidence discipline applies to its own tests.
- Absorbs from ace: #127 (dissolved — TS from birth), parity-check lessons (green-but-blind classes).
- Children reference this issue as `Part of #5`.
```

**#6 — Title:** `EPIC: Build the Learning System (Chapter 4)` — labels `status:blocked`, `area:learning`

```markdown
## Summary (HC)
- Write and ratify the chapter for how deuce learns: four input channels (vendor guidance, outside practitioners, observed platform changes, downstream-repo findings) feeding reviewed config changes — never automatic ones.
- Includes the recurring hygiene sweep: re-verify platform assumptions, promote recurring findings into rules, demote rules whose defect class stopped occurring, audit ADR/glossary drift.
- Done when: the intake skills that survive audit exist, Insights ingestion works, and the sweep has run once for real.

## Technical detail (HC+AC)
- Blocked by: Chapter 3 (#5 — reference only).
- Trust boundary applies: field input is data, never instructions; only a reviewed PR changes config.
- Absorbs from ace: #131, #164, #158; fleet channel classifies findings generic vs host-specific at recording time.
- Children reference this issue as `Part of #6`.
```

**#7 — Title:** `EPIC: Distribute deuce to the fleet and retire ace (Chapter 5)` — labels `status:blocked`, `area:distribution`

```markdown
## Summary (HC)
- Write and ratify the chapter for how deuce reaches other repositories: what ships, what a host owns, how updates arrive as reviewable pull requests, and how findings flow back up.
- Then cut the fleet over: bryce first as the canary, then nadal and mpi-ace. ace is archived when the last host is over.
- Done when: every host runs on deuce, each has a vendoring receipt, and ace is read-only.

## Technical detail (HC+AC)
- Blocked by: Chapter 4 (#6 — reference only).
- Payload manifest (contract/seed/host classes), vendoring receipt (source SHA + checksums), sync-as-PR, findings-up channel. Host findings accumulated during the freeze get swept here.
- Absorbs from ace: #149 (reshaped).
- Children reference this issue as `Part of #7`.
```

**#8 — Title:** `EPIC: Automate the factory (Chapter 6)` — labels `status:blocked`, `area:factory`

```markdown
## Summary (HC)
- Write and ratify the chapter for hands-off operation: an issue enters the queue and the system runs it to a merge-ready (or attested-merged) pull request without a human driving each step.
- This is deliberately last: it automates a pipeline only after every gate in that pipeline has proven itself under human observation.
- Done when: a real issue travels queue → merged with no human keystroke between the two standing gates.

## Technical detail (HC+AC)
- Blocked by: Chapter 5 (#7 — reference only).
- Consumes Ch 1's model/effort declarations; contractor-reviewer model removes ace's harness-exclusion complexity. Board/trigger/idempotency design re-cut from ace #144; graduated merge per charter §4.
- Absorbs from ace: #144, #77 (consumption).
- Children reference this issue as `Part of #8`.
```

- [ ] **Step 2: Verify numbering and labels**

Run: `gh issue list --repo wrburgess/deuce --json number,title,labels --limit 10`
Expected: issues #2–#8 with the titles above; each has exactly one `type:`, one `status:`, one `area:` label. If numbers drifted, update the `Part of #N` / "Blocked by" references in affected bodies via `gh issue edit`.

### Task 5: deuce — draft Chapter 0 + Glossary, open the ratification PR

**Files:**
- Create: `sds/00-identity-and-governance.md`
- Create: `GLOSSARY.md`

**Interfaces:**
- Consumes: charter §§1–9 as source material
- Produces: the PR the HC ratifies in Task 6

- [ ] **Step 1: Clone fresh and branch** (do not reuse a stale clone)

```bash
git clone https://github.com/wrburgess/deuce.git && cd deuce
git checkout -b sds/00-identity-and-governance
```

- [ ] **Step 2: Write `sds/00-identity-and-governance.md`** — stranger-ready, Readout-shaped (bullets/tables over paragraphs; plain register with glossed terms; every rule carries its why). Sections, each drafted from the named charter source:

| Section | Content (source) |
|---|---|
| `# Chapter 0 — Identity & Governance` | One-paragraph plain statement of what deuce is (charter §1) |
| `## What this system is for` | The five purpose bullets, rewritten for a stranger (charter §1) |
| `## Who acts, who reviews` | Claude-only AC; contractor-reviewer model with its three mechanisms; residual risk named (charter §§2–3) |
| `## Governing principles` | Perfectionism boundary ("excellent enough", with the boundary test); foundation-first prioritization incl. reversibility rule; invariant/adaptive split; bootstrap exception (charter §§4–6) |
| `## Trust boundary` | The three standing rules, verbatim in substance (charter §7) |
| `## Health measures` | The four measures, Quality + Autonomy primary, recording home (charter §8) |
| `## Decision records` | ADR bar, consolidating supersession, archive dir, founding set listed (charter §9) |
| `## Work Tracking System` | Full schema: title grammar, five types + gap rule, three axes one-each, dual-register body contracts, relationship + reference grammar (charter §5) |
| `## Vocabulary` | The Glossary's role: terms enter canon only via entries at ratification; gloss rule (charter §5) |
| `## Governance` | Public/MIT/HC-governed; merge authority note: until the Review System (Ch 2) exists, every merge is HC — graduated `attested` merges activate only when their machinery does |
| `## Ratification` | What ratifying a chapter means: bounded distill session → amendments → merge → glossary/ADR updates → tag `v0.N` |

- [ ] **Step 3: Write `GLOSSARY.md`** — alphabetized, one entry per term, each entry: **Term** — plain one-to-two-line definition. Founding set (define each from the charter's usage): AC · Adaptive layer · Bootstrap exception · Chapter · Consolidating supersession · Contractor reviewer · Dual register · Epic / Task / Bug / Spike / Chore (one entry: Issue types) · Findings System · Fleet · Foundation-first · Freeze · Glossary · HC · Health measures · Host · Hygiene sweep · Invariant layer · Learning System · must-port · Ratification · Readout · Reference grammar · Review System · SDS · Trust boundary · Work Tracking System · Zero-based port.

- [ ] **Step 4: Verify structure**

Run: `grep -c '^## ' sds/00-identity-and-governance.md` (expected: 10) and `grep -c '^\*\*' GLOSSARY.md` (expected: ≥ 28). Read both files top to bottom once for register (no ungloseed jargon in Summary-level text).

- [ ] **Step 5: Commit, push, open PR**

```bash
git add sds/00-identity-and-governance.md GLOSSARY.md
git -c commit.gpgsign=false commit -m "docs(sds): draft Chapter 0 — identity & governance, plus founding Glossary

Part of #2

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_018R8pEVrRaYu31jeDmdXtUN"
git push -u origin sds/00-identity-and-governance
gh pr create --repo wrburgess/deuce \
  --title "docs(sds): Chapter 0 — identity & governance (ratification draft)" \
  --body "Part of #2. The Chapter 0 text and founding GLOSSARY.md, drafted from the approved reboot charter for HC ratification. Built under the bootstrap exception (the machinery this chapter establishes does not yet exist to govern it).

Ratification session happens on this PR; amendments land as commits here.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

https://claude.ai/code/session_018R8pEVrRaYu31jeDmdXtUN"
```

### Task 6: HC GATE — Chapter 0 ratification

**Files:** amendments to Task 5's two files, on the same branch

- [ ] **Step 1: Ratification session.** A bounded distill session with the HC over the PR: walk the chapter section by section; the HC accepts, edits, or rejects each; contested terms resolve into the Glossary. Convergence rule (per ace's own learnings): register plainly-stated HC decisions and move on; grill genuine ambiguity only.
- [ ] **Step 2: Fold amendments** as commits on the branch (same trailer/signing constraints).
- [ ] **Step 3: HC merges the PR.** (Every deuce merge is HC until Chapter 2 exists.) Chapter 0 is now ratified canon.

### Task 7: deuce — governance artifacts (built under the ratified chapter)

**Files:**
- Create: `adr/0001-deuce-succeeds-ace-chapter-gated-rebuild.md`
- Create: `adr/0002-claude-sole-ac-contractor-reviewers.md`
- Create: `adr/0003-typescript-runtime.md`
- Create: `adr/0004-invariant-adaptive-split.md`
- Create: `adr/0005-trust-boundary.md`
- Create: `adr/0006-adr-policy.md`
- Create: `adr/archive/.gitkeep`
- Create: `CLAUDE.md`, `AGENTS.md`, `labels.yml`, `README.md` (overwrite the 7-byte placeholder)
- Create: `.github/ISSUE_TEMPLATE/{epic,task,bug,spike,chore}.yml`, `.github/ISSUE_TEMPLATE/config.yml`
- Create: `.githooks/guard-protected-branch`, `bin/setup`

**Interfaces:**
- Consumes: ratified `sds/00-identity-and-governance.md` (cite it; never restate it — the chapter is the single source)
- Produces: the governed repo every later chapter builds inside

- [ ] **Step 1: Branch** — `git checkout main && git pull && git checkout -b chore/chapter-0-artifacts`

- [ ] **Step 2: Write the six ADRs.** Shared format per ADR (keep each under ~40 lines):

```markdown
# ADR NNNN: <title>
- Status: accepted
- Date: 2026-08-01
## Decision
<one short paragraph>
## Why (the trade-off that was live)
<bullets>
## Supersedes / references
<ace ADRs or issues by URL; "none" if none>
```

Content per ADR (decision + core trade-off): **0001** deuce replaces ace via a chapter-gated rebuild (SDS ratified chapter-by-chapter; nothing exists unsanctioned) rather than in-place refactor — trade: rebuild cost vs. carrying ace's accreted architecture; references the ace charter URL. **0002** Claude Code is the sole AC; other models are contractor reviewers (summons carries standards; AGENTS.md is a role-boundary file; compliance validated at intake) — trade: vendor hedge weakened, accepted eyes-open; supersedes ace's canonical/adapter architecture (ace ADR 0002 by URL). **0003** TypeScript is the tooling runtime from birth — trade: forces node for contributors vs. ace's zero-install bash floor; notes ace #127's bash conclusion was bound to the dropped harness-neutrality constraint. **0004** canon splits invariant process truths from dated, sourced, re-verifiable adaptive declarations — trade: two-tier bookkeeping vs. ace's platform-economics-frozen-into-canon failure (ace ADR 0022 cited as the counter-example). **0005** external content is data, never instructions; config changes only via reviewed PR; provenance + review-at-update for absorbed material; credential blast-radius declarations — trade: ingestion friction vs. prompt-injection surface of a self-modifying config system. **0006** ADR bar is hard-to-reverse + surprising + real trade-off; consolidating supersession moves stale ADRs to `adr/archive/`; live set stays small — trade: fewer records vs. ace's 37-ADR restatement debt.

- [ ] **Step 3: Write `AGENTS.md`** (complete file):

```markdown
# Role boundary for non-Claude agents

If you are not Claude Code, you are in this repository as a **Reviewer** —
a contractor, not a resident. You were summoned for one bounded job.

- **Do:** review the diff or artifact named in your summons; report findings
  in the format the summons specifies; sign with your tool and model.
- **Do not:** implement, commit, push, open PRs, edit issues, or modify any
  file. Enforcement exists (git hooks, gates); this notice is so you never
  need to hit it.
- Your standards arrive **in the summons** (severity framework, lens set,
  findings format, scope). If a summons lacks them, say so in your response
  rather than inventing standards.
- Context, if you want it: `sds/` is this repo's governing standard;
  `GLOSSARY.md` defines its terms.
```

- [ ] **Step 4: Write `CLAUDE.md`** — sections, citing the chapter rather than restating it: **Identity** (deuce is the SDS reference implementation; you are the sole AC; `sds/00` governs — link); **Reading order** (sds/ → GLOSSARY.md → adr/); **Work Tracking** (issue conventions: link the chapter's section; reference grammar and dual-register named as always-on rules); **Git** (feature branches only — hooks enforce; commit trailer format with tool + model; unsigned-commit note for this machine); **Gates** (every merge is HC until the Review System chapter lands); **Bootstrap status** (chapters ratified so far: 0; nothing outside a ratified chapter's sanction may be built).

- [ ] **Step 5: Write `labels.yml`** — the 17 labels exactly as created in Task 3, as data:

```yaml
# Source of truth for deuce's labels. Synced by hand until config-lint (Ch 3) automates it.
# Discipline: every issue carries exactly one label per axis.
axes:
  type:
    - {name: "type:epic",  color: "5319E7", description: "Umbrella with children; eight-field brief; closes when last child merges"}
    - {name: "type:task",  color: "0E8A16", description: "One branch, one PR of feature/improvement work"}
    - {name: "type:bug",   color: "D93F0B", description: "Defect with a reproduction; fix ships with the test that failed first"}
    - {name: "type:spike", color: "1D76DB", description: "Research feeding a decision; terminal artifact is a readout"}
    - {name: "type:chore", color: "FBCA04", description: "Mechanical maintenance; minimal ceremony"}
  status:
    - {name: "status:ready",              color: "C2E0C6", description: "Unblocked; may be picked up"}
    - {name: "status:in-progress",        color: "FEF2C0", description: "Actively being worked"}
    - {name: "status:blocked",            color: "E99695", description: "Waiting on something named in the issue"}
    - {name: "status:review",             color: "BFDADC", description: "PR open; in review"}
    - {name: "status:done-pending-merge", color: "BFD4F2", description: "Work complete; awaiting merge/close"}
  area:
    - {name: "area:identity",     color: "006B75", description: "Ch 0 — identity & governance"}
    - {name: "area:lifecycle",    color: "0052CC", description: "Ch 1 — lifecycle & skills"}
    - {name: "area:review",       color: "8250DF", description: "Ch 2 — Review System + Findings System"}
    - {name: "area:tooling",      color: "76428A", description: "Ch 3 — quality gate & TypeScript tooling"}
    - {name: "area:learning",     color: "2E7D32", description: "Ch 4 — Learning System"}
    - {name: "area:distribution", color: "B60205", description: "Ch 5 — distribution & fleet cutover"}
    - {name: "area:factory",      color: "555555", description: "Ch 6 — factory automation"}
```

- [ ] **Step 6: Write the five issue templates + config.** `config.yml`:

```yaml
blank_issues_enabled: false
```

`task.yml` (the others repeat this shape with the fields varied as listed below):

```yaml
name: "TASK — one branch, one PR"
description: "Feature or improvement work sized to a single PR"
title: "TASK: "
labels: ["type:task", "status:ready"]
body:
  - type: markdown
    attributes:
      value: "Title rule: plain-language imperative a human understands with zero context. Add exactly one area: label after creating."
  - type: textarea
    id: summary
    attributes:
      label: "Summary (HC)"
      description: "Non-technical, bulleted, no jargon: what's wanted, why it matters, what done looks like"
    validations: {required: true}
  - type: textarea
    id: technical
    attributes:
      label: "Technical detail (HC+AC)"
      description: "Optional. As deep as the work needs; still bulleted."
    validations: {required: false}
  - type: textarea
    id: done_when
    attributes:
      label: "Done when"
      description: "The observable state that closes this issue"
    validations: {required: true}
  - type: input
    id: part_of
    attributes:
      label: "Part of"
      description: "Parent epic, as 'Part of #N' — never a closing keyword"
    validations: {required: false}
```

Field variations — `epic.yml`: labels `["type:epic","status:ready"]`, title `"EPIC: "`, replace `done_when`/`part_of` with the eight-field brief textareas (Problem, Target solution, Goals, Constraints, Expectations, Risks, Edge cases, Punted paths — Problem/Target/Goals required, rest optional) plus a required checkbox "Exit test: a zero-history AC could start Plan from this brief". `bug.yml`: labels `["type:bug","status:ready"]`, title `"BUG: "`, replace `done_when` with required textareas Reproduction / Expected / Actual. `spike.yml`: labels `["type:spike","status:ready"]`, title `"SPIKE: "`, replace `done_when` with required textareas "Question" and "Decision this feeds"; add optional "Readout destination". `chore.yml`: labels `["type:chore","status:ready"]`, title `"CHORE: "`, only `summary` required and `part_of` optional.

- [ ] **Step 7: Write `.githooks/guard-protected-branch` + `bin/setup`** (port of ace's proven guard, trimmed):

```bash
#!/usr/bin/env bash
# .githooks/guard-protected-branch — block direct commits/pushes on main.
# Installed as pre-commit and pre-push via bin/setup (core.hooksPath).
set -euo pipefail
branch="$(git symbolic-ref --quiet --short HEAD 2>/dev/null || echo detached)"
if [ "$branch" = "main" ]; then
  echo "deuce: '$branch' is protected — create a feature branch (sds/00 § Governance)." >&2
  exit 1
fi
```

```bash
#!/usr/bin/env bash
# bin/setup — activate repo git hooks after clone.
set -euo pipefail
cd "$(git rev-parse --show-toplevel)"
mkdir -p .githooks
for h in pre-commit pre-push; do ln -sf guard-protected-branch ".githooks/$h"; done
git config core.hooksPath .githooks
chmod +x .githooks/guard-protected-branch bin/setup
echo "deuce hooks installed (core.hooksPath=.githooks)"
```

Then run `bash bin/setup` in the clone and verify: a `git commit` attempt on `main` fails with the message; on a feature branch it succeeds.

- [ ] **Step 8: Write `README.md` v0** (replaces placeholder) — sections: **deuce** (two plain sentences: a software development system for running AI-agent-built projects with human judgment at exactly two points; deuce is both the standard and its reference implementation); **Status** (table: chapters 0–6, one row each, Ch 0 `ratified`, rest `not started`; link #2–#8); **How this repo grows** (three bullets: chapter-gated; ratification = HC session; nothing unsanctioned); **Adopting it** (one line: not yet — arrives with Chapter 5; watch #7); **Provenance** (successor to ace, link + charter link); **Vocabulary** (link GLOSSARY.md).

- [ ] **Step 9: Verify, commit, PR**

Run: `bash bin/setup && git checkout main && git commit --allow-empty -m test` → expected: hook blocks. `git checkout chore/chapter-0-artifacts` → commit proceeds:

```bash
git add -A
git -c commit.gpgsign=false commit -m "chore: Chapter 0 governance artifacts — ADRs, agent config, hooks, templates, labels, README

Part of #2

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_018R8pEVrRaYu31jeDmdXtUN"
git push -u origin chore/chapter-0-artifacts
gh pr create --repo wrburgess/deuce --title "chore: Chapter 0 governance artifacts" \
  --body "Part of #2. The artifacts Chapter 0 sanctions: founding ADRs 0001–0006, CLAUDE.md, AGENTS.md (contractor role file), labels.yml, issue templates, branch-protection hooks, README v0.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

https://claude.ai/code/session_018R8pEVrRaYu31jeDmdXtUN"
```

- [ ] **Step 10: HC GATE — HC reviews and merges.**

### Task 8: deuce — tag v0.0 and settle the queue

**Files:** none (gh/git operations)

- [ ] **Step 1: Tag the ratification**

```bash
git checkout main && git pull
git tag -a v0.0 -m "Chapter 0 ratified: identity & governance"
git push origin v0.0
gh release create v0.0 --repo wrburgess/deuce --title "v0.0 — Chapter 0: Identity & governance" \
  --notes "First ratified chapter. deuce exists, is governed, and may now build its lifecycle (#3). Charter: see README § Provenance."
```

- [ ] **Step 2: Settle epic states** — comment on #2 that both child PRs merged and v0.0 is tagged, then close #2; flip #3 from `status:blocked` to `status:ready` (`gh issue edit 3 --repo wrburgess/deuce --remove-label status:blocked --add-label status:ready`).

- [ ] **Step 3: Report** — summary to the HC: freeze state in ace, deuce queue state, tag URL, and the single next action: plan Chapter 1 (new plan, after this one closes).

---

## Plan self-review (performed at authoring)

- **Spec coverage (this phase):** charter §11 freeze → Tasks 1–2; §5 Work Tracking schema live → Tasks 3–4, 7; §§1–9 as chapter text → Task 5; ratification protocol → Task 6; §9 ADR policy + §3 AGENTS.md + governance artifacts → Task 7; §10 tag-per-ratification → Task 8. Chapters 1–6 are explicitly future plans (chapter-gating forbids planning them now).
- **Placeholders:** none — every file has content or an exact per-section content map; the two HC gates are deliberate stops, not gaps.
- **Consistency:** label names in Task 3 = Task 4's usage = Task 7's `labels.yml`; issue numbers #2–#8 carry a drift-recovery rule; `Part of #2` used by both deuce PRs; reference grammar respected throughout.
