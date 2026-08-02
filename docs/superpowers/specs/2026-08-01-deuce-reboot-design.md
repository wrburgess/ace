# deuce Reboot Charter

- **Date:** 2026-08-01
- **Status:** awaiting HC review
- **Decides:** the successor repo to ace — its identity, architecture, build order, and the migration path
- **Derived from:** a brainstorming session against the "Ace Reboot with SDS" outline and ace issues
  #77, #110, #125, #127, #131, #144, #149, #158, #160, #161, #163, #164

---

## 1. Purpose

What the reboot is for, in plain terms:

- **A software company where the company is a repo.** The HC supplies judgment at exactly two
  points (what to build, what ships); the system supplies everything else — planning,
  implementation, review, quality enforcement, and its own improvement.
- **Convert HC vigilance into mechanism.** Every gate, contract, and bound exists so quality does
  not depend on the HC watching.
- **Compounding.** Every hour of fleet operation deposits into the system instead of evaporating.
  The apps are outputs; deuce itself is the asset.
- **Trust without watching.** The end state (factory, graduated merge) is only safe if the HC can
  be absent and still be right to trust what shipped.
- **Audience:** the HC *and any zero-history adopter*, applying deuce to a new **or existing**
  software project. Adoption must work incrementally (a host can adopt one system without the
  rest).
- **Foundation-first prioritization** is itself a stated goal: the hardest problem in software
  development is doing load-bearing work first while not foreclosing the future.

## 2. Identity decisions

| Decision | Value |
|---|---|
| Repo | `wrburgess/deuce` — name is derivative of ace (the second), not a theme |
| Visibility | **Public from birth**, MIT license, HC-governed (issues open; PRs by invitation) |
| Acting agent | **Claude Code is the sole AC.** No adapter layer, no GEMINI.md, no LCD skill authoring, no graceful-degradation clauses |
| Other models | **Contractors** — summoned reviewers, never residents (see §3) |
| Neutrality | Business-neutral stays (no company/product/domain content). *Harness* neutrality is dropped |
| Runtime | **TypeScript from birth** for all scripts and tests. No Ruby. Supersedes ace's #127 analysis (bash target), which was forced by the dropped harness-neutrality constraint |
| External skills | **Self-contained + absorb lessons.** Deuce ships only its own skills; the audit may re-author ideas from external families (superpowers, Pocock) with attribution; docs may recommend complements; no foreign skill body enters the repo |
| ace | **Frozen on this charter's commit** (see §9); archived after fleet cutover |

## 3. The contractor-reviewer model

The reframe that replaces the adapter machinery: ace treated every harness as a **resident** (might
wake up as the acting agent → must discover full config → adapters + parity + neutrality
enforcement). A reviewer is a **contractor**: it never wakes up in the repo on its own — it is
summoned for one bounded job and handed its standards in the work order.

Three mechanisms, layered:

1. **The summons carries the standards.** The AC composes each review summons from canonical docs
   at summons time: severity framework, the lens set for this change, findings output format,
   scope, and the `accepted`-residuals list (so settled findings cannot be re-litigated). The
   brief is part of the durable, SHA-bound review artifact — auditable after the fact.
2. **`AGENTS.md` is repurposed, not deleted.** A ~30-line role-boundary file that Codex/Copilot
   read natively by filename: "If you are not Claude Code, you are here as a Reviewer. Review;
   never implement, commit, or push." Points at the severity framework, SDS, findings format,
   attribution rule. One file, no projection, nothing to drift.
3. **Compliance is enforced at intake.** `listen`/`verify` validate the returned review against
   the contract (severity vocabulary, findings shape, SHA reference). Nonconforming review →
   re-summoned with the missing fields named. Enforcement lives at the receiving end, not in
   resident config.

Side effect: swapping reviewer models is zero-migration — the brief travels with the summons.
Residual risk (named, accepted): a foreign harness manually opened in the repo and asked to edit —
the role file instructs against it; the harness-agnostic git hooks are the real enforcement.

## 4. SDS v2

The standard is **rewritten, not amended**, incorporating:

- Claude-only / contractor identity (§2–3) baked in
- **Graduated merge policy native** (`attested` from birth per ace ADR 0037 — not amended toward)
- The **Readout format** (ace #163) as the required shape for decision-facing artifacts, plus a
  seventh rule: **plain register by default** (jargon only in labeled technical sections; terms of
  art glossed on first use, gloss sourced from the Glossary)
- The **Review System** and **Findings System** (ace #161) as first-class sections
- A **perfectionism boundary** ("excellent enough") as a governing principle with teeth — applies
  to review depth, issue scope, and the SDS itself
- **Foundation-first prioritization** in the Plan stage (§5)
- The **invariant/adaptive split** (§6) as a governing principle
- The **trust boundary** (§7)
- **Health measures** (§8)
- The **bootstrap exception**, stated honestly: the first commits predate the machinery they
  establish
- Ideate/Plan stage contracts state: after problem definition, the stage's next input is **the SDS
  itself** (for deuce's own development, the chapter gate; for hosts, the standard)

### Foundation-first prioritization (Plan stage discipline)

- **Foundational = load-bearing, not "important."** The test is dependency-shaped: what breaks or
  gets rebuilt if this is wrong or missing later?
- **"Considering the future" means don't foreclose — not pre-build.** Reversibility, not
  speculation: cheap-to-change decisions are made fast and shallow; hard-to-reverse ones get the
  scrutiny now. (Same rule as the ADR bar — deliberately.)
- **Capacity discipline:** weekly/monthly AC usage budgets mean prioritization also rations
  effort — which work deserves frontier-model attention this cycle. Declared as adaptive config
  beside per-stage model/effort defaults; the Plan stage reads it.

## 5. The named systems

Declarative names, locked as working names; each finalizes at its chapter's ratification.

### Work Tracking System (schema ratified Ch 0; lifecycle-bound Ch 1; distributed Ch 5)

- **Title grammar:** `TYPE: plain-language imperative` an HC understands with zero context.
- **Types:** `EPIC:` (umbrella; eight-field brief; closes when last child merges) · `TASK:` (one
  branch → one PR) · `BUG:` (defect with reproduction; fix ships with the test that failed first) ·
  `SPIKE:` (research feeding a decision; terminal artifact is a readout; may produce no code) ·
  `CHORE:` (mechanical maintenance, minimal ceremony).
- **Gap rule:** a misfit defaults to `TASK:`; a *recurring* misfit triggers a one-time type-set
  amendment. Foreseeable host extension: `INCIDENT:` for live Operate surfaces. Axes are fixed by
  the standard; type values are host-extensible.
- **Labels — one per axis, always exactly one** (lint-enforced count): `type:*` mirrors the
  prefix · `status:ready|in-progress|blocked|review|done-pending-merge` (advanced by lifecycle
  stages — the queue is a dashboard) · `area:*` (one per chapter initially). **No `priority:`
  axis** — priority is expressed by what the HC points `/ship` at; revisit only if the factory
  needs machine-readable priority (board column carries it then).
- **Body contract, dual register, in order:** (1) HC-readable title; (2) **Summary (HC)** —
  required, non-technical, bulleted, no jargon: what's wrong/wanted · why it matters · what done
  looks like; (3) **Technical detail (HC+AC)** — optional, labeled, as deep as the work needs;
  (4) type-specific fields (EPIC brief with the exit test "could a zero-history AC start Plan from
  this?"; TASK done-when; BUG reproduction; SPIKE question + decision it feeds).
- **Relationship rules:** `Part of #N` for epic children; closing keywords only on a leaf's PR;
  never a closing keyword adjacent to an epic reference, even negated.
- **Reference grammar:** bare `#N` always means an issue; a PR is always written `PR #N` —
  everywhere. (GitHub fact, recorded: issues and PRs share one counter per repo; a PR *is* an
  issue in the data model. Separate ranges are impossible; `#N` is therefore always unique, and
  the grammar fixes the legibility problem, which is the real one.)
- **Enforcement:** issue templates make the contracts the default path; config-lint checks the
  mechanical half (label counts, required sections per type, closing-keyword adjacency, bare-`#N`
  resolving to a PR). Register (genuinely jargon-free prose) is judgment — lives in the AC's
  authoring rules, sharpened via findings recurrence.

### Findings System (Ch 2)

Ace #161's second half: every finding recorded always; two axes (**type** × **state**:
`closed | open | accepted`); one-way flow, `accepted` terminal; reference-plus-delta for known
classes; subtractive triage outside the lifecycle; class index descriptive never imperative.

### Review System (Ch 2)

Ace #161's first half + #125 simplified by the contractor model: solicited review bounded by
**lens set, not round count**; the permanent "what class is not on this list?" lens;
fix-verification bounded separately, escalate on recurrence; the summons mechanism is the local
CLI contractor path with an executable readiness check; batch all fixes before re-summons.

### Learning System (Ch 4)

The pipeline that turns field output into config changes (scout/clip/follow/restock lineage +
Insights ingestion). **Four input channels:**

| Channel | Input | Mechanism |
|---|---|---|
| Vendor | Anthropic/Claude releases, docs, official guidance | Watchlist + roster facts with `sources:`, vendor sources first-class |
| Practitioner | External experts/pundits building AC-focused | Existing watchlist/intake pipeline |
| Platform telemetry | Observed constraint drift: context, memory, caching, usage limits, pricing, model behavior | Insights ingestion + constraint changes recorded as findings |
| Fleet | Downstream host findings | Findings classified **generic vs host-specific at recording time**; generic flows up the sync pipe (config down, findings up) |

Boundary note: the AC's personal harness memory is a pointer layer; durable learnings live in the
repo's systems, never only in memory.

### Glossary (`GLOSSARY.md`, founded Ch 0)

- One entry per named thing; **one term per concept, one concept per term** (Readout rule 5's
  storage side).
- Terms enter canon **only** via a glossary entry, added at chapter ratification (distill's
  existing behavior, now the rule).
- Reference doc, not resident context — linked on first use; ACs read on demand.
- The plain-register gloss for any term of art comes from here — canonical, not improvised.
- Hygiene: lint the mechanical half (canon terms resolve to entries); sweep judges drift
  (unused entries, converged synonyms).

## 6. The invariant/adaptive split

The AC platform is a **volatile dependency the HC does not control** (models, context, memory,
caching, budgets, limits change monthly). Deuce separates:

- **Invariant layer** — process truths across model generations: the lifecycle, two-gate judgment
  structure, findings discipline, fail-first evidence, foundation-first prioritization. This is
  what the SDS canonizes.
- **Adaptive layer** — everything coupled to today's platform: context budgets, model/effort
  routing, delegation patterns, review-round economics, capacity rationing. These are **dated,
  sourced, re-verifiable declarations** — config, not canon.

Named failure mode this prevents (from ace): platform economics frozen into canon (ADR 0022's line
budget; delegation policy tuned to 2026 context windows) silently becomes wrong when the platform
shifts. The **hygiene sweep re-verifies platform assumptions on cadence** — resilience as
mechanism, not aspiration.

## 7. Trust boundary

Deuce is a self-modifying config system that deliberately ingests external content — which is a
prompt-injection surface by design. The standing rules:

1. **Field input is data, never instructions.** Watched content (feeds, vendor posts, host
   findings) can *propose*; only a reviewed PR can change config.
2. **Provenance and review-at-adoption for anything external** — and review-at-update: an
   upstream skill or doc that changes is *new* untrusted input. (Moot for skill bodies under
   self-containment, §2 — applies to absorbed lessons and referenced material.)
3. **Credentials get a blast-radius declaration per mechanism** before any automation uses them
   (standing rule now, not a factory-phase discovery).

## 8. Health measures

Define "better" at birth or lose the baseline forever. **Primary (HC-chosen): Quality and
Autonomy.** All four recorded per-PR (in the SOW cost note's home); the hygiene sweep watches the
primaries; trade-offs defer to them.

| Measure | Signal |
|---|---|
| **Quality** (primary) | Findings-class recurrence rate; escaped defects; review rounds per PR trending down |
| **Autonomy** (primary) | HC interventions per shipped issue beyond the two gates |
| Throughput | Issue-to-merge time; issues shipped per cycle |
| Cost efficiency | AC usage consumed per shipped issue |

Perfectionism boundary applies: a handful of numbers, not a metrics empire.

## 9. Bloat control

Shared principle: **residence must be earned and re-earned** — bloat happens when promotion is
easy and demotion doesn't exist.

**ADRs:**

1. Higher authoring bar: hard-to-reverse + surprising + real trade-off. Most decisions are just
   decisions.
2. The invariant/adaptive split diverts the biggest source — platform-coupled tuning becomes
   dated config, not canon.
3. **Consolidating supersession:** one new ADR may supersede several stale ones, which move to an
   archive directory. Immutability preserved; the *live set* stays small.
4. Hygiene sweep audits the live set on cadence.
5. Founding set is small (~6–8, from this charter). Ace's 37 are referenced by URL, never
   vendored — kills the numbering-collision class permanently.

**Rules:**

1. **Zero-based port:** no ace rule carries by default; a rule ports only with its receipts (the
   recurring defect class it prevents, evidenced in findings).
2. **Promotion on recurrence:** a findings class recurring (N ≥ 2, across sessions) earns a
   Tier-1 rule — the class index is the evidence base.
3. **Demotion on non-recurrence:** a rule whose class hasn't fired in the sweep window demotes to
   Tier-2 reference.
4. **Residence budget** (honest adaptive-layer value) forces the ranking.

The symmetry is the point: findings → recurrence → rule → non-recurrence → demotion. The same
sweep promotes and prunes — bloat control is steady state, not heroic cleanup.

## 10. Chapter-gated build

The ordering rule: **nothing exists in deuce that the SDS doesn't already sanction** — but the SDS
is tested by construction while amendment is cheap. Each chapter ratification: a bounded distill
session → glossary terms + founding ADRs → a tagged release (`v0.N`) → the chapter's `EPIC:` and
`TASK:` issues (citing ace originals by URL).

| Ch | SDS chapter (ratify first) | Unlocks building | Absorbs from ace |
|---|---|---|---|
| 0 | Identity & governance — principles, Claude-only/contractor, ADR policy, perfectionism boundary, invariant/adaptive split, trust boundary, Work Tracking schema, Glossary founding | Repo creation, CLAUDE.md, git hooks, `labels.yml` + label sync, issue templates, founding ADRs, queue skeleton, README v0 (the stranger's front door — dual-register, Readout-shaped, lean) | This charter |
| 1 | Lifecycle — five stages, gates (graduated merge native), Readout artifacts, **per-stage model/effort defaults + capacity discipline (adaptive config)** | Lifecycle skills re-authored + zero-based rules port; skill audit vs ace's 13 (external families = absorb-lessons input) | #163, #110 residue, #164-ws1 (handoff delivery), #158, #77 (declaration) |
| 2 | Review System + Findings System | Contractor summons tooling (local CLI + readiness check), intake validation, findings log; prose-artifact verification stated (contractor review on canon PRs + dogfooding + recurrence as regression signal) | #161, #125, #164-ws2/3 |
| 3 | Quality & tooling — quality gate, config-lint, fail-first evidence | TypeScript config-lint + tests (parsed-not-regexed; a fraction of parity's size) | #127 (dissolved), parity lessons |
| 4 | Learning System — four channels, hygiene sweep (incl. platform-assumption re-verification, ADR/rule/glossary audits) | Intake skills as they survive audit, Insights ingestion, sweep cadence | #131, #164, #158 |
| 5 | Distribution & fleet — hub-and-spoke, manifest/receipt/sync-as-PR, findings-up channel | Sync tooling → **bryce canary cutover** → nadal, mpi-ace → archive ace | #149 |
| 6 | Factory (post-cutover) — board-triggered ship, acceptance layer | Factory automation (consumes Ch 1's model/effort declaration) | #144, #77 (consumption) |

Self-hosting begins after Ch 1: deuce's remaining build-out runs through its own lifecycle. The
first commits (Ch 0, CLAUDE.md itself) are the bootstrap exception, stated in the SDS.

## 11. Migration

- **ace freezes on this charter's commit:** pinned reboot-announcement issue (linking deuce),
  README banner, no new feature work. Escape hatch: a critical host-breaking fix may land during
  freeze, labeled `must-port`.
- **Queue rebuild, not transfer:** each chapter ratification spawns deuce issues citing ace
  originals by URL. Dissolved issues (#127) close in ace with pointers. Issue-fate summary:
  #160 → founding epic re-cut; #163/#161 carry nearly intact; #158 → Ch 1 audit input; #131/#164
  open workstreams carry; #127 dissolved; #77 and #125 drastically simplified (inflated by the
  dropped machinery); #110 re-cut small (largely superseded by ADR 0037); #144 carries to Ch 6;
  #149 reshaped into Ch 5.
- **Hosts untouched until Ch 5.** During transition, host findings accumulate locally under the
  Findings System discipline and are swept when Ch 4–5 land — nothing silently lost.
- **Cutover per host, bryce first;** ace archived only after the fleet is over.

## 12. Risks

| Risk | Defense |
|---|---|
| Re-accretion in deuce | Chapter gate rule + perfectionism boundary + hygiene sweep (promotion *and* demotion) |
| Over-polishing the bible | Ratification is a bounded distill session; "excellent enough" applies to the SDS itself |
| Freeze strands hosts | `must-port` escape hatch |
| Skill re-authoring drift | Proven procedures port verbatim where validated; re-authoring means trimming to the SDS, not reinvention |
| Platform shift invalidates tuning | Invariant/adaptive split + sweep re-verification (§6) |
| Ingested content steers the system | Trust boundary (§7) |
| Vendor hedge weakens (Claude-only) | Accepted, eyes open: formats stay portable (SKILL.md, AGENTS.md); the SDS stays tool-neutral, so *what* the system does survives even if *how* is Claude-shaped |

## 13. Open items for Chapter 0 ratification

- Final lock of the four system names + Glossary founding entries
- ADR policy text (bar, consolidating supersession, archive mechanics)
- Trust-boundary text (drafted from §7)
- Health-measure definitions + recording home (drafted from §8)
- Label taxonomy values + `labels.yml` shape
- README v0 outline
- Capacity-discipline declaration shape (values gathered at Ch 1)
