# Design: the `constitution-interview` preset

The buildable specification for a bundled preset that turns `/speckit.constitution` into a bounded, inference-first interview producing an enforceable constitution.

## How to read this

This is the **flattened view** of [Map: guided constitution authoring](https://github.com/DigitalWink/spec-kit-for-human-team/issues/1) and the fifteen resolutions on its child tickets. Those tickets stay the primary sources: they hold the evidence, the alternatives that lost, and the reasoning. This document holds the **current state, with every later correction already applied**, so a builder never has to read fifteen resolutions in order and layer amendments while reading.

Four rules govern it:

- **Every section names the ticket that settled it**, by name and link. Where a later ticket amended an earlier one, the section carries the amended result and names both.
- **Shipped strings are reproduced in full.** Anything the interview prints — a step frame, a prompt line, an acknowledgement, a closing artifact — appears here in its final form. Derivations, alternatives and the reasoning behind a string live in [Per-area question wording: what each step actually says](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12), whose resolution spans **two comments** because the wording exceeded one comment's size limit. A builder never has to reconstruct a shipped string from a diff.
- **The vocabulary is [CONTEXT.md](../../CONTEXT.md)**, and this document uses its terms exactly rather than restating them. When a term here is doing precise work — *decision*, *area*, *rung*, *drop delta*, *measured set*, *uncharged turn*, *prompt-text invariant* — that file is its definition.
- **Dashes in shipped strings are normalised to `—`.** The settled comments render the same strings with `—` and with `-` in different places; the difference is presentation, not content.

**Scope.** This document specifies what to build. It creates none of it: no preset directory, no `data/strictness-ladders.yml`, no command override, no test file.

Settled by [Design document: flatten the fifteen resolutions into one buildable specification](https://github.com/DigitalWink/spec-kit-for-human-team/issues/16).

---

## Contents

1. [What is being built, and why](#1-what-is-being-built-and-why)
2. [Packaging](#2-packaging)
3. [The flow, end to end](#3-the-flow-end-to-end)
4. [The wording](#4-the-wording)
5. [Amendment mode](#5-amendment-mode)
6. [`$ARGUMENTS`](#6-arguments)
7. [The output floor](#7-the-output-floor)
8. [The test plan](#8-the-test-plan)
9. [Out of scope](#9-out-of-scope)
10. [Stated residues](#10-stated-residues)
11. [Appendix: what the tracker superseded](#appendix-what-the-tracker-superseded)

---

## 1. What is being built, and why

**One bundled preset, `constitution-interview`, declaring exactly one `wrap` override of `speckit.constitution`.** It replaces core's one-shot constitution authoring with a **per-area walk**: a bounded pre-walk scan, an exact announced decision count, six always-ask areas plus at most three fired conditionals, a Governance confirmation, a self-review, a single write, and one correction round.

**The content model is locked and unchanged from upstream.** The constitution stays durable, project-wide technical and process policy — not a values charter. Every principle must be mechanically checkable by a downstream consumer. The preset's job is to find *the few rules that actually bind* and to refuse everything else, naming where the refused content belongs instead.

### 1.1 The verdict that justifies it

[Prototype: a full sample interview transcript](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8) executed the whole design end to end, four arms, every inference measured against HEAD. The comparison the map exists to make came back positive:

| | Today's `.specify/memory/constitution.md` | The brownfield run's output |
|---|---|---|
| Principles | 5 | 8 |
| **Obligations** | **46** | **22** |
| Body characters | **11,699** | **3,646** |
| Whole file | 13,213 | 4,003 |
| Sync Impact Report | 1,512 | 355 |
| Plan-visible principles | fewer than half of what is admitted | **6 of 8** |

Both obligation counts use one rule — the self-review's accounting unit, case-insensitive and file-wide: `grep -oiE '\bMUST NOT\b|\bMUST\b|\bSHOULD NOT\b|\bSHOULD\b' <file> | wc -l`. Using one unit on both sides is the point.

52% fewer obligations, 69% fewer body characters. On the brownfield arm **6 of 9** decisions close in one word; on greenfield, 0 of 7.

### 1.2 The two caveats, stated up front

Both come from the same prototype, and a builder who does not know them will build to the wrong pitch.

**The cut is made by the obligation quota and the shipped exemplars, not by inference.** The route-out table and the admission test refuse roughly 18 of today's 46 obligations; the remainder comes off through the **per-area obligation quota**, which trims at draft time before the user ever sees the rule and deliberately logs nothing. Roughly **17 of the 22** obligations in the produced document are shipped exemplar text from [Taxonomy of areas, and the already-enforced-elsewhere router](https://github.com/DigitalWink/spec-kit-for-human-team/issues/5), identical on any repo of that shape.

What is inferred is **which areas to ask about**, **what goes in the blanks**, and **what evidence to show**. What is written is mostly shipped. The honest description of the product: *a curated, admission-tested, quota-capped starter constitution, personalised by four measurements and one cold question, that refuses to write anything a gate cannot read.* The preset's README should say that, not "infers aggressively from your repo".

Named casualties of the quota, each admissible and each silently absent from the produced document: the catalog-stack surface model, the base-class and registry obligations, and a lazy-loading `SHOULD` that no area in the taxonomy has a home for.

**An existing constitution cannot be reached by amending. Migration is delete-and-re-author.** Two independent reasons compound. The amendment interview touches at most **three areas** per run. And **thirteen of today's 46 obligations live in `##` sections that are not areas** — the amendment walks areas, so no step opens them at any ceiling. One amendment run buys about **11%** of the obligations, in one area, for three decisions.

The only route from a legacy document to the interview's document is to remove `.specify/memory/constitution.md` and re-run. The record frames that as a convenience escape hatch; it is in fact **the migration path**, and it is the first thing a real user hits on any repo that already has a constitution. It belongs in the preset's README.

*Settled in [Prototype: a full sample interview transcript](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).*

### 1.3 The locked constraints this design is built inside

From the map. They are not reopened here.

1. The constitution keeps its upstream content model — durable technical and process policy, mechanically checkable.
2. One **adaptive** flow detecting greenfield and brownfield. **Brownfield is primary**: infer, then confirm or correct.
3. **One person answers.** No multi-person consensus.
4. Ships as a **bundled preset wrapping `speckit.constitution`**. It must not modify core command files.
5. The flow **detects an existing constitution** and switches to a short amendment interview.
6. A **one-pass self-review** rewrites weak principles and reports what it changed. No score, no loop.
7. The flow **refuses content already enforced** by a linter, CI, or branch protection, and routes it elsewhere.
8. It **must terminate**.

---

## 2. Packaging

*Settled in [Preset shape: wrap `/speckit.constitution` or add a command](https://github.com/DigitalWink/spec-kit-for-human-team/issues/4), as corrected by [Behavior when the preset stack has no Constitution Check gate](https://github.com/DigitalWink/spec-kit-for-human-team/issues/9) on `{CORE_TEMPLATE}` placement, on supersession scope and on the single-write invariant, and amended by [Per-area question wording](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12) on the ladder schema.*

### 2.1 Identity

- **Preset id:** `constitution-interview` (`^[a-z0-9-]+$`, valid).
- **Display name:** Constitution Interview.
- **Bundled**, which is three mechanical facts and not one: a directory at `presets/constitution-interview/`, an entry in `presets/catalog.json` with `"bundled": true`, and a force-include line in `pyproject.toml`.

The name is the ubiquitous-language name — *interview* is [CONTEXT.md](../../CONTEXT.md)'s settled term for the flow — and it matches the `<object>-<mechanism>` shape of its sibling `constitution-sync`, which is what a user sees beside it in `specify preset search`.

### 2.2 The manifest

Every field below is one the schema validator accepts. Nothing here is invented; in particular there is **no `priority`** (an install-time argument recorded in the registry, not a manifest field), **no `replaces:`** (not a schema field — nothing in `src/specify_cli/` reads it), and **no incompatibility declaration** (no such field exists).

```yaml
schema_version: "1.0"

preset:
  id: "constitution-interview"
  name: "Constitution Interview"
  version: "1.0.0"
  description: "Turns /speckit.constitution into a bounded, inference-first interview that produces an enforceable constitution."
  author: "github"
  repository: "https://github.com/github/spec-kit"
  license: "MIT"

requires:
  # Runtime-resolution baseline: the core /constitution command no longer
  # propagates into dependent templates. The interview's write contract
  # assumes exactly one writer of .specify/memory/constitution.md.
  speckit_version: ">=0.14.4"

provides:
  templates:
    - type: "command"
      name: "speckit.constitution"
      file: "commands/speckit.constitution.md"
      description: "Wrap /constitution with a bounded, inference-first interview"
      strategy: "wrap"

tags:
  - "constitution"
  - "governance"
  - "interview"
  - "onboarding"
```

**One `provides.templates` entry, and only one.** Explicitly not a `constitution-template` (§2.4), not a `speckit.tasks` override (§2.7), and not the ladder file (§2.5).

The catalog entry mirrors `constitution-sync`'s shape and is **appended** — `presets/catalog.json` is not alphabetical today:

```json
"constitution-interview": {
  "name": "Constitution Interview",
  "id": "constitution-interview",
  "version": "1.0.0",
  "description": "Turns /speckit.constitution into a bounded, inference-first interview that produces an enforceable constitution.",
  "author": "github",
  "repository": "https://github.com/github/spec-kit",
  "license": "MIT",
  "bundled": true,
  "requires": { "speckit_version": ">=0.14.4" },
  "provides": { "commands": 1, "templates": 0 },
  "tags": ["constitution", "governance", "interview", "onboarding"]
}
```

`docs/community/presets.md` gets **no row** — that table is community-only.

### 2.3 The wrapper command file

`presets/constitution-interview/commands/speckit.constitution.md`, `strategy: wrap`.

**Frontmatter contract.** Composition reattaches the *highest-priority* layer's frontmatter and inherits only three keys from the base — `scripts`, `agent_scripts`, `argument-hint`. Therefore the wrapper:

- **MUST NOT** declare `scripts:`. Declaring it would shadow core's `resolve-template.sh constitution-template --json` and break `{SCRIPT}`, which is a **single** substitution — a command cannot invoke two scripts.
- **MUST** declare `description:`. It is not inherited, and the composed description **wins** over core's, which is the whole discoverability mitigation for wrapping a core command: `/speckit.constitution` announces itself as the interview wherever the agent lists commands.
- **MUST** re-declare `handoffs:`. Also not inherited; core's handoff to `speckit.specify` is otherwise silently lost.

**`{CORE_TEMPLATE}` goes first.** Composition substitutes the base **into** the wrapper, so only `{CORE_TEMPLATE}` first puts the supersession block last — which is what makes it the last instruction the agent reads. This corrects the "goes last" directive in [Preset shape](https://github.com/DigitalWink/spec-kit-for-human-team/issues/4), whose own justification and whose cited precedent (`constitution-sync` places it first) both say *first*.

**Block supersession, rescoped to producing and writing.** The wrapper names the region it displaces rather than overriding core line by line — line-level supersession depends on core's line numbers and rots on the next core edit. All nine core lines that would otherwise leak placeholders, TODO markers or judgment calls lie inside core's `## Outline` region (`[75, 146)`), so the region subsumes the list. The shipped clause:

> Everything above is superseded **as the procedure for producing and writing the constitution**, except `## User Input`, `## Scope Guard`, `## Pre-Execution Checks` and `## Post-Execution Checks`. If any of those sections is absent, proceed without it. A section above that is explicitly scoped to run **after** the constitution has been written — another preset's propagation or sync pass — is **not** superseded: it runs after this command's single write, in the order those sections appear above.

The rescoping is load-bearing: the unscoped form ("everything above is superseded") silently deleted `constitution-sync`'s entire propagation pass, because that pass composes *above* the wrapper's block. The rescoped form is **order-robust** — it works in both priority orders — and it preserves the three properties the wording was chosen for: no dependence on core's line numbers, base-agnostic, and supersession read last.

**Which core lines the region covers**, for a reader checking coverage: `constitution.md:99` (ratification-date ask), `:107` (retained-slot carve-out), `:108` (comment-removal judgment), `:109` ("explicit rationale if not obvious"), `:110` (Governance contents), `:117` (TODO markers in the Sync Impact Report), `:120` (bracket-token validation — compatible, and strengthened rather than replaced), `:123` (SHOULD substitution), `:142` (the general `TODO(<FIELD_NAME>)` emitter). Core's step 4 at `:112`, which prepends the Sync Impact Report, is **not** superseded (§3.13). Core's step 6 write at `:125` is inside the region and never executes.

**The template precondition check**, immediately after `{SCRIPT}` returns `TEMPLATE_CONTENT` and **before** the announced count:

> Check the resolved template for `## Core Principles`, `## Governance`, and the `**Version** | **Ratified** | **Last Amended**` footer. If any is missing, stop and report which one, without announcing a count and without writing anything. **If the resolver line is still literal, or `TEMPLATE_CONTENT` is empty, stop and report that the stack supplied no resolvable `constitution-template`** — same treatment as a failed resolution.

The unsubstituted-resolver branch is what makes a `lean`-based stack a clean refusal instead of a broken run (§2.6). The check costs **zero decisions** because it precedes the announcement, and it can never leave a half-written document. One honest limitation: `resolve-template.sh --json` emits only `TEMPLATE_NAME` and `TEMPLATE_CONTENT`, with no source attribution, so the message can name the missing anchor but not the layer that supplied the template. The user finds that with `specify preset resolve constitution-template`.

### 2.4 No shipped `constitution-template`

**The preset pins by precondition, not by override.** Under the write contract the template's *text* is never a source: the write assembles a document **to** the template's structure, never **from** its bytes. Nothing is copied, so nothing needs replacing. Shipping a stripped template would buy a benefit the write contract already delivers, at the price of a second override surface, a changed `specify init --preset` seed, and a fight with `constitution-sync`'s materialization — and it would not guarantee the pin anyway, because a project-local `.specify/templates/overrides/` file is priority 1 and beats every preset.

### 2.5 Where the shipped ladder content lives

`presets/constitution-interview/data/strictness-ladders.yml`, installed to `.specify/presets/constitution-interview/data/strictness-ladders.yml` — a preset's entire directory is copied by `shutil.copytree`, so shipping arbitrary files is mechanically free.

**A data file, read lazily** — only when a menu actually opens, which a mature brownfield repo never reaches. Inline in the wrapped command was refused twice over: dead prompt weight on the common path, and asserting the shipped-rung invariants over prose embedded in a several-hundred-line composed markdown file means parsing prose, which is what makes the invariant untestable. A resolvable template was refused on a mechanical constraint: `{SCRIPT}` is a single substitution already spent on `constitution-template`.

The schema, with the `drops:` field [Per-area question wording](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12) added:

```yaml
schema_version: "1.0"
ladders:
  - area: "testing"
    rungs:
      - text: |
          A plan MUST state, in Technical Context, the automated evidence for each
          behavior change it introduces. A functional requirement whose only stated
          evidence is manual verification MUST NOT be planned.
        gate_question: "Does Technical Context name automated evidence for every behavior change in this plan?"
      - text: |
          A plan MUST state, in Technical Context, the automated evidence for each
          behavior change it introduces.
        drops: "a plan whose only stated evidence is manual verification"
```

Field rules, each a shipped-content invariant a unit test asserts (§8):

- **Rung order in the list is the strictness order**, strictest first. No `strictness:` index — an index that must equal the position is a second source of truth.
- **`text` is emitted verbatim** and is the only thing that reaches the document. Block scalars keep it byte-exact.
- **`gate_question` is on rung 1 only.**
- **`drops` is required on every rung after the first and forbidden on rung 1.**
- **`none` is not a rung.** It is a rendered co-equal option, not shipped content.
- **Run-time elements are not in the file** — the *nothing to infer* line and the repo fact that opened the menu are produced per run.

**Ten ladders are reachable, not eleven.** Negative scope is cold on every run, and a cold area is not a menu, so its ladder would be unreachable content. A uniform schema costs nothing either way; whoever authors the file should not author an eleventh ladder without a reason.

Nothing else ships as data. The walk order, the route-out table and the conditional triggers are **procedure**, and they live in the wrapped command's prose where the agent reads them every run.

### 2.6 Composition with the other presets that touch this command

**`constitution-sync` — compatible, and it composes.** Both declare `wrap`, and composition chains recursively. At default priorities both install at priority 10, the tie breaks alphabetically, and `constitution-interview` < `constitution-sync`, so **the interview is the outer wrapper**. Install order cannot decide the winner: every install and remove re-runs `_reconcile_composed_commands`, which recomputes the composition. The rescoped supersession clause (§2.3) is what lets sync's propagation pass survive, and it is order-robust in both arrangements. **Do not flip the default priorities**: flipping hands the composed command's `description:` to `constitution-sync` and costs the discoverability lever.

Install-time materialization cannot reach an interviewed constitution: `_reconcile_constitution` returns immediately unless the registry holds an enabled entry under the literal id `constitution-sync`, and when it does, it replaces the live file only while the file still matches its recorded generated-content hash. An interviewed constitution matches neither, from the moment the wrapper writes it.

**`lean` — do not stack it.** `lean` declares no `strategy:`, so it defaults to `replace`. With `lean` at higher priority it shadows the interview outright. With the interview at higher priority the interview wraps **lean's 33-line stub**, not core — and lean's stub declares only `description:`, so the composed command has **no `scripts:`**, `{SCRIPT}` is left literal, `TEMPLATE_CONTENT` never arrives, and the precondition check stops the run. That is a clean refusal, and it falls out of settled machinery rather than needing a decision. The README must carry the rule with the `{SCRIPT}` reason attached: *lean's `speckit.constitution` is a deliberate minimum and the interview is a deliberate maximum of the same command.*

**`self-test` — a fixture, not a stack member.** It ships the `constitution-template` override that motivates the precondition check, and a test asserts it is deliberately absent from the catalog.

### 2.7 No `speckit.tasks` override

The plan-side anchor for testing is accepted, and its weakness is recorded rather than hidden. Core's default is real and stays: `templates/tasks-template.md:12` and `templates/commands/tasks.md:147` make tests optional and do not list the constitution as a trigger. A testing rule anchored on the plan's Technical Context collides with none of it, which is the point. The honest cost: enforcement binds at `/plan` and at `/analyze`'s requirement-to-task mapping, not at task generation.

### 2.8 Files this creates and edits

| | Path | Note |
|---|---|---|
| new | `presets/constitution-interview/preset.yml` | §2.2, verbatim |
| new | `presets/constitution-interview/commands/speckit.constitution.md` | §2.3; carries the whole flow |
| new | `presets/constitution-interview/data/strictness-ladders.yml` | §2.5; rung texts and `drops` strings are build work |
| new | `presets/constitution-interview/README.md` | `specify preset add constitution-interview`; what it does and does not do; the **do not stack with `lean`** rule with its `{SCRIPT}` reason; that `constitution-sync` composes at default priorities with the interview outermost; the **migration path** of §1.2; the pitch of §1.2 |
| edit | `presets/catalog.json` | §2.2, appended |
| edit | `pyproject.toml` | `"presets/constitution-interview" = "specify_cli/core_pack/presets/constitution-interview"` under `[tool.hatch.build.targets.wheel.force-include]` |
| — | `presets/README.md` | optional; it enumerates no bundled list, so nothing is stale by omission |
| — | `docs/community/presets.md` | **no change**; community-only table |

Write the catalog entry **first**: `tests/contract/test_wheel_bundled_presets.py` goes red the moment the entry lands without the force-include line, which is the right order to discover it in.

---

## 3. The flow, end to end

This section is the mechanical specification — order, predicates, numbers. The strings the flow prints live in [§4](#4-the-wording), and each stage below points at the one that carries its text.

### 3.1 Preconditions and routing

In order, before anything is announced and before anything is written:

1. **Hook blocks.** Core's `## Pre-Execution Checks` runs strictly before core's `## Outline`, therefore strictly before the interview announces anything. A `before_constitution` hook cannot interleave a question into a counted walk, because at the moment it runs there is no count yet.
2. **`{SCRIPT}`**, then the **template precondition check** (§2.3). A failure stops the run.
3. **The amendment predicate** over the live `.specify/memory/constitution.md`. All five conditions must hold to route to the amendment interview ([§5](#5-amendment-mode)); any failure routes to authoring.

| | Condition |
|---|---|
| **A1** | `.specify/memory/constitution.md` exists. |
| **A2** | It carries all four mandatory anchors: an `# ` H1, `## Core Principles`, `## Governance`, and a `**Version**` / `**Ratified**` / `**Last Amended**` footer line. |
| **A3** | The footer **parses**: `**Version**` is a `MAJOR.MINOR.PATCH` triple of integers, and both dates are ISO `YYYY-MM-DD`. |
| **A4** | Zero matches for `\[[A-Z][A-Z0-9_]{3,}\]` anywhere in the file. |
| **A5** | Zero occurrences of the literal `TODO(`. |

**It reads the document, never the provenance sidecar and never a bare file-exists check.** `specify init` seeds `.specify/memory/constitution.md` unconditionally on **every** project — `ensure_constitution_from_template`, called at `src/specify_cli/commands/init.py:931` — so a file-exists predicate fails on the ordinary path, not on a rare corner. The sidecar `.constitution-template.json` is refused too, and for a sharper reason: it answers *"were these bytes materialized"*, and an organisation preset that ships a **ratified** constitution as a `constitution-template` produces a document that is materialized *and* ratified. The sidecar routes it to authoring, and authoring **overwrites**. That is the one unrecoverable failure available in this design.

The anchors test is also deliberately **not** a write-contract-conformance test. A ratified document may legally carry a preamble, extra `##` sections and rationale paragraphs the contract would never have written; this repo's own constitution does all three. A conformance predicate would route most real ratified constitutions to authoring.

A2 through A5 failing means a file is present and about to be overwritten, so the flow says **which** condition failed on the line the announced count would otherwise occupy — [§4.6.11](#4611-the-authoring-mode-line). That statement costs zero decisions.

*Settled in [Amendment mode: the short "what changed?" interview](https://github.com/DigitalWink/spec-kit-for-human-team/issues/6). The output-floor ticket re-verified that A4 stands as written and corrected only its justifying sentence: `templates/commands/converge.md` names **no** token or placeholder syntax anywhere in its 273 lines, so A4 is a sound unilateral choice rather than a match to `/converge`'s predicate.*

### 3.2 The pre-walk scan

**One bounded pass, hard stop. At most 40 file reads and 13 listings-or-searches**, divided across five rows whose numbers are each derived from the procedures the row names.

| Phase | Reads | Listings + searches | What it buys |
|---|---|---|---|
| **0 — Shared sources** | 3 | 2 | build manifest(s); repo-root listing; CI/workflow directory listing |
| **1 — Trigger resolution** *(first-priority)* | 5 | 5 | the five conditional triggers (§3.2.1) |
| **2 — Measured sets** | 1 | 3 | dependency set, artifact ceiling, surface grammar |
| **3 — Content inference, remaining areas** *(residual)* | 31 | 2 | negative scope, testing, irreversible operations |
| **4 — Gate probe** *(last)* | 0 | 1 | the `Constitution Check` search over the installed `speckit.plan` |
| **Total** | **40** | **13** | |

**Phase 0 is charged first because triggers depend on it** — four of the five triggers key on the manifest and two on the workflow listing. Reading a shared source once and spending it many times is what makes the allocation fit.

**Trigger evaluation is first-priority spend.** All five triggers resolve before any remaining budget goes to content inference. The asymmetry is not a preference: content inference degrading is harmless, because the area step exists either way and falls through to a menu or a cold step; an unevaluated trigger changes the **announced count**.

**The gate probe is last, and it has its own row.** It is the one measurement that changes no number — it changes one conditional paragraph — so it must never compete with trigger resolution, and the honest place for the lowest-priority spend in a forward-only budget is the end. Phase 0 is the wrong home on the same test: nothing depends on the probe.

**Spillover is forward-only.** Unspent budget in an earlier row rolls into later rows; a row never borrows from a later one.

**Two polarities.** An unresolved **content** signal becomes a menu or a cold step, never a deeper search. An unresolved **trigger** does **not** fire, and lands one `Trigger unresolved` row in the Deferred/Assumed table.

**Phase 3 is read-heavy and search-light by design.** A content phase that hoarded searches would be budgeting for a behaviour the design forbids.

*The budget's number and unit were settled in [Stop rule: how the constitution interview knows it is done](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2); its allocation in [Inferred sets in the constitution](https://github.com/DigitalWink/spec-kit-for-human-team/issues/10); and the five-row table, the move from 12 searches to 13, and the probe's own row in [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15). The earlier 12 was the sum of a table with one row mispriced and one row missing.*

#### 3.2.1 Trigger detection

Signals come from [Taxonomy of areas, and the already-enforced-elsewhere router](https://github.com/DigitalWink/spec-kit-for-human-team/issues/5); sources and ceilings from [Inferred sets in the constitution](https://github.com/DigitalWink/spec-kit-for-human-team/issues/10), with trigger 4's content condition from [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).

| # | Conditional area | Signal | Sources | Cost |
|---|---|---|---|---|
| 1 | Failure semantics and operating envelope | HTTP/network client dependency, subprocess invocation, or writes outside a temp dir; or a metered-inference SDK (`openai`, `anthropic`, `google-generativeai`, `bedrock`, `cohere`, `mistralai`) or a stated cloud budget | phase-0 manifest **+** one search for subprocess / `Popen` / `os.system` **+** one search for non-temp file writes | 0 reads, 2 searches |
| 2 | Security and privacy at the data boundary | an auth / identity / session / password-hashing / crypto / payment SDK in the manifest; **or** a person-identifying column; **or** a redaction or masking helper. **Never** `SECURITY.md`, CodeQL, gitleaks, SBOM or a dependency audit — those are the route-out signal, so a trigger keyed on them guarantees refused content | phase-0 manifest **+** one search for redaction/masking helpers **+** the model/migration listing shared with trigger 4 | 0 reads, 1 search |
| 3 | Supported platform and runtime matrix | more than one OS **or** more than one runtime version in the CI matrix; **or** OS trove classifiers, `browserslist`, a declared minimum mobile SDK. A manifest version floor alone MUST NOT fire it | phase-0 workflow listing **+** up to 2 reads of the test/build workflow **+** phase-0 manifest for classifiers | 2 reads, 0 searches |
| 4 | Data lifecycle | a migrations directory, ORM model files, a database client dependency, or a seed/fixture script | phase-0 manifest **+** one search for `migrations` / `models` / `seeds` / `fixtures` paths. **A path hit fires the trigger only when the same phase also finds a database client in the phase-0 manifest, or an ORM base class or table mapping in one read of a matched module — the lexicographically first source module under the first matched path, `__init__` files excluded. With neither, the trigger does not fire.** | up to 1 read, 1 search |
| 5 | Release and rollback for a running service | a CD job targeting a deployment environment, or an IaC/container deploy descriptor. Publishing a package to a registry does **not** fire it | phase-0 workflow listing **+** up to 2 reads of a deploy-looking workflow **+** one search for `Dockerfile` / compose / `*.tf` / k8s descriptors | 2 reads, 1 search |

**Trigger 4's content condition is load-bearing, not defensive.** The bare path search hits `src/specify_cli/bundler/models/` on this repo — three modules of plain `@dataclass` types, no ORM, no database client, no user data. Read literally, three conditionals fire and this repo announces **10** decisions rather than 9, which would make the design's own headline measurement rest on an unspecified disambiguation. Measured at HEAD: 8 `@dataclass` declarations, zero ORM imports, and none of the nine runtime dependencies is a database client. **Trigger 4 does not fire, and this repo announces 9.**

Three consequences of the fix, each a place it could be misread:

1. **The read is charged to phase 1**, which allocates 5 reads across the three triggers that read (trigger 3 up to 2, trigger 5 up to 2, trigger 4 up to 1).
2. **One read, then stop.** No second read if the first is inconclusive.
3. **A trigger that does not fire this way is *resolved*, not unresolved.** No `Trigger unresolved` row and no scan-budget line: the procedure ran to completion and returned *no*.

**Which three fire, when more than three trigger.** The ordinal rank above is both the selection key and the ask order. Take the top three that triggered; the losers land `Conditional not asked` rows with their trigger evidence. An `impact × uncertainty` key is refused: it is a silent model judgment, so the same repo would yield different interviews on different runs, and reproducibility is the only thing that justifies announcing a number at all.

#### 3.2.2 Progress lines

One line per phase, five lines, **phases and never filenames** — a filename stream over a 40-read scan is exactly the noise the format exists to avoid. Text in [§4.6.1](#461-the-scan-progress-lines). The probe's line prints in all three probe states.

#### 3.2.3 The gate probe

> After `{SCRIPT}` returns and the template precondition check passes, and **before** the announced count: search the installed `speckit.plan` command file for this agent — the sibling of the file this command was loaded from, named `speckit.plan` or `speckit-plan` (dot or hyphen depending on the agent), or laid out as `speckit-plan/SKILL.md` — for the string `Constitution Check`.
>
> - one or more occurrences → **gate present**
> - zero occurrences → **gate absent**
> - file not found or unreadable → **not determined**

**A content predicate over the resolved stack, never a `lean` name check.** A name check would miss every project override (`.specify/templates/overrides/speckit.plan.md` is priority 1 and beats every preset) and every third-party or organisation preset, which is the likelier case — and it would hard-code a sibling preset's id into this one.

**The command, not the template.** The gate is two parts: the section in `plan-template` where the answer is written, and the instruction in `speckit.plan` that fills and re-checks it. `lean` ships no `plan-template` override, so `## Constitution Check` survives in `.specify/templates/plan-template.md` while nothing reads it — a template-side probe returns a false negative on the one stack everyone cares about.

**Named blind spot, accepted:** a stack that keeps core's `speckit.plan` but replaces `plan-template` and drops its `## Constitution Check` section reads as *gate present*. The command is the actor, and a false *"your gates do not work"* on a healthy stack is the worse error. No shipped preset does this.

**It is non-blocking, asks nothing, and changes no step** — not the walk order, not the areas, not the triggers, not the quota, not the write contract. It appears twice, on lines that already print: a **stack fact** on the announcement when the probe says *absent* ([§4.6.2](#462-the-announced-count)), and a **three-state status line** on the routing table ([§4.7.1](#471-the-routing-table)). No fourth closing artifact and no Deferred/Assumed row — an environment fact the user cannot reverse in the correction round does not belong in a table of reversible content defaults.

**It changes no admission verdict.** A plan-anchored rule still names an artifact that exists; `lean` still writes `plan.md`, and `/speckit.analyze` still maps requirements against it. What is lost is the **blocking moment**, not the check, and the warning says exactly that. On a `lean` stack `/speckit.analyze` and `/speckit.converge` are untouched, and those carry the larger share of the enforcement teeth — so the warning must say *inform but do not block*, never *enforced by nothing*.

*Settled in [Behavior when the preset stack has no Constitution Check gate](https://github.com/DigitalWink/spec-kit-for-human-team/issues/9); given its own budget row and its own progress line in [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).*

### 3.3 The announced count

**7 to 10 decisions for authoring** — six always-ask areas, plus 0 to 3 fired conditionals, plus one Governance confirmation. Announced **exactly**, once, before the first question, on a line that also **names what it excludes**. Text in [§4.6.2](#462-the-announced-count).

Three requirements, each load-bearing:

- **The number is exact, not a ceiling.** The satisficing collapse is priced so the count never moves (§3.9), and `$ARGUMENTS` never moves it either ([§6](#6-arguments)).
- **It names what it excludes.** Push-backs, disambiguations, the negative-scope follow-up and the correction round are all real typing the number does not cover. Disclosing that is the same honesty standard that made the Governance confirmation charged.
- **It sits between the scan's last progress line and the cold first question**, with nothing between it and real content.

**Progress headers are `Step N of M`**, where M is the announced total and N counts every charged decision in order, Governance included. Fired conditionals are numbered inline among the always-ask areas so they stay indistinguishable in the walk. Governance reads `Step M of M — Governance`, never `Area M of M`.

**The Governance confirmation is charged.** It is one answer that closes one thing and it asks a genuinely non-inferable field. Calling it free while the user still pays for it is an accounting lie.

*Settled in [Stop rule: how the constitution interview knows it is done](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2).*

### 3.4 The walk

**One decision = one area step = one answer that closes an area.** This holds identically across all three step shapes. The interview walks areas one at a time; the next area appears only after the current one closes. There is no whole-document draft presented for edits-by-number.

**Eleven areas can be asked** — six always-ask, five conditional. Governance is not an area.

**The walk order is fixed, and positions 1 to 4 are forced rather than preferred.** Re-opening a closed area is refused, so an area whose answer can invalidate an earlier rule must run before it. Three one-way feeds exist:

- **Negative scope → everything.** It holds an exclusive claim over "we do not add X" rules, which otherwise collide with the dependency area.
- **Dependencies → architecture and surface contract.** The same build manifest supplies the artifact ceiling architecture counts; the stack supplies the grammar the surface contract governs. No verb-grammar answer ever changes the manifest.
- **Architecture → surface contract.** The extension-point carve-out decides which registry members are sanctioned surface.

| Step | Area |
|---|---|
| 1 | Negative scope — non-goals and forbidden alternatives |
| 2 | Technology and dependency boundaries |
| 3 | Architecture and structural limits |
| 4 | Surface contract — consistency and compatibility |
| 5 | Irreversible and destructive operations |
| 6 | Testing and verification |
| 7…9 | Fired conditionals, in the rank of §3.2.1 |
| last | Governance confirmation |

Position 1 is forced twice over: negative scope is the only **cold** step, so the user generates before seeing any pre-filled rule, and a cold step can never be a verbatim accept, so it can only break a collapse run — position 1 is the one slot where breaking costs nothing. Positions 5 and 6 are free; testing takes the last area slot because its answer is the most predictable in the walk, and irreversible operations takes position 5 because its content is the constitution's killer items, which should not sit in the cheapest slot.

**Governance sits after the last area step and before the self-review**, for two mechanical reasons. Route-out lines accumulate as `## Governance` lines during the walk, so the block cannot be pre-filled until the last route-out exists. And placing it after the conditional tail keeps the one genuinely non-inferable field outside the collapse window, so it is asked on every run.

**No going back mid-walk.** The user cannot re-open a closed area while the walk is running. The interview does not refuse — it says the request lands in the correction round and continues ([§4.6.5](#465-no-going-back-mid-walk)). Re-opening on demand makes the walk unbounded, and the closing pass is the cheaper place to fix an earlier answer anyway, because the user sees it in the assembled document rather than from memory.

#### 3.4.1 The three step shapes

Every area resolves through exactly one shape, and every shape costs one decision. Frames in [§4.2](#42-the-three-frames).

- **Pre-filled (A).** The scan produced a candidate rule. Shows the rule, its gate question, and its provenance — source, extraction and threshold, never only a file. Set-bearing steps extend it with a fourth element (§3.7).
- **Menu (B).** The scan produced no candidate rule but **can name a concrete repo fact** that says which rules are worth offering. Nothing is pre-selected; picking none is a valid answer that closes the area. The naming fact is a hard gate: with no fact the area falls through to a cold step.
- **Cold (C).** An open question, no candidates, free-text answer.

**A step carries either a gate question or a *why it matters* sentence — never both and never neither.** The two fill one slot, and the gate question is the stronger filler wherever a rule already exists.

**Conditional areas.** A fired conditional runs as a normal step with the same shapes and the same anatomy. An unfired conditional produces **no step at all** — it does not degrade into an open question. An unresolved trigger does not fire and is logged.

#### 3.4.2 What fills a menu

**A shipped strictness ladder, keyed to the area and never to a project type.** By the time a candidate reaches a menu the area has fixed the topic, the admission test has fixed the form, the obligation quota has fixed the length, and the ban on sets and numbers has stripped out everything stack-specific — so project type buys selection along an axis already closed. What is left variable is **strictness**, and strictness is type-independent: a hobbyist Python CLI and a regulated Java service want the same testing *topic* at different *strengths*.

**The nesting rule is the definition, not a heuristic: each rung forbids a strict subset of what the rung above forbids.** Four consequences:

1. **The order is total, fixed and reproducible** — strictest first, a property of the shipped text rather than of the run.
2. **The length is capped by the forbidden-alternative test, not by a number.** A ladder ends at the last rung that still forbids something a competent engineer would otherwise do. That yields **1 to 3 rungs**, most often 2. The ceiling of five stands and is never reached; a ladder is never padded.
3. **The quota is satisfied by construction.** Rung 1 is the area's own settled exemplar, which already fits the quota; every rung below forbids less. The draft-time quota check is a guaranteed no-op on a menu step.
4. **A non-nested variant is reachable by editing**, and costs nothing extra. The alternative — a flat list of independently pickable clauses — is a shopping list of obligations, which is the mechanism that produced today's 46.

**Strictest first**, deliberately, because the two failure directions are not equally detectable. A rule that is too strict fires on a plan that should have passed — loudly, at `/plan`, and CRITICAL at `/analyze` — so the user sees it and pushes back. A rule that is too weak **never fires at all**. Anchoring toward the loud, correctable failure is the right bias.

**Rung 1 is already in the settled record.** For the three set-bearing areas it is exactly the residue that survives when the measured set cannot be produced (§3.7.3); for every other area it is that area's settled exemplar, unchanged.

**Generated-per-run is refused** on reproducibility — a candidate authored at run time from a detected stack is model judgment over prose by construction, so two runs on the same repo would offer different menus. It also has no authoring-time review, so its genericity could only be checked at run time by the model that just wrote it.

**The genericity screen runs twice**, and neither run costs anything: on the **rung text at authoring time**, before the preset ships, against the full route-out table and the admission test; and on the **repo fact at run time**, which is what decides whether the menu opens at all. A picked rung is caught a third time after the fact — it is by construction a rule the scan found no evidence of, so it already lands a `No evidence found` Deferred/Assumed row through settled machinery.

**Where the menu actually lives is an *early* repo** — a project with facts but no practice. A **greenfield** repo has no facts at all, so every area falls to a cold step and the ladder is never read; a mature **brownfield** repo pre-fills instead. Measured: **zero of nine** steps reach a menu on this repo, and zero on an empty one. The ladder is reachable only in between, and also on a fired conditional whose trigger fact exists but whose convention is unobservable, and on a brownfield area whose measured set cannot be produced and whose residue is empty.

One consequence a builder should weigh when authoring rung texts: the ladder is **most valuable exactly where the gate refuses to open it**. On a greenfield repo the irreversible-operations step — the largest quota, the constitution's killer items — produces nothing, and a shipped rung about dry-runs and idempotency guarantees is precisely what that step wants.

**A deliberate "pick none" costs one decision, breaks the collapse run, produces no principle, and gets no Deferred/Assumed row** — it is an answer, not a default, and the table exists to make defaults reversible. *"I don't know"* on a menu step **means** pick none and **does** get a row.

*Settled in [Where the menu's candidate rules come from](https://github.com/DigitalWink/spec-kit-for-human-team/issues/11), with the early-repo finding measured in [Prototype: a full sample interview transcript](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8) and the `drops:` delta added by [Per-area question wording](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12).*

### 3.5 The admission test and the route-out table

Two filters, in this order, on every candidate rule.

#### 3.5.1 The route-out table runs first

It is the **pre-admission** filter and it is net-negative on the decision budget: every candidate it refuses is a decision the user never has to make. A route-out verdict is **not overridable**.

| Category | Destination | Detection |
|---|---|---|
| Coding style, formatting, language idiom | Linter/formatter config, **naming the exact rule to enable** | Object is a token- or file-level form (import style, type syntax, identifier casing, member order) and a named tool can express it as a config rule |
| Build/test/run commands, repo layout, environment setup | `AGENTS.md` | Answers "how do I run or find things here?" not "what may a change do?" |
| One-time decisions and past trade-offs | An ADR | Names a chosen-over-rejected pair, or is past tense, and constrains no future plan |
| Feature requirements and product scope | The spec, via the Scope Guard deferred-intent path | Names a specific feature, screen, endpoint or user outcome; becomes moot once it ships |
| Values and aspirations | Cut | Inversion test: no competent team could defend the opposite |
| Descriptive overview prose | Cut | Declarative sentence about what the project *is*, no modal verb, deletion changes no decision |
| Standard defaults the agent already applies | Cut | Inverting it would be surprising in *any* project, not just this one |
| Lint / CI / branch-protection restatement | The tool's config; at most one `## Governance` line | A named tool, **in a config you have opened**, already fails the build |
| Merge and branch policy | Branch protection; one Governance line | About pull requests, approvals, required checks, direct pushes. No consumer observes merges |
| Dependency licence policy and provenance | A licence-scan CI job plus the contribution acceptance gate; one Governance line | Object is a licence identifier or the origin of vendored content. Only the transitive closure matters legally, and no plan section enumerates it |
| Agent conduct outside the repo's artifacts | `AGENTS.md` | Violation observable only in a PR body, review comment, or commit message |
| Code-review standards needing a diff or a repo-wide sweep | `AGENTS.md` or a lint rule | Answering needs a comparison against a previous revision, or enumerating call sites repo-wide. Nothing in the pipeline reads diffs |
| Version pins and brittle paths | The plan, the manifest, or config | Names a non-floor version, or a path/symbol whose rename would not fail the build |
| Already stated in a governance doc this project maintains | Cite once in Governance; do not duplicate | Same obligation already in `AGENTS.md`, `CONTRIBUTING.md`, or a linked standards file. Partial overlap measurably degrades adherence to its neighbour |
| Outcome facts verifiable only in production or a future release | A named CI job, or a named human gate in Governance | Answering requires an event that has not happened — a rollback window, a restore drill, a measured p95, a verified backup |

**Two mandatory carve-outs on the style route-out**, or it deletes real rules:

1. Naming of the product's **external** surface — command verbs, endpoint paths, flag names — is surface-contract content, not style.
2. A naming rule whose violation **breaks the build or the test suite** is not style. Example in this repo: *the integration `key` MUST match the executable name so `shutil.which(key)` resolves.* That looks like a convention and is a functional requirement.

**Apply the lint/CI route-out by opening the config, never from the tool's reputation.** Verified counter-example here: the entire ruff lint configuration is `extend-select = ["S602", "S604", "S605"]` and CI runs `uvx ruff@0.15.0 check src tests`, so `UP`, `FA`, `I`, `N`, `B904` and `S113` are all **off**. Modern type syntax, future annotations, import ordering and `raise ... from exc` are therefore *not* already enforced here — they are refused by the **style** route-out instead, which runs before the admission test, and the refusal must be **constructive**: name the ruff rules to switch on.

**Code idiom is routed out even though it is admissible**, and the reasoning is worth carrying. Four idiom rules on this repo pass all three admission tests and no linter enforces them. They are still refused, for two reasons: idiom is the single most reliably inferable thing about a brownfield repo, so an inference-first interview will surface it first and, given a slot, mint it as principles — which is how today's document reached 46 obligations; and the content is one config line away from being enforced properly, which is strictly better than a MUST no gate reads.

#### 3.5.2 The admission test

A candidate that survives the route-out table becomes a principle only if it passes all three.

- **T1 — Gate-restatement.** The rule restates, with no added information, as a yes/no question about one specific implementation plan. Its by-product is the step's **gate question**, so surfacing that costs nothing.
- **T2 — Forbidden-alternative.** The rule names the cheaper thing a competent engineer would otherwise do. A rule compatible with every possible implementation is not a rule.
- **T3 — Evidence.** The rule names the artifact that **answers** the checkbox.

| Tier | Artifact | Who reads it |
|---|---|---|
| **Strong** | `plan.md` prose — Technical Context, architecture and stack choices, technical constraints | `/plan` writes, `/analyze` audits |
| **Strong** | `tasks.md` — task descriptions and file paths | `/plan` writes, `/analyze` audits |
| **Strong** | feature-scoped source reachable by `/converge` | `/converge` |
| **Weak** | `data-model.md`, `contracts/`, `## Complexity Tracking` | `/plan` only — self-attestation |
| **Strong** | a named human gate (specific role, specific moment) | a person |

A rule whose only honest answer is *"read the whole codebase"* fails T3. **The interview prefers a strong anchor when one exists**; a rule anchored only on a weak artifact is still admissible but is checked by the agent that wrote the answer, so it is audit-free — and the routing table says so ([§4.7.1](#471-the-routing-table)).

Two rules ride with the test:

- **Durability.** Prefer named constructs and types over file paths and versions. A named symbol, file or command is allowed only when renaming it would fail the build or the test suite. A version is allowed only as a **floor**, never a pin. **Mandatory carve-out:** where the constitution is the only file both `/plan` and `/analyze` load, an inline enumeration is the *correct* durable form — a dependency rule pointing at "the project manifest" fails T3, because no consumer reads `pyproject.toml`, so `/plan` writes an unfalsifiable PASS. Enumerate inline; do not point at a file.
- **Tier is internal.** Plan-visible, converge-visible and human-gate decide admission and are **never** written into the document as a label. No downstream command reads such a tag. It belongs in the routing table.

*Settled in [Taxonomy of areas, and the already-enforced-elsewhere router](https://github.com/DigitalWink/spec-kit-for-human-team/issues/5).*

### 3.6 Uncharged turns

**A turn that costs no decision prints no `Step` header, and one that continues the current step opens with the word `Still`.** One learnable signal for "this is not a new decision" — which is what the announcement's exclusion clause promises and could not otherwise deliver turn by turn. Measured on the prototype's four arms, this convention carries five turn kinds and is legible; the missing `Step N of M` header is the real signal and `Still` is the label on it.

| Turn | Cap | Text |
|---|---|---|
| The negative-scope follow-up | once, mandatory, prints even when empty | [§4.6.3](#463-the-negative-scope-follow-up) |
| A push-back on an inadmissible rule | at most once per area | [§4.6.4](#464-the-push-back-and-the-route-out-verdict) |
| A route-out verdict | not overridable, re-prints the step's prompt | [§4.6.4](#464-the-push-back-and-the-route-out-verdict) |
| A disambiguation | does not advance | [§4.6.6](#466-disambiguation) |
| The amendment set diff | once, mandatory | [§5.4](#54-step-1s-mandatory-follow-up--the-set-diff) |
| The correction round | one round | [§4.6.10](#4610-the-correction-round) |

**Push-back and route-out are two different failures with two different behaviours.**

- **Admission failure — one push-back, then the user wins.** The interview says once why no gate can check the rule, names where it belongs, and offers a checkable rewrite. If the user insists, the rule is written **as they wrote it** and the routing table records that no consumer can observe it. Silently routing out a rule the user personally typed is the "the tool dropped my rule" failure the routing table exists to prevent, and it is worse when the user wrote it than when the scan produced it.
- **Route-out verdict — not overridable.** Content the route-out table refuses is refused. The interview names the destination constructively and records the line in the routing table.

An unenforceable rule is the user's call to make badly. A rule that duplicates a gate that already exists is not.

*Settled in [Interview mechanic: draft-first, question-first, or hybrid](https://github.com/DigitalWink/spec-kit-for-human-team/issues/3); the `Still` convention in [Per-area question wording](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12).*

### 3.7 The measured sets

Three sets, three measurement kinds. All three ride on area steps the walk already runs — dependencies at step 2, architecture at step 3, surface contract at step 4 — so they cost **zero new decisions**.

| Set | Kind | Source | Threshold |
|---|---|---|---|
| Runtime dependency set | **Declared** — a manifest enumerates the members | the build manifest | none; exhaustive by construction |
| Surface verb and flag grammar | **Observed** — members are scattered across source | source tree, by search | recurrence ≥ **3** declaration sites |
| Distribution-artifact ceiling | **Scalar** — a count, not a set | the build manifest | none; measured exactly |

Each is produced by a **declared measurement**: a named source, a named extraction, and a stated threshold, deterministic and re-runnable, printed in the step's provenance line. **Never model judgment over prose** — two runs on the same repo must produce the same set.

#### 3.7.1 The three procedures

**Dependency set — declared, exhaustive.** The runtime dependency table only; dev, test, build and optional groups are excluded, because they are not what a plan adds when it adds a dependency. **Names only, no version specifiers** — a version specifier inside an enumerated set is a pin wearing a different costume, and the runtime version floor is a separate clause that stays a floor. Cost: 0 additional reads; phase 0 already read the manifest.

**Surface grammar — observed, thresholded.** By search, never by reading the files that declare the members — this repo has 84 `.command(` sites across 12 files, so a read-based measurement would spend a third of the scan budget on one clause of one area. **Three searches**, two forms per language surface plus flags:

```
grep -rhoP '\.command\(\s*"[^"]+"' src --include=*.py     # explicitly named
grep -rn   '\.command()' src --include=*.py -A1           # name defaults to the function
grep -rhoP '"--[a-z0-9][a-z0-9-]*"' src --include=*.py    # flags
```

The defaulted form is not optional: `@app.command()` with no name string takes the function name, and on this repo `version` appears in no other form, so a single-pattern search would drop it entirely. A declared measurement that is wrong is worse than no measurement, because it looks authoritative.

**Threshold: a member is in the grammar when it is declared at 3 or more sites.** Members at 1 or 2 sites form the **near-miss band**, disclosed at confirmation and addable by the user. Three is the smallest number that distinguishes a convention from a coincidence, and it reproduces the only human-authored fixture available: this repo's hand-written constitution names exactly the measured ≥3 set plus `install`, which measures at 2 and sits in the band.

**Cost: 3 searches, 0 reads.** Plus at most 1 read of the entry point if all three searches return empty.

**Artifact ceiling — scalar.** The count of declared build or distribution targets in the same phase-0 manifest. **What is counted and what is not must both be named**, because this is the number most likely to be misread: word it as *"distribution artifact declared in the build manifest"*, never *unit* or *project*. On this repo `[tool.hatch.build.targets.wheel]` is the only declared target — **1** — while `force-include` ships **7** independently versioned units inside that one wheel, and none of them is a distribution artifact. Cost: 0 additional reads.

**The template's `3` is never used and was never a ceiling.** `templates/plan-template.md:112` carries it inside a Complexity Tracking *example row*, illustrative placeholder text in a table the plan author fills. **The default is the measured current count, with no invented headroom** — adding headroom would be a silent model judgment. If the user wants headroom, the edit affordance is right there.

**Backstop for an oversized observed set.** If an observed set still exceeds **20 members** at threshold 3, raise the threshold by 1 and re-measure, until it fits or the threshold exceeds the highest recurrence found. If it never fits, treat it as unmeasurable (§3.7.3). The threshold actually used is printed in the provenance line, and a raised threshold lands a `Threshold raised` Deferred/Assumed row.

#### 3.7.2 A measured set is a declarative sentence

> A measured set is written as its own **declarative sentence**. The obligation is a separate clause that **refers** to it.

**Consequence: a measured set costs zero obligations.** A definitional sentence carries no normative verb in any casing, so it does not count against the per-area quota. This is not a loophole in the case-insensitive counting rule (§3.8) — that rule closed a gap for keyword-free *normative* prose written to dodge the quota; a sentence that defines a set is not normative in any casing, and the obligation that constrains a plan is stated separately, in full, and counts in full.

This is where the surface contract's missing room comes from. It sits at exactly three obligations with none left for the T3 artifact naming it needs; under this form the space comes from inside the existing clause rather than from a fourth one.

**The accepted rule string is the substituted text.** The interview materialises the set into the sentence at accept time. It never stores a template plus a member list, and **it never re-measures at write time** — the write contract requires the principle body to equal the accepted rule strings verbatim, and the self-review tests containment against them, so both are meaningless if the accepted string still carries a placeholder.

**Provenance stays out of the document.** A line reading *"from .github/workflows/test.yml"* is not an accepted rule string, so it is a framing sentence by definition and the self-review's body-fidelity check would delete it on every set-bearing principle, on every run. Outside a principle body it is worse: a `## Provenance` section is net-new document structure no consumer reads. Provenance lives **in the walk**, as the step's third element, and **in the Deferred/Assumed table** in exactly three cases — a set that could not be measured, a set accepted inside a batch summary, and a set whose threshold was raised.

#### 3.7.3 When a set cannot be measured

**Every set-bearing rule has a set-free residue**, so an unmeasurable set never empties an area:

| Area | Clause that carries the set | Residue that survives without it |
|---|---|---|
| Dependencies | `The runtime dependency set is: …` plus the MUST NOT that references it | version floors, never pins; a version change is recorded in the manifest, not by hand-editing the lockfile |
| Architecture | the ceiling number | no single-implementation abstraction outside a declared extension point |
| Surface contract | `The measured … grammar is: …` plus the MUST NOT that references it | no removal or rename without naming the replacement; a user-visible change names the document it updates |

**A set the phase-2 ceiling cannot produce is not guessed and not searched harder.** The set-bearing clause **is not offered**, the residue clauses are offered as a normal pre-filled step, and one `Set unmeasured` row records it. If the residue is empty too, the area falls to a menu or a cold step. This never changes the announced count, because the area step exists either way.

#### 3.7.4 Two boundary rulings

**A member edit is not user-typed.** The clause is the declarative sentence, and the interview composes that sentence from a member list — so `drop 7, 12` and `add httpx` change the member list, not the sentence, and the accepted clause contains no user-typed characters. Body fidelity stays live over the set sentence, which is exactly where verbatim fidelity matters most: a set is data, and a mis-joined set is a broken gate. **Typing replacement prose for the rule is user-typed**, and carries the full quota and self-review exemptions.

**A member edit breaks the satisficing-collapse run.** A drop-by-number or an add is an edit. This matters because the two inventory-bearing steps sit at positions 2 and 4, inside the window where the collapse can arm: a user who actually reviews a set has demonstrably not collapsed.

#### 3.7.5 Freshness needs no new machinery

**The rule's own gate is the drift detector, and it is already loud.**

| Set | Stale because a member was **added** to the repo | Stale because a member was **removed** |
|---|---|---|
| Dependency set | fires — loudly, at `/plan` and CRITICAL at `/analyze` | silent, and harmless: the rule permits less than it could |
| Verb / flag grammar | fires — loudly | silent, and harmless |
| Artifact ceiling | fires — loudly | silent, and permissive |

**No direction in which a measured set goes stale produces a silent wrong PASS**, which is the failure mode T3 exists to catch. Better still, **drift and violation are the same event**: a plan that adds a dependency outside the set *is* the event the rule exists to flag, the rule already names the remedy (record it in Complexity Tracking, naming the alternative rejected), and the amendment interview is the repair. A drift check emitted as a principle is refused: a script nothing in the pipeline runs is not a legal answering artifact, and the detection it would buy already exists, louder and earlier, in the gate.

**Stated and not fixed:** a thresholded set is a **grammar, not an inventory**, and it can flag a real member. A plan that adds a below-threshold flag trips the gate once; the remedy is the designed one. The near-miss band at confirmation is the user's chance to prevent it, and what they leave out they leave out knowingly. Exhaustive sets were refused on reviewability — nobody reviews a 61-item list inside a walk step.

*Settled in [Inferred sets in the constitution](https://github.com/DigitalWink/spec-kit-for-human-team/issues/10); the three-grep pricing corrected in [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).*

### 3.8 The obligation quota

An **obligation** is one MUST, MUST NOT or SHOULD clause, counting each semicolon-separated sub-rule separately, **counted case-insensitively over normative clauses**.

| Area class | Quota |
|---|---|
| Always-ask areas | **3** |
| Irreversible and destructive operations | **4** |
| Fired conditionals | **2** |

Maximum **25** obligations across a 9-area run. This quota **supersedes** any global obligation cap; the two never run together.

**Case-insensitive counting is load-bearing, not tidiness.** The self-review repairs a lowercase `must` in drafted prose to `MUST`, on the settled ground that capitalisation is a form repair. Counted case-sensitively, a drafted clause written *"a plan must not add a dependency outside the set"* counts as **zero** at draft time and becomes a real obligation after the repair — so a rule could pass its quota and exceed it in the written document. Quota *pressure* creates exactly that incentive: an agent held to three obligations gains headroom by writing keyword-free normative prose, which is the form the repair later promotes.

**Enforced at draft time, inside the area step, before the user sees the rule.** Not a post-write check. **A rule the user typed is exempt entirely** — after one push-back the rule is written as they wrote it, and trimming a clause afterwards is not writing it as they wrote it.

**A per-area quota trim produces no Deferred/Assumed row and no routing-table line.** It is enforced before the user saw the rule; logging it would flood the table and invite the user to re-add the obligations the budget exists to prevent. Only a drop crossing the **25-obligation document cap** is logged.

**The overflow default**, the one default the stop rule owns, because overflow is a budget event:

> On overflow, drop the last-admitted obligation from whichever area currently carries the most obligations. Never an obligation from a rule the user typed. Ties break by reverse walk order — the latest area loses first. Log the drop as an `Obligation dropped` row.

No ranking key is invented; no settled ticket defines a ranking over admitted rules. The alternative — asking which existing principle the new one replaces — is refused in that form and in any other.

**The `SHOULD` term is inert in the per-area count** and stays live in the document-level count. The interview never *offers* a SHOULD; a user-typed SHOULD survives verbatim and is quota-exempt, but it is still an obligation the accounting line counts. No BCP-14 boilerplate is added.

**A caution the record corrects:** *"SHOULD buys zero enforcement"* holds for `/converge` only. `/analyze` attaches CRITICAL to a constitution conflict with **no keyword qualifier at all**, so a user-typed SHOULD is plausibly CRITICAL there. Any Deferred/Assumed row telling the user otherwise must be narrowed to `/converge`.

*Settled in [Stop rule](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2), amended to case-insensitive counting by [Self-review pass: which quality checks run before writing](https://github.com/DigitalWink/spec-kit-for-human-team/issues/7).*

### 3.9 The satisficing collapse

**When the user accepts 3 consecutive pre-filled rules with no edit, the fired conditionals not yet asked are presented as one batch summary instead of separate steps.** Text in [§4.6.9](#469-the-batch-summary).

Definitions, because the rule is unwritable without them:

- **"Consecutive" ranges over area steps, not over pre-filled steps.** A menu answer, a cold answer, an edit, a replacement, or "pick none" all **break** the run. Only accept-without-edit on a pre-filled step feeds it.
- **Push-backs, disambiguations and the negative-scope follow-up are transparent** — they neither feed nor break a run.
- **The run must be live at the collapse point.** A sliding window over the three most recent area steps, evaluated when the first conditional step would begin. Satisficing is a current state, not a history.
- **Floor: at least two fired conditionals must remain unasked.** With one remaining, it runs as a normal area step.
- **The batch blocks.** Silence is never a yes during the walk.

**Price: one decision per area the batch covers**, so the decision count never changes and the announced number stays exact. **What the collapse saves is round-trips, not decisions**, and the batch says so out loud.

The batch must name every area it covers, print each assumed rule in full with its provenance, and write every one to the Deferred/Assumed table as a `Batch assumed` row.

**It is not an escape hatch for an expensive interview**, and it fires rarely. It cannot fire on greenfield, where steps are menu or cold and conditionals rarely trigger. It cannot fire on this repo at all: only two conditionals fire, so the floor of two remaining is unreachable. It fires most readily on well-inferred brownfield runs where each collapsed step was already a one-keystroke accept.

*Settled in [Stop rule](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2), as a stated amendment to the rule that a fired conditional is indistinguishable from an always-ask area in the walk — for a collapsed tail it is not.*

### 3.10 The Governance confirmation

**Not an area — one confirmation, charged as one decision, always last before the self-review.** Text in [§4.6.8](#468-the-governance-confirmation).

Every clause in this repo's ratified Governance block was routed out by the dogfood: a precedence declaration fails T1; *"the Constitution Check MUST be evaluated"* fails T2, because it is already hard-coded in `templates/commands/plan.md` and `analyze.md` and the artifact the pipeline reads cannot instruct the pipeline; the semver rules and the Sync Impact Report are the command documenting its own mechanics inside its own output; *"every PR and review MUST verify compliance"* fails T3.

**But the section is mandatory and is emitted, pre-filled, on every run.** Omitting it leaves `[GOVERNANCE_RULES]`, `[CONSTITUTION_VERSION]` and `[RATIFICATION_DATE]` as bracket tokens, and `/plan` and `/analyze` have no unfilled-template carve-out. That is the worst available failure.

**Exactly one field is genuinely not inferable: who approves an amendment.** One person *answers* the interview; that says nothing about who *approves*. Everything else is pre-filled — ratified today, version `1.0.0`, consequence fixed by the pipeline.

**A generic default is safe here specifically.** The genericity screen governs principles, and Governance is not a principle — no consumer reads it. The pre-fill carries **no ALL-CAPS MUST**, so it cannot smuggle an unpriced obligation past the draft-time quota. That matters because principle-name extraction is not heading-scoped: an R1 Governance line written with an ALL-CAPS MUST would be extractable enforcement the quota never priced.

**One clause must not ship.** The dossier proposes writing *"principles are re-checked when a principle is waived twice in a row"* into Governance. It states an unimplementable trigger as a MUST — nothing aggregates Complexity Tracking rows, nothing counts them, and the rows name no principle — so it fails the evidence test and is exactly the decoration the admission test exists to refuse.

*Settled in [Taxonomy of areas](https://github.com/DigitalWink/spec-kit-for-human-team/issues/5); charged by [Stop rule](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2); the unimplementable clause ruled out by [Amendment mode](https://github.com/DigitalWink/spec-kit-for-human-team/issues/6).*

### 3.11 The write contract and the self-review

#### 3.11.1 The write contract

Owned by the preset, which owns the write. The self-review is its conformance pass, and every check exists only because this contract can be violated.

| | The write step MUST emit |
|---|---|
| **W1** | Exactly the mandatory structure of the resolved `constitution-template`: the `# <name> Constitution` H1, `## Core Principles`, `## Governance`, and the `**Version** \| **Ratified** \| **Last Amended**` footer. Nothing else. |
| **W2** | One `### <numeral>. <short name>` per rule accepted in an area step, directly under `## Core Principles`, numbered densely from `I` in walk order, skipping areas that produced no principle. |
| **W3** | Each principle body = **the accepted rule strings, verbatim, joined**. No invented rationale, no framing sentence, no paraphrase, no added modal clause. |
| **W4** | Zero `[ALL_CAPS]` tokens and zero `TODO(` markers anywhere in the file, including inherited template comments. Unused template slots are deleted whole, not left. |
| **W5** | The Governance block and footer filled from the fixed table in §3.11.2. |

**W3 is the load-bearing clause and the one most likely to be violated**, because the drafting agent's instinct is to explain and core actively pushes it that way at `constitution.md:109` — which the wrapper supersedes.

**The write contract governs what this flow may emit. It never defines what a valid constitution is.** A ratified document may legally carry a preamble, extra sections and rationale paragraphs the contract would not have written. Conflating the two would route most real constitutions to authoring (§3.1).

#### 3.11.2 The five checks

**The pass is not a review. It is a conformance check on the interview's own assembly**, and saying so is the substance of it. Nothing content-weak reaches it: the route-out table, the admission test, the durability rule and the obligation quota removed everything content-weak *before the user ever saw it*. So *"rewrites weak principles"* means **form-weak or document-weak**, never content-weak — and *"reports what it changed"* usually means it changed nothing.

| | Check | Acts? | Logs to |
|---|---|---|---|
| **S1** | Structural conformance | fill / rewrite | change log |
| **S2** | Body fidelity | rewrite (whole body only) | change log + Deferred/Assumed |
| **S3** | The accounting line | **no** | change log footer |
| **S4** | No repo evidence | **no** | Deferred/Assumed |
| **S5** | Overlap | **no** | Deferred/Assumed |

**Two act, three measure**, and on a well-formed run the acting half is a no-op.

**S1 — structural conformance.** Four regexes over the assembled draft, verifying W1, W2, W4 and W5.

- **Tokens.** Fires on `\[[A-Z][A-Z0-9_]{3,}\]` or the literal `TODO(`. **The length floor is 3, not 2, deliberately** — `[P]`, `[ID]` and `[US1]` are live task-marker idioms a surface-contract or testing rule may legitimately quote, and a floor of 2 still matches `[US1]`. A floor of 3 excludes all three markers and matches every token the core template carries, the shortest being `[PROJECT_NAME]`. Resolution is a **fixed table, never a judgment**: `[CONSTITUTION_VERSION]` → `1.0.0`; `[RATIFICATION_DATE]` and `[LAST_AMENDED_DATE]` → **today, ISO `YYYY-MM-DD`, one constant**; `[PROJECT_NAME]` → the manifest name the scan already read, else the repo root directory name; `[GOVERNANCE_RULES]` → the block the Governance confirmation produced. Unused `### [PRINCIPLE_n_*]`, `## [SECTION_2_*]` and `## [SECTION_3_*]` slots → **deleted heading-and-body whole**, after a containment test proves the region holds no accepted rule string. Any token **outside** the fixed table → **log-only**, never an improvised substitution. A token inside text the user typed → **log-only, left verbatim**.
- **Headings.** Every accepted rule is a `###` directly under `## Core Principles`, densely numbered. **Short name defaults to the area's own name** — never a model-invented title, which would make two runs differ and would smuggle in an emphatic `(NON-NEGOTIABLE)` label.
- **Footer.** `**Version**: 1.0.0 | **Ratified**: <today> | **Last Amended**: <today>`, dates ISO.
- **Governance.** Block present, carrying the amendment approver plus any route-out lines accumulated during the walk.
- **Comments.** **Delete every inherited `<!-- Example: … -->` comment unconditionally**, superseding core's judgment-shaped *"unless they still add clarifying guidance"*. They are 1,786 of the core template's 2,336 characters, and `constitution-template.md:47` carries a `[GUIDANCE_FILE]` token that would otherwise trip the token scan.

**S2 — body fidelity.** Fires when a principle body is not equal to its accepted rule strings, verbatim, joined — tested by **case-sensitive, whitespace-normalized containment** against the **set** of strings accepted for that area. **Action: whole-body replacement only. No sentence-level surgery** — sentence boundaries are unpinned in the settled record, and a wrong split deletes an accepted clause with no loop to catch it. **Insert-only against a set, never delete-then-insert**: negative scope provably yields more than one accepted string per principle, so a single-string assumption would delete content the user explicitly accepted. **"User-typed" is per-clause, never per-principle** — any characters the user typed, including an edit of a pre-filled rule and a single clause inside a mixed body — and a body containing any user-typed clause is **log-only**.

**The residue, stated not fixed:** a drafting-invented modal sentence sitting *outside* every accepted string, in a body that otherwise passes containment, **survives the pass** and is reported, not removed. Assembly can therefore mint an obligation the draft-time quota never priced. Removing it would need the sentence surgery this check refuses.

**S3 — the accounting line.** Never acts; prints on **every** run as the change log's footer. Both warns on the character budget are struck: they would fire on essentially every run against a threshold that is not binding here (§3.11.3). The principle-count warn survives at **9 or more**, log-only, one `Over principle count` row. Text and the zero-obligation clause in [§4.7.3](#473-the-change-log-and-the-accounting-line).

**S4 — no repo evidence.** An accepted rule names behavior the scan found no evidence of → **accepted verbatim, one `No evidence found` row.** No downgrade, no dated migration commitment — that needs an owner, which needs a question. **Greenfield flood control:** a cold answer has no scan evidence by definition, so on a greenfield run this would fire on every principle. **Emit one line, not N rows.**

**S5 — overlap.** Two principles share subject nouns, share a named tool, or one restates a subset of the other's obligations. **Log-only, one row naming both.** A **merge is refused** — it rewrites rules the user accepted, cannot ask which to keep, and changes the obligation count. The **observation is kept**, because overlap is the one defect draft-time structurally cannot see: it does not exist until assembly, no draft-time mechanism looks across areas, and `/analyze`'s own duplication pass is scoped to `spec.md`, `plan.md` and `tasks.md`. Overlap actively degrades adherence to the neighbouring principle, so leaving it unsaid is too expensive.

**Execution order is fixed**, and three adjacencies are outcome-determining:

**S1-headings → S1-tokens → S2 → S1-footer/Governance → S3 → S4 → S5**

- **Headings before tokens**, so the heading move rescues an accepted principle sitting under a section whose name slot survived assembly; token deletion would remove it first.
- **Tokens before body fidelity**, so deleting a token-bearing clause cannot strip the only modal from a body S2 is about to test.
- **Footer and Governance last among the acting checks**, so exactly one check writes the date bytes.
- **Measuring checks run after all acting checks**, or S3's numbers describe a document that no longer exists.

**No check ever asks the user anything.** Every check applies a stated, deterministic default and logs it.

**Thirteen dossier checks are dropped**, most as draft-time duplicates: gate-restatement, the instruction-vs-overview classifier and the ALL-CAPS repair are **absorbed** into S2; forbidden-alternative, named-enforcement-moment, the inversion test, the standing-rule router and the durability check are draft-time duplicates; the atomic-obligation count is superseded by the quota; the character and principle **budget warns** are permanently red or dead; the near-duplicate **merge** is forbidden; SHOULD-downgrade justification is unreachable; and the requirements-smell lexicon scan fires on nothing — measured zero hits across all nine settled exemplars, at ~59% published precision, over sentences the user personally approved.

#### 3.11.3 The character budget reports and gates on nothing

Two numbers, both reported, neither gating.

- Measured on this repo: `.specify/memory/constitution.md` is **13,213 characters**, its Sync Impact Report **1,512**, its body **11,699**.
- The ~6,000 figure is Windsurf/Cascade's `global_rules.md` cap — a whole-file **byte** budget with no comment exemption. The dossier's check excluded the report; the source does not.
- **The quota and a whole-file 6,000 cap are inconsistent by construction.** 25 obligations at the measured 131 characters each, plus per-principle overhead, is ~5,500 characters of body — which passes — and ~7,000 as a whole file, which fails.
- **`.specify/memory/constitution.md` is never installed as any agent's global-rules file.** Verified: `global_rules` and `.windsurfrules` appear nowhere in code, and the preset layer handles the file as a path plus a SHA-256 sidecar without parsing its content. **6,000 is a portability aspiration, not a live constraint.**
- **Units are stated, not conflated.** S3 reports **characters**; the source cap is **bytes**. On this repo they differ by 39.

Reporting rather than gating is forced, not chosen: the check may not ask, may not trim an accepted rule, and the quota already ran at draft time. A check that can neither fix nor ask can only report — so it reports honestly.

*Settled in [Self-review pass: which quality checks run before writing](https://github.com/DigitalWink/spec-kit-for-human-team/issues/7).*

### 3.12 The single write

**One pass, after the self-review, owned by the preset.** Core's step 6 never executes — it is inside the superseded region. Any byte-level edit flips the document from generated to authored, so the interview must write a complete, coherent document in one pass; `clarify.md`'s habit of saving after every accepted answer must **not** be copied.

**The single-write invariant is scoped to the document body** — the mandatory structure, the accepted rule text verbatim, the pipeline-fixed Governance block and the footer. **The Sync Impact Report is a kept region** and is the one region a lower `wrap` layer may amend after the write, with file paths only. Saying *"one writer of the body"* is honest; saying *"exactly one write"* was not.

**The Sync Impact Report is still emitted.** Core's step 4 is not superseded; only the two lines that surface TODO markers inside it are. Under the write contract the report's deferred-items line has nothing to carry, because the Deferred/Assumed table is terminal output. On an authoring run the report's fields are those of §5.6, with `Version change: (none) → 1.0.0`.

### 3.13 The three closing artifacts

Shown with the finished document, **before** the correction round opens, so the disclosure and the remedy arrive together.

| Artifact | Records | Text |
|---|---|---|
| **The routing table** | content **refused** as out of scope, plus the gate probe's status line | [§4.7.1](#471-the-routing-table) |
| **The Deferred/Assumed table** | content **admitted but unanswered, assumed, or dropped for budget** — every default that left the document short of what the user might have wanted, reversible in the correction round | [§4.7.2](#472-the-deferredassumed-table) |
| **The change log**, with the accounting line as its footer | what the self-review **changed**, and what it **measured** | [§4.7.3](#473-the-change-log-and-the-accounting-line) |

All three are **terminal output only**, never file bytes. The change log is never merged into the Sync Impact Report — an in-file report would inflate the very whole-file number the accounting line measures.

All three print their empty state. Silence would read as "the step did not run", which destroys trust in the whole pass.

### 3.14 The correction round

**One round, zero decisions, after the document exists and after the interview has terminated.** The user can change anything in the assembled document, and can act on any Deferred/Assumed row by typing what its last column says. Text in [§4.6.10](#4610-the-correction-round).

**There is no gating "type yes to ratify" step.** The write is not blocked on a final answer; the typed acceptance an earlier draft priced as a decision is superseded by this round. **But silence is never a yes during the walk** — every area requires a real answer before the interview advances. Consent is given area by area; the closing pass is a chance to correct, not the moment approval is collected.

**Reversal is free.** Acting on a row returns the document to the state the row describes, and the interview applies it rather than silently dropping something else — a second silent default is exactly what the table exists to prevent.

Anything after this round is a fresh `/speckit.constitution` run, which opens in amendment mode.

---

## 4. The wording

Every string the interview prints, in its final form. Derivations, alternatives and the reasoning behind each choice are in [Per-area question wording: what each step actually says](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12), whose resolution spans **two comments**; the fixes applied here on top of it are from [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).

### 4.1 The wording principle

**A confirmation, not an interrogation — one word from a user who agrees, and every legal reply named.**

The target is not a short prompt. It is a **cheap agreeing answer**: inference-first downgrades a question (high cost) to a confirmation (low cost), and a confirmation costs a single word, so the per-question fatigue cost measured in surveys does not transfer at full strength. Six rules fall out, and every string below obeys them.

1. **Every step is answerable in one word by a user who agrees.** `Accept.` `1.` `Yes.` A user who disagrees pays more, which is correct — that is where the information is.
2. **Every prompt line enumerates every legal reply.** A set inventory the user is never told they can `drop 7, 12` from is a blind accept, and that is the most expensive misread in the walk.
3. **The house question-quality rules are adopted; the house recommendation apparatus is refused.** From `clarify.md`: lead with a full interrogative ending in `?`; never a topic label as the question; everyday wording; a reader who does not know Spec Kit must be able to answer from the question line alone. Refused: `**Recommended:** Option [X]` and `**Suggested:**`.
4. **No exit is ever advertised.** *"I don't know"* appears in no prompt line anywhere. It is accepted gracefully everywhere (§4.6.7). It must be a **legible answer, not an offered one** — the design's stated bias is already toward shorter, and an over-signposted exit tips a bias into a default.
5. **The interview states measured facts and quotes rules. It never states an opinion about the project.** Every line describing the repo names its source; every line describing a rule quotes the rule.
6. **A turn that costs no decision prints no `Step` header, and a follow-up that continues the current step opens with the word `Still`.**

**How rule 3 discharges the opposite reflex.** `clarify.md` trains users of this repo to expect a recommendation with every question, and this design offers none. The wording carries the difference by stating the reason affirmatively in the slot where the recommendation would have been, and never apologising for it. The general answer is sharper: the mechanism worth reusing is *downgrade a question to a confirmation*, and that mechanism is **inference**, not a `**Recommended:**` line. A pre-filled step does not recommend a rule — it **shows one measured from the user's own repo, with its source**, which is a stronger reason to say `Accept.` in one word than any model's opinion. On a mature brownfield repo every step pre-fills, so the reflex is over-served on the common path. Where it genuinely is violated — negative scope, and any menu — the wording says why in the same breath.

### 4.2 The three frames

Three fixed frames, not thirty-two bespoke ones. Writing a framing per area is how a settled anatomy drifts.

**Header:** `Step N of M — <area name>`. The area name and nothing else — never the question. `M` is the announced total; `N` counts every charged decision in order, Governance included. Governance reads `Step M of M — Governance`.

The eleven area names, fixed, sentence case after the em dash:

`Negative scope` · `Technology and dependency boundaries` · `Architecture and structural limits` · `Surface contract` · `Irreversible and destructive operations` · `Testing and verification` · `Failure semantics and operating envelope` · `Security and privacy at the data boundary` · `Supported platform and runtime matrix` · `Data lifecycle` · `Release and rollback`

#### Shape A — pre-filled

```
Step N of M — <area name>

Proposed rule
  <rule text, wrapped at ~70 columns>

What this checks, on every future plan
  "<gate question>"

Where it came from
  <source> — <extraction>[, <threshold>]
  <source> — <extraction>[, <threshold>]

<prompt line — §4.3>
```

Four labels, fixed: **`Proposed rule`**, **`What this checks, on every future plan`**, **`Where it came from`**, then the prompt.

- **`Where it came from` names source, extraction and threshold, never only a file.** One line per source, `<path or glob> — <what was extracted>[, <threshold>]`. Worked: `src/**/*.py — 84 command declarations across 12 files; 61 distinct flags, members at 3 or more sites`.
- **No *why it matters* sentence.** The gate question **is** it, and it is strictly better: it states what the rule will do to the user on every future plan rather than asserting a stake. A deliberate divergence from the house idiom — the slot is not dropped, it is filled by something stronger.

Set-bearing steps add **element 4** between `Where it came from` and the prompt: §4.2.1 for the two inventory-bearing steps, §4.2.2 for the scalar step.

#### Shape B — menu

```
Step N of M — <area name>

Nothing to infer
  <what the scan looked for in this area and did not find>

Why you are being asked anyway
  <the repo fact that opened the menu>

Each option below forbids less than the one above it. None was found in your
repo, and none is selected.

  1  strictest
     <rung 1 text, verbatim from data/strictness-ladders.yml>

  2  drops: <rung 2's `drops` string>
     <rung 2 text, verbatim>

  none  This project adopts no <area noun> rule.

What rung 1 checks, on every future plan
  "<rung 1 gate_question>"

Pick a number, edit one, write your own, or pick none.
```

**The two-clause stem is the load-bearing sentence.** Clause 1 states the **nesting rule** as a fact about the shipped text. Clause 2 carries *not found in your repo* — the load-bearing half of the candidate label — and *none is selected*, the ban on pre-selection. The other half of the label, *name the fact*, is already a labelled line of its own, so the stem does not repeat it. **The phrase "typical for this project type" does not ship**: there is no project type in this design.

**One-rung variant.** Clause 1 is false with one rung, so the stem becomes:

```
The option below was not found in your repo, and it is not selected.
```

A one-rung ladder is still a real menu — `none` and *write your own* are always present, which clears the house 2-to-5 floor with three routes.

**Rung labels.** Rung 1 carries `strictest`. Lower rungs carry **no strength label** — `weak` and `relaxed` would editorialise a choice the design requires be unbiased. They carry a **drop delta** instead.

**`none` is rendered co-equal**, on the same indent as the numbered rungs, never as a trailing escape. Its text names the area so it reads as a choice rather than a refusal: `none  This project adopts no testing rule.`

**Rung 1 prints its full gate question. Every lower rung prints a one-line `drops:` delta instead, and that delta ships in the ladder file.** The two horns are about different objects: the gate question is about the rule; the cost of dropping a clause is about the **delta**. A lower rung's gate question is rung 1's with one disjunct removed, so printing it restates rung 1 minus something — exactly the noise a second gate question was feared to be.

**What the delta says is the gate consequence, not the text diff.** The text diff is already on screen, because the rungs are nested and rung 2 is visibly rung 1 minus a sentence. What is *not* on screen is **what now passes**:

```
  2  drops: a plan whose only stated evidence is manual verification
```

**It ships; it is not derived at run time.** Deriving it would be model judgment over prose, so two runs would word it differently. Shipping it also puts it through the authoring-time genericity screen and makes it assertable in the same unit test the data file exists for.

**If the schema amendment is ever refused, the fallback is complete and needs no field:** print rung 1's gate question only, and no delta line. The step still works; the user reads the nesting off the rung texts.

#### Shape C — cold

```
Step N of M — <area name>

<the cold question — one full interrogative ending in ?>

Why it matters
  <one plain sentence: the named fact that makes a rule here worth having>

<prompt line — §4.3>
```

**`Why it matters` and `What this checks` are the same slot, and a step prints exactly one of them.** A pre-filled or menu step has a gate question, which is the stronger filler. A cold step has no rule yet, so the house *why it matters* sentence comes back to fill the slot. That is the whole reconciliation with the house idiom, and it is why no step is ever missing that line.

**The sentence is a named fact, never an exhortation.** Every one of the eleven in §4.4 cites something — a template line, a measured absence, a pipeline fact. On a greenfield run it carries the entire informational load of the interview.

#### 4.2.1 Element 4 on the two inventory-bearing steps

Steps 2 (dependencies) and 4 (surface contract). Labels pinned: `Runtime dependency set (9)`, `Set 1 — command verbs (9)`, `Set 2 — recurring flags (16)`; a numbered inventory in columns; then the band.

```
Set 1 — command verbs (9)
   1 add        4 info      7 search
   2 disable    5 list      8 set-priority
   3 enable     6 remove    9 update
  Below the threshold, not included: build, check(2), init(2), install(2),
  resolve(2), resume, run(2), scaffold, status(2), switch, uninstall,
  upgrade(2), use, validate, version

Set 2 — recurring flags (16)
   1 --author            7 --integration-options   13 --priority
   2 --commands-dir      8 --json                  14 --script
   3 --dev               9 --model                 15 --skills
   4 --force            10 --name                  16 --tag
   5 --from             11 --offline
   6 --integration      12 --output-format
  Below the threshold, 45 more, including: --here(2), --all(2), --dry-run(1),
  --format(1), --debug(1)
```

**`Below the threshold, not included` is fixed and mandatory. Never `rejected`, never `excluded`, never `dropped`** — those members were never candidates. **The near-miss band is the review's substance, and it is free**: the same measurement produces it, and it converts the step from a blind accept into a judgment about one boundary.

**The numbered inventory is a rendering of the declarative sentence, not a separate string.** What is accepted and written is the comma-joined sentence, which is the one unambiguous string body fidelity tests containment against.

**The declared dependency set has no threshold and therefore no near-miss band** — the label line is omitted, not printed empty. Its inventory is still numbered, because `drop 7` must work there too.

#### 4.2.2 Element 4 on the scalar step

Step 3 (architecture). No inventory, so the disclosure does the work, and **both halves are mandatory**:

```
Counted
  pyproject.toml — 1 declared build target ([tool.hatch.build.targets.wheel]).
  Not counted: 7 independently versioned units force-included into that wheel.
```

Without the second sentence `1` is unreviewable and the user cannot tell whether the interview made the mistake this rule exists to prevent — a hand-counted `9` for those units was wrong in two settled tickets at once.

### 4.3 The prompt lines — the closed set

Seven, and no eighth. A prompt line that names a reply the step cannot accept is as much a bug as one that hides a legal reply.

| Step | Prompt line |
|---|---|
| Pre-filled, no measured set | `Accept, edit, or replace?` |
| Pre-filled, **inventory-bearing** — steps 2 and 4 | `Accept, drop by number, add members, or replace the rule?` |
| Pre-filled, **scalar-bearing** — step 3 | `Accept, change the number, or replace the rule?` |
| Menu | `Pick a number, edit one, write your own, or pick none.` |
| Cold | `Answer in your own words, or say there is no rule here.` |
| Governance | `Name the approver, or accept?` |
| Batch summary | `Accept all <n>, or name any you want to drop or change?` |

**The wider prompt is scoped to the two *inventory*-bearing steps, and the scalar step gets its own.** Architecture is set-bearing, but its set is a scalar with no numbered list, so `drop 7, 12` there is an unfollowable instruction. `Accept, change the number, or replace the rule?` is the plain prompt with the edit named; on a one-number step, naming it costs three words and removes the ambiguity between editing the number and editing the prose. This reconciles the measured-sets ticket with itself: its own presentation section already specifies accept-or-edit for a scalar.

**On the cold prompt.** The legal replies are: state a rule, or state that there is none. `say there is no rule here` is an **answer**, not the exit rule 4 forbids advertising — a deliberate none and *"I don't know"* have different consequences, and naming the one and not the other is that distinction made visible.

### 4.4 The eleven areas

Each row supplies the three per-area variables. Everything else is the frame.

- **Cold question** — the full interrogative for shape C. Phrased as *what would that rule out*, never *why*: why-questions drift toward blame and personal preference, while how-questions produce testable constraints.
- **Why it matters** — the cold step's one sentence, always a named fact.
- **Nothing to infer** — the menu step's line: what the scan looked for and did not find.

#### Area 1 — Negative scope (walk position 1, cold on every run)

**Cold question**

> `What is this project deliberately not going to do — something a reasonable engineer might propose, that this project would refuse?`

That is the forbidden-alternative test surfaced as the question — the anti-platitude filter doing its work before the answer exists rather than after.

**Why it matters**

> `A rule that forbids nothing gates nothing, and what a project refuses is the part hardest to read off its code. You answer first; what the scan found comes after.`

Note what it does **not** say: *"the one area a repo scan cannot pre-fill"*. That was falsified on this very repo — the scan finds real, admission-passing negative scope at `AGENTS.md:213` and `README.md:376-382`. *Hardest to read off its code* is the true form, and the second clause is where the missing recommendation is answered affirmatively (§4.1).

**Prompt:** `Answer in your own words, or say there is no rule here.`

#### The ten remaining areas

| # | Area | Cold question | Why it matters | Nothing to infer |
|---|---|---|---|---|
| 2 | **Technology and dependency boundaries** *(walk 2, inventory-bearing)* | `What is this project's runtime stack allowed to contain, and what does adding to it require?` | `Nothing in the plan pipeline reads your manifest — "pyproject" appears zero times under templates/commands/ — so a dependency rule that points at one cannot be checked. The set has to be written down here.` | `No build manifest declaring a runtime dependency group.` |
| 3 | **Architecture and structural limits** *(walk 3, scalar-bearing)* | `How many separately shipped pieces should this project have, and what would justify one more?` | `Every plan fills a Structure Decision; without a stated ceiling there is nothing to measure it against.` | `The build manifest declares no distribution target, and no extension-point registry was found.` |
| 4 | **Surface contract** *(walk 4, inventory-bearing)* | `Which parts of this project's surface — command names, flags, endpoints, output shapes — are a promise you cannot quietly break?` | `Nearly every feature plan adds a name to this surface. This is the rule that decides whether a new name has to fit the ones already there.` | `No command, endpoint or flag declared at three or more sites.` |
| 5 | **Irreversible and destructive operations** *(walk 5)* | `What has to be true before this project deletes, overwrites, or migrates something it cannot get back?` | `These are the changes a plan describes in one line and a rollback cannot undo.` | `No migrations directory, and no destructive file or store operation in source.` |
| 6 | **Testing and verification** *(walk 6)* | `When this project changes how it behaves, what has to prove the change works?` | `Spec Kit's own task template makes tests optional by default — tasks-template.md:12, "Tests are OPTIONAL" — so if this constitution says nothing, nothing asks for them.` | `No test directory, no test runner in any workflow, and no test framework in any dependency group.` |
| c1 | **Failure semantics and operating envelope** | `When something in this project fails at runtime, what does the user see, and how many times does it try again?` | `An unbounded retry is the cheapest thing to write and the hardest to notice once it ships.` | `Failure-capable operations exist, but no retry, fallback or exit-status practice recurs in source.` |
| c2 | **Security and privacy at the data boundary** | `Where does data that identifies a person leave this project, and what has to happen to it first?` | `"Be secure" fails every test in this document. Only a named sink with a named redaction path is checkable.` | `An auth, identity, crypto or payment dependency is declared, but no redaction or masking helper and no person-identifying column were found.` |
| c3 | **Supported platform and runtime matrix** | `Which operating systems and runtime versions is this project promising to keep working on?` | `Target Platform and Language/Version are fields every plan already fills — plan-template.md:29 and :21. Without a stated set there is nothing to compare them to.` | `A breadth signal fired, but no CI matrix could be read for the supported set.` |
| c4 | **Data lifecycle** | `How long does this project keep the data people give it, and how does that data get deleted?` | `Retention written only into data-model.md is invisible: /analyze derives exactly spec.md, plan.md and tasks.md.` | `A data store was found, but no retention statement, deletion path or seeding script in source.` |
| c5 | **Release and rollback** | `When a change to something already running turns out to be wrong, what turns it off?` | `Rolling a deploy back is not the same as turning a change off, and only one of the two is something a plan can name.` | `A deploy target was found, but no feature-flag or gating mechanism recurs in source.` |

### 4.5 The eleven gate questions

Printed by shapes A and B. Six are quoted verbatim from the taxonomy's own `**Checkbox:**` lines. **The taxonomy ships a checkbox for the six always-ask areas and none for any conditional**, so five are minted by applying the gate-restatement test to that area's own exemplar and nothing more — restating the exemplar as a yes/no question about one plan, with no added information. That is the only operation the admission test licenses here, and it is what keeps them reproducible rather than authored.

| Area | Gate question | Source |
|---|---|---|
| Negative scope | `Does this plan's data model, contracts, or Technical Context introduce a way to partition data between customer organizations?` | verbatim |
| Dependencies | `Does this plan add a runtime dependency outside the set named in this constitution, and if so does Complexity Tracking name the rejected alternative?` | verbatim |
| Architecture | `Does this plan's Structure Decision add a distribution artifact beyond the declared ceiling, or add a single-implementation abstraction that is not a registered extension point?` | verbatim |
| Surface contract | `Does this plan add a verb, flag, endpoint or output shape outside the named grammar; remove a published one without naming its replacement; or change user-visible behavior with no documentation task?` | verbatim |
| Irreversible operations | `Does this plan add a migration or a destructive operation, and if so does Technical Context name the reverse path and the idempotency guarantee?` | verbatim |
| Testing | `Does Technical Context name automated evidence for every behavior change in this plan?` | verbatim |
| Failure semantics | `Does this plan add an operation that can fail at runtime, and if so does Technical Context name what the user sees and how the operation bounds its retries?` | **minted** |
| Security and privacy | `Does this plan add a log sink, telemetry export, error reporter, or a path derived from outside input, and if so does the plan text name its redaction path or its containment rule?` | **minted** |
| Platform matrix | `Does this plan state a Target Platform and minimum runtime, and does it stay inside the supported set unless it adds the matching CI matrix cell in the same change?` | **minted** |
| Data lifecycle | `Does this plan add a store of user-supplied data without declaring its retention and deletion in plan prose, or seed a non-production dataset from production data?` | **minted** |
| Release and rollback | `Does this plan change the observable behavior of an already-released endpoint or command, and if so does it name the flag that gates the change?` | **minted** |

A gate question is **never written into the constitution**, where the principle body is the accepted rule text alone.

**The eleven gate questions are also the key `$ARGUMENTS` attaches by** ([§6](#6-arguments)), which is why they are a closed, shipped set rather than free association.

### 4.6 The flow strings

#### 4.6.1 The scan progress lines

Five lines, one per phase. **Phases, never filenames.** Instantiated on this repo:

```
Reading the build manifest and the workflow directory…
Resolving conditional triggers — 5 of 5.
Measuring the dependency set, the artifact ceiling and the surface grammar.
Reading source for negative scope, testing and irreversible operations — 3 files.
Checking the installed speckit.plan for a Constitution Check step.
Scan complete — 8 files, 12 searches.
```

The last line is the announcement's first line, so the transition is a sentence rather than a mode switch. `5 of 5` on line 2 is the only progress figure shown as a fraction, because it is the only one that changes the announced count. `Scan complete` counts the probe's search. **The probe's line prints in all three probe states** — silence is never unambiguous.

#### 4.6.2 The announced count

Fixed word for word, with **two conditional clauses**. Full form, both firing:

```
Scan complete — 8 files, 12 searches.

On this preset stack /speckit.plan has no Constitution Check step. Plan-time
gating is not available: your principles will inform planning but will not
block it.

This interview closes 9 decisions: 6 standard areas, 2 your repo triggered
(failure semantics, platform matrix), and 1 Governance confirmation. Follow-up
turns that clarify an answer you already gave never add a decision, and neither
do the extension hook turns this project's .specify/extensions.yml adds around
the command.

Step 1 of 9 — Negative scope
```

| Clause | Prints when |
|---|---|
| `On this preset stack /speckit.plan has no Constitution Check step…` | the gate probe returns **gate absent** |
| `, and neither do the extension hook turns this project's .specify/extensions.yml adds around the command` | `.specify/extensions.yml` declares a `before_constitution` or `after_constitution` hook |
| `, 2 your repo triggered (failure semantics, platform matrix),` | at least one conditional fired. With none: `…closes 7 decisions: 6 standard areas and 1 Governance confirmation.` |

**Three placements, all load-bearing.** The gate clause fires **before** the count, so the user learns it before spending 7 to 10 decisions rather than after. The count sits between the scan's last progress line and the first question, with nothing between it and real content. The hook clause is an **exclusion**, so it joins the exclusion sentence; the gate clause is a **stack fact** that excludes nothing, so it gets its own paragraph.

**Extension hooks, and the one thing that cannot be done.** A `before_constitution` hook runs strictly before the announcement, so it cannot interleave a question into a counted walk. An `after_constitution` hook runs after the interview has finished and may ask the user anything; it is outside the counted walk by construction, the count has already been honoured, and the wrapper cannot constrain another package's command. Naming it in the exclusion line is the whole of the available remedy.

#### 4.6.3 The negative-scope follow-up

Zero decisions, mandatory, prints even when empty. Runs **after** the user answers, so the anti-anchoring property is preserved and the user's own thinking lands first.

```
Still on negative scope — the scan found these too. They cost you nothing to reject.

  1  A plan MUST NOT add a code path in the Specify CLI that creates, updates,
     removes, resolves, or migrates an agent context or instruction file.
     Where it came from
       "The Specify CLI carries no agent-context state whatsoever; the CLI never
        creates, updates, removes, resolves, or migrates a context/instruction
        file." — AGENTS.md:213
  2  A plan MUST NOT introduce a file write outside the project root, and MUST
     NOT make a consume or author command depend on network access.
     Where it came from
       "Installs are idempotent and confined to the project root; all
        consume/author commands work offline." — README.md:376-382

Each numbered rule is what gets written if you attach it. Attach any of these to
your answer, or reject them all.
```

**A rejectable candidate is a *drafted rule*, with the repo quote beneath it as provenance.** The alternative reading — attaching the verbatim quote — writes a descriptive sentence carrying **zero obligations** into the one area the taxonomy built to gate the most, and that sentence **cannot pass the admission test**: it does not restate as a plan checkbox, forbids nothing, and names no artifact. A document of admitted rules is admissible by construction, and the quote reading puts unadmitted content past that floor.

Three consequences:

1. **`Each numbered rule is what gets written if you attach it.`** is what makes the accept informed. Without it the only cheap action is a blind accept.
2. **A quote that yields no admissible rule is not offered.** The candidate is drafted first and judged by the admission test before it reaches the screen, exactly as any drafted rule is.
3. **The drafted attachment is quota-charged; the user's own typed answer is not.** Negative scope's quota of 3 obligations is spent on the drafted attachments only.

**Empty form, one line** — it is mandatory, and mandatory means it prints when there is nothing, because silence reads as *"the step did not run"*:

```
Still on negative scope — the scan found nothing to add to your answer.
```

**On "I don't know" here.** The follow-up still runs, and the area produces no principle **only if the user also rejects the candidates**. So the acknowledgement (§4.6.7) is deferred until the follow-up closes — the only step in the walk where it is.

#### 4.6.4 The push-back, and the route-out verdict

At most one push-back per area. Three elements plus a binary prompt, and it states its own price:

```
Still on this step — no gate can check that as written.

  What you typed  "A plan MUST follow our internal style guide."
  Why             It names no artifact that answers the checkbox. A reviewer
                  reading the whole codebase is not a legal answer.
  A checkable version
                  "A plan MUST NOT introduce a module whose name begins with an
                   underscore outside src/<pkg>/_internal/."

Take the rewrite, or keep yours? Keeping yours costs no decision, and the
routing table will record that no command can observe it.
```

- **The prompt is binary and terminal**, so nothing invites a second round. One push-back per area is a hard cap and the wording must not read like the opening of an argument.
- **`Why` names which test failed, in one sentence, in the user's terms** — never the label `T3`.
- **On insist**, the acknowledgement is flat, with no residue of the disagreement: `Kept exactly as you typed it. The routing table will say that no command in the pipeline can observe it.`

**A route-out verdict is not a push-back and must not read like one**, because it is not overridable. Different string, no offer, and it re-prints the step's prompt because the area is still open:

```
Still on this step — that one is already enforced outside this document, so it
does not go in: enable ruff rules UP, FA, I and N in pyproject.toml. It is on
the routing table.

Accept, edit, or replace?
```

Naming the exact rule to enable is the constructive-route-out requirement, carried inline rather than as a footnote.

#### 4.6.5 No going back mid-walk

No `Step` header, costs nothing, names where the request lands:

```
Noted — step 2 is closed for now. Bring it up in the correction round at the
end, where you will see it in the finished document.
```

`where you will see it in the finished document` is the reasoning made visible: the closing pass is the cheaper place to fix an earlier answer, because the user sees it rather than remembers it.

#### 4.6.6 Disambiguation

Not a decision, does not advance:

```
Still the same decision — <the narrow question>?
```

#### 4.6.7 The "I don't know" acknowledgement

Never advertised, always acknowledged. This is the string that carries the no-placeholder rule to the user.

| Shape | Acknowledgement |
|---|---|
| **Pre-filled** | `Taken as accepted, and logged as assumed. It is row DA<n> below, with the sentence that reverses it.` |
| **Menu** | `Taken as none — this area produces no principle. It is row DA<n> below, with the sentence that adds one later.` |
| **Cold** | `Noted. This area produces no principle, and nothing is written in its place. It is row DA<n> below, with the sentence that adds one later.` |
| **Cold, negative scope** | *deferred until the follow-up closes* — §4.6.3 |
| **Amendment, touched area** | `Taken as no change — principle <numeral> is carried forward exactly as it stands.` |

**`nothing is written in its place` is the phrase, and it is not decoration.** `/plan` loads the constitution unconditionally and must fill a blocking gate from it; `/analyze` rates a constitution conflict CRITICAL; `/converge`'s carve-out is document-level and binary. The user needs to know a blank is a blank, not a `TODO`.

**"I don't know" never writes a placeholder, a bracket token, or a `TODO(...)` marker.** It closes the area and costs its one decision. On a pre-filled step it accepts the inferred rule, logged as assumed — so the least engaged user gets the most logged assumptions, which is the prescribed behaviour. On a mature repo that also means the least engaged user gets the **longest** constitution, not the shortest, because most steps pre-fill.

**On a pre-filled step it feeds the satisficing-collapse run, and the acknowledgement does not say so** — the collapse announces itself when it fires, and narrating the counter would turn a mechanism into a scoreboard.

#### 4.6.8 The Governance confirmation

```
Step 9 of 9 — Governance

Governance is filled, not asked. It is not a principle, and no plan gates on it.

  Version        1.0.0
  Ratified       2026-08-26
  Last amended   2026-08-26
  Amendments     approved by the project maintainers
  Carried here   3 lines the walk routed to Governance rather than to a principle

One field here is not something the repo can tell me: who approves an amendment
to this constitution.

Name the approver, or accept?
```

- **`approved by the project maintainers` is a real pre-fill, not a token.** The field is genuinely not inferable, a placeholder is forbidden, and `[GOVERNANCE_RULES]` may not survive — so the step must carry a default the user overrides.
- **`Carried here` prints only when n > 0.** It is the reason Governance sits last: route-out lines accumulate as `## Governance` lines and the block cannot be pre-filled until the last route-out exists.
- **No ratification-date question.** Core asks one; the wrapper supersedes it and substitutes *default to today; never ask, never mark*. The wording does not hint that the date is negotiable.

#### 4.6.9 The batch summary

```
Steps 7 to 9 of 10 — Failure semantics, Data lifecycle, Release and rollback

You accepted the last three proposed rules unchanged, so the rest of the walk is
one block instead of three steps. It still costs three decisions — what this
saves is round-trips, not decisions.

  Failure semantics and operating envelope
    Rule        <full rule text>
    Where from  <source> — <extraction>
  Data lifecycle
    Rule        <full rule text>
    Where from  <source> — <extraction>
  Release and rollback
    Rule        <full rule text>
    Where from  <source> — <extraction>

All three go in the Deferred/Assumed table either way, with the sentence that
reverses each one.

Accept all three, or name any you want to drop or change?
```

- **`It still costs three decisions — what this saves is round-trips, not decisions.`** is mandatory in substance. Without it the block reads as the tool taking content away, and with it the announced number stays honest at the one moment it visibly could have moved.
- **Header form: `Steps N to N+k of M`.** The range is what keeps `M` arithmetically true across a collapsed tail.
- The prompt **blocks**, and is a real question with two branches.
- **It does not offer to un-collapse.** The affordance that would buy — reviewing them separately — is already there in `name any you want to drop or change`.

#### 4.6.10 The correction round

One round, zero decisions. **It must not read as asking for ratification**: no *type yes*, no *approve*, no *ratify*, anywhere in this string.

```
That is your constitution, written to .specify/memory/constitution.md.

One correction round, and it costs no decision. Change anything above — a rule, a
principle name, the Governance block — or act on any row below by typing what its
last column says, like "add a rule for negative scope: <your rule>". Say "done",
or say nothing more, and this run is finished. After that, changing the document
is a fresh /speckit.constitution run, which opens in amendment mode.
```

- **The example quotes, verbatim, column 6 of the first row the Deferred/Assumed table actually printed.** It is a rule, not fixed text. A fixed example naming an `Obligation dropped` row's reversal sentence would, at the floor, tell the user to type a sentence no row accepts — in the one run where the table is the entire remedy.
- **`act on`, not `reverse`.** `reverse` is the verb for an `Obligation dropped` row and for nothing else; at the floor every row is `Area unanswered`, whose column 6 **adds** rather than reverses. `act on` is true of all fifteen kinds.
- **When the table is empty the whole clause drops**, leaving `Change anything above — a rule, a principle name, the Governance block.`
- **`or say nothing more`** is deliberate and does not contradict *"silence is never a yes"*, which is scoped to **during the walk**. Saying so out loud is what stops the user waiting for a gate that no longer exists.

**The closing line prints either way; `Applied.` is the conditional half:**

- corrections applied — `Applied. Version 1.0.0, ratified 2026-08-26. Anything further is a fresh /speckit.constitution run, which will open in amendment mode.`
- nothing changed — `Version 1.0.0, ratified 2026-08-26. Anything further is a fresh /speckit.constitution run, which will open in amendment mode.`

#### 4.6.11 The authoring-mode line

Fires when the amendment predicate fails and a file at `.specify/memory/constitution.md` is about to be overwritten. Prints where the announced count would go, then the count. It must name **which** condition failed.

**Two variants, keyed on the failing condition, both free** — every fact comes from the read the predicate already did.

*Tokens present — the ordinary case, and it is every project `specify init` ever touched:*

```
There is a file at .specify/memory/constitution.md and it is still an unfilled
template: 20 placeholder tokens are in it, starting with [PROJECT_NAME] on line 1.
This run replaces it, which is what it is for.
```

*Tokens absent but an anchor missing or the footer unparseable — somebody wrote this by hand:*

```
There is a file at .specify/memory/constitution.md that this command cannot
amend: it has no "## Governance" heading, so there is no ratified structure to
amend against.

This run will write a new constitution over it. If that file holds work you want
to keep, stop now, copy it somewhere else, and run this again.
```

**The split is keyed on the failing condition, never on the provenance sidecar** — the sidecar answers the wrong question, and tokens-present already separates *"a template nobody filled"* from *"a document somebody wrote"*, which is the split that decides how loud the warning should be.

The failing-condition strings, one per condition:

| Condition | String |
|---|---|
| `[ALL_CAPS]` tokens survive | `it is still an unfilled template: <n> placeholder tokens are in it, starting with <token> on line <n>` |
| `TODO(` survives | `it carries a TODO( marker on line <n>` |
| no H1 | `it has no "# <name> Constitution" heading on the first line` |
| no `## Core Principles` | `it has no "## Core Principles" heading` |
| no `## Governance` | `it has no "## Governance" heading` |
| footer absent or unparseable | `its "**Version** \| **Ratified** \| **Last Amended**" footer is missing or does not parse` |

`stop now` rather than `press Ctrl-C`: the interrupt key is agent-dependent, and naming the wrong one is worse than naming none.

### 4.7 The three closing artifacts

#### 4.7.1 The routing table

Three blocks under one header:

```
Routing table

  Routed out — 12 candidates that belong somewhere else
    Import ordering and modern type syntax
      → ruff, by enabling rules UP, FA, I and N in pyproject.toml
    "Every PR must verify compliance"
      → nothing; a review is not a plan section, a source file, or a named person
    Direct pushes to main are forbidden
      → branch protection, plus one line in ## Governance
    ...

  Weak anchors — 1 principle is answered only by the agent that writes the plan
    IV. Surface contract → ## Complexity Tracking

  Plan-time gate: present. 5 of 7 principles are plan-visible and are gated by
  /speckit.plan's Constitution Check.
```

- **`→ <destination>, by <the exact rule, file or setting>`.** A route-out must be **constructive** — name the ruff rules to switch on, do not just refuse. A destination with no lever is the *"the tool dropped my rule"* failure one step removed.
- **`Weak anchors` prints only when n > 0.** A rule anchored on `data-model.md`, `contracts/` or `## Complexity Tracking` is checked by the same agent that wrote the answer, so it is audit-free, and the header sentence is that saying-so in words a user can act on.
- **Empty state:** `Routing table — nothing was routed out. Every candidate this run produced is in the document.`

**The gate status line prints in all three states**, so silence is never ambiguous:

- *gate present* — `Plan-time gate: present. N of M principles are plan-visible and are gated by /speckit.plan's Constitution Check.`
- *gate absent* — `Plan-time gate: absent on this preset stack. N of M principles are plan-visible; on this stack they inform planning but do not block it.`
- *not determined* — `Plan-time gate: not determined — the installed speckit.plan command could not be read. Check with 'specify preset resolve speckit.plan'.`

**Two zero-principle variants**, fired when the document names **zero principles**, because `0 of 0` is true and awkward:

- *gate present* — `Plan-time gate: present. This document names no principles, so /speckit.plan's Constitution Check has nothing to gate.`
- *gate absent* — `Plan-time gate: absent on this preset stack. This document names no principles, so nothing turns on it either way.`
- *not determined* — **unchanged**; that line carries no `N of M`, so it needs no variant.

#### 4.7.2 The Deferred/Assumed table

**Columns: `ID | Kind | Where | Default applied | Why | To change it, say`.**

| # | Column | Contents |
|---|---|---|
| 1 | **ID** | `DA<n>`, assigned in print order. A **flat** namespace, not a per-category initial: this column is an *input channel*, and a category prefix creates a mis-aim failure a single prefix cannot have. |
| 2 | **Kind** | One label from the closed set below. |
| 3 | **Where** | The document location if the row produced one (`III. Surface contract`); the interview location otherwise (`Step 3 — Architecture`); `Not asked — <area>` for an area that never ran. **Never empty.** |
| 4 | **Default applied** | What the run did, as the document's state now. One sentence — **except** where content was removed, which is **quoted verbatim**. A row the user cannot read is not reversible. |
| 5 | **Why** | The named fact: repo evidence, trigger evidence, the scan-budget stop, quota arithmetic, a measured count. **A fact, never a judgment.** |
| 6 | **To change it, say** | The exact sentence to type in the correction round. |

**Header:** `Deferred / assumed — <n> rows. Each says what was assumed and what to say to change it.`

It does **not** say *all reversible in the correction round*: two of the fifteen kinds need a fresh run, so a blanket promise would be false. The per-row column 6 carries the truth.

**Fifteen kinds**, in print order.

| Kind | Default applied | Why | To change it, say |
|---|---|---|---|
| `Trigger unresolved` | `The area was not asked and produces no principle.` | `Its trigger could not be resolved inside the scan budget of 40 reads and 13 searches.` | `"ask me about <area>"` |
| `Conditional not asked` | `The area was not asked and produces no principle.` | `<n> conditionals triggered; at most 3 run in one interview, and this one ranked <k>.` | `"ask me about <area>"` |
| `Area unanswered` | *rendered in three columns — see below* | | `"add a rule for <area>: <your rule>"` |
| `Rule assumed` | `Written as proposed: "<rule, clipped to 72 chars>"` | `You accepted it unchanged after saying you did not know.` | `"DA<n>: drop it" — or type a replacement rule` |
| `Batch assumed` | `Written from the batch summary: "<rule, clipped>"` | `Three rules in a row were accepted unchanged, so the remaining conditionals ran as one block.` | `"DA<n>: drop it" — or type a replacement rule` |
| `Obligation dropped` | `This clause was removed: "<clause, verbatim>"` | `The document reached 25 obligations; <area> carried the most.` | `"DA<n>: put it back"` |
| `Set unmeasured` | `The rule was written without the sentence that names the <set>: "<residue, clipped>"` | `The <set> could not be produced from <source> inside the scan budget.` | `"DA<n>: the set is <members>"` |
| `Threshold raised` | `The <set> was measured at a recurrence threshold of <k>, not 3: <members>` | `At threshold 3 the set held <n> members, above the 20-member ceiling.` | `"DA<n>: use threshold 3"` |
| `No evidence found` | `Written as accepted. The scan found no code doing this.` | `<what was searched, and not found>` | `"DA<n>: drop it"` |
| `Left verbatim` | `Written exactly as you typed it. The self-review changed nothing in it.` | `A clause here is text you typed, and fidelity to your text is verbatim.` | `"DA<n>: <your rewrite>"` |
| `Overlap` | `Both were written. Neither was merged.` | `They share <the shared subject, tool, or restated obligation>.` | `"DA<n>: drop <numeral>"` |
| `Over principle count` | `Nothing was changed.` | `The document carries <n> principles; 9 or more is above this interview's designed maximum.` | `"DA<n>: drop <numeral>"` |
| `Argument unplaced` | `Nothing was written from it: "<clause, verbatim>"` | `It answered the gate question of no area this interview asked.` | `"add a rule for <area>: <your rule>"` |
| `Beyond the ceiling` *(amendment)* | `The area was not amended. Its principle is carried forward unchanged.` | `You named <n> areas; at most 3 are amended in one run, in walk order.` | `run /speckit.constitution again and name <area> first` |
| `Carried forward` *(amendment)* | `<n> principles carried forward unchanged and not reviewed: <numerals>.` | `You did not name their areas, so this run did not open them.` | `run /speckit.constitution again and name the area` |

**Four rules the row text obeys:**

1. **Column 6 is a sentence the user can type, not a description of an action**, quoted where it is literal input. That is the difference between a remedy and bookkeeping, and at the floor it is the only lever the run produces.
2. **Column 4 quotes verbatim wherever content was removed.**
3. **Column 5 is a fact, never a judgment** — always a measured number, a named source, or a stated cap.
4. **`Where` is never empty.**

**`Area unanswered` renders in three columns**, under one header sentence carrying columns 4 and 5 once. The rows are **not** collapsed — column 6 is per-area and is the artifact's entire justification at the floor. What is collapsed is the two columns that were byte-identical on every row.

```
Area unanswered — each of these areas produces no principle, and nothing was
written in its place. You said you did not know, on a step that offered no
default.

  ID   Where                                    To change it, say
  DA1  Step 1 — Negative scope                  "add a rule for negative scope: <your rule>"
  DA2  Step 2 — Technology and dependency       "add a rule for technology and dependency
       boundaries                                boundaries: <your rule>"
  DA3  Step 3 — Architecture and structural     "add a rule for architecture and structural
       limits                                    limits: <your rule>"
  DA4  Step 4 — Surface contract                "add a rule for surface contract: <your rule>"
  DA5  Step 5 — Irreversible and destructive    "add a rule for irreversible and destructive
       operations                                operations: <your rule>"
  DA6  Step 6 — Testing and verification        "add a rule for testing and verification:
                                                 <your rule>"
```

`on a step that offered no default` is exact for **both** shapes this kind can come from: a cold step offers no candidates, and a menu step offers candidates with nothing pre-selected — so neither offers a default, and that is precisely what separates `Area unanswered` from `Rule assumed`, which is what a pre-filled step's *"I don't know"* produces instead. It is a fact, not a judgment, so the column-5 rule holds.

**`Where` for this kind is `Step N — <area>`, never `Not asked — <area>`.** An `Area unanswered` row is by definition an area that **did** run. `Not asked — <area>` stays correct for the two kinds it was written for: `Trigger unresolved` and `Conditional not asked`.

**The block is contiguous by construction** — IDs are assigned in print order and the kinds print in a fixed order, so all `Area unanswered` rows already print together.

**Two mandated one-line collapses**, each replacing N rows:

- `No principle in this document has supporting repo evidence — the scan found no code.`
- `4 principles carried forward unchanged and not reviewed: I, III, IV, V.`

**Empty state:** `Deferred / assumed — nothing. Every area was answered and every default was yours.`

**What produces no row**, so the firing conditions are unambiguous: a deliberate *"pick none"* (the user chose; no default was applied); a **per-area** quota trim (enforced at draft time before the user saw the rule); a rejected set-diff candidate; an unfired trigger.

#### 4.7.3 The change log and the accounting line

Header, when the pass acted: `Self-review — <n> changes.` then the table `ID | Check | Where | Change`, capped at 12 rows. IDs `C1`…`Cn` in document order — **not** `S`, which would collide with the check names S1–S5. Quote the **after** form only, clipped to 72 characters. No `Severity` column (a per-run model judgment) and no `Recommendation` column (every row is already applied).

**The no-op line prints, always**, and given what the pass is, it is the expected line on a good run:

```
Self-review — no changes. The document is exactly the rules you accepted.
```

**The accounting line is the footer**, and it prints on every run:

```
Measured — 7 principles, 19 obligations, 4,730 characters of body,
6,241 characters whole file (Sync Impact Report: 1,511).
```

**The zero-obligation clause** follows the numbers when the written document carries **zero obligations**, counted exactly as the line already counts them:

```
Measured — 0 principles, 0 obligations, 0 characters of body,
1,180 characters whole file (Sync Impact Report: 664).

No MUST, MUST NOT or SHOULD reached the document, so there is nothing for a gate
to check: /speckit.plan, /speckit.analyze and /speckit.converge will each load
this file, extract an empty rule set, and report zero. The file is written and
complete — it carries all four required sections and its adoption date, so the
next run will open as an amendment. Every area that produced no principle is
listed in the Deferred/Assumed table below, with what to say to add one.
```

Three properties of that clause, each deliberate:

- **The third sentence drops when the document has principles but no obligations** — there are no such areas to point at. Sentences 1 and 2 are true in both shapes and do not change.
- **It reads as a measurement, not a verdict.** It opens with what was measured rather than with a characterisation; it states the outcome **positively** (`The file is written and complete`) rather than denying a failure, because *"this is not an error"* plants the idea it denies; and it ends by pointing at a remedy the user can type.
- **It never says *empty*.** *Empty constitution* is reserved for the materialized scaffold, which is the one document shape this emphatically is not — the scaffold carries twenty `[ALL_CAPS]` tokens and routes to authoring; this carries none and routes to amendment.

---

## 5. Amendment mode

*Settled in [Amendment mode: the short "what changed?" interview](https://github.com/DigitalWink/spec-kit-for-human-team/issues/6); worded in [Per-area question wording](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12); its scan re-derived and its touched-area disclosure and set-diff states added by [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).*

**The same walk, over a set the user names instead of a set the taxonomy fixes.** Entered on the anchors predicate of §3.1.

### 5.1 The walk and the budget

| Step | What | Shape |
|---|---|---|
| **1** | **What changed?** | **cold**, with a mandatory follow-up showing the set diff as rejectable candidates |
| **2…k** | one **touched area**, at most **3**, in the walk order of §3.4 restricted to the touched set | pre-filled from the **existing principle**, or cold if the area has none |
| **last** | **bump confirmation** | pre-filled |

**A touched area is one the user named in step 1, or a set-diff candidate they accepted in its follow-up.**

**No conditional fires in amendment mode.** A conditional is a repo signal that decides whether to open an area the user did not ask for; here the user names the areas.

**The 3-area ceiling.** If step 1 names more than three areas, take the first three in walk order and log the rest as `Beyond the ceiling` rows. The selection key is a **fixed order, never a model judgment**. **Tie-break: areas the user named are taken before set-diff candidates they accepted.** Three is derived twice over — it is the per-run firing cap the taxonomy already set for *how many extra areas may open in one run*, and it keeps the amendment maximum (5) strictly below the authoring floor (7).

**Cost: 1, or 3 to 5 decisions.**

- **1** — step 1 answered "nothing changed". The run ends with **no write at all**: no `**Last Amended**` move, no version bump, no Sync Impact Report change.
- **3 to 5** — step 1, plus 1 to 3 touched areas, plus the bump confirmation.
- **2 is unreachable.** Any run that touches an area pays the bump confirmation, so the count jumps 1 → 3. Stated so the range is not read as an off-by-one.

**Scan: 2 reads and 4 searches**, one pass, hard stop — about 5% of the authoring budget.

| Spend | Reads | Searches |
|---|---|---|
| `.specify/memory/constitution.md` — the predicate, and every touched area's pre-fill | 1 | 0 |
| the three measured sets, at the allocation of §3.7 | 1 | 3 |
| the gate probe | 0 | 1 |
| **Total** | **2** | **4** |

There is nothing else to spend on: no triggers resolve, and every touched area's pre-fill comes from the first read.

**The satisficing collapse cannot fire and is not inherited.** It needs 3 consecutive accept-without-edit answers on area steps, and an amendment walks at most 3 area steps with a cold step always at position 1; and its floor needs at least two fired conditionals still unasked, and no conditional fires. **The ceiling of 5 is the escape hatch** — an interview that cannot exceed five decisions does not need a mechanism for shortening itself.

**What carries over unchanged.** *"I don't know"* never writes a placeholder; on a touched area it means *carry the existing principle forward unchanged*, logged, and on a touched area with no existing principle the area produces no principle. No check ever asks. The correction round is one round and is not charged. All three closing artifacts print, including the gate probe's status line.

**The re-run path is the same interview.** A user who lands here minutes after authoring gets the same flow as one who returns in six months. Distinguishing them needs net-new state, and the predicate is deliberately a function of the document alone. The immediately-after case comes out honest with no special casing: `**Ratified**` stays at the authoring run's date, `**Last Amended**` moves to today, and `1.0.0` becomes `1.0.1` or `1.1.0`.

**The escape back to authoring is outside the flow**: remove or rename `.specify/memory/constitution.md` and re-run. No in-flow override and no flag — mode is a property of the document, and an in-flow override would spend a decision on every amendment run to buy an outcome that is one `rm` away. This is also the **migration path** of §1.2.

**There is no separate yes/no confirmation.** The mode switch is announced, and consent lives inside step 1, where *"nothing changed"* is a valid answer that ends the run with no write at a cost of one charged decision. A blocking yes/no would be one answer closing one thing — a decision — and it would buy an outcome step 1 already buys.

### 5.2 The two announcements

**A ceiling before step 1, then the exact count once step 1 closes.** An amendment scan provably cannot complete before the first question the way authoring's can: the count depends on the user's answer to step 1, and no scan can pre-empt it. The design changes **what is announced** rather than faking a number. Both blocks name the same exclusions.

```
Found a ratified constitution — Spec Kit Constitution, v1.0.0, ratified
2026-06-19, last amended 2026-06-19, 5 principles.
Plan-time gate: present.

Amendment mode. Nothing already in this document is rewritten unless you name
it. To write a new one from scratch instead, remove
.specify/memory/constitution.md and run this again.

At most 5 decisions: this one, up to 3 areas you name, and one version-bump
confirmation. Follow-up turns that clarify an answer you already gave never add
a decision. If nothing has changed, say so and this run ends here without
writing anything.

Step 1 — What changed?
```

then, once step 1 closes:

```
This amendment closes 3 decisions: what changed, 1 area (technology and
dependency boundaries), and the version bump. Same exclusions as above.

Step 2 of 3 — Technology and dependency boundaries
```

Three things the wording does, each with a reason:

- **The consent clause is an imperative in its own sentence.** It is the mode's entire consent mechanism, so it cannot be a footnote — a quoted phrase describing a behaviour is a footnote; an instruction telling the user what to say is not.
- **`To write a new one from scratch instead`**, in plain wording. The escape is the only route out, so it has to be legible on first read.
- **The ceiling block drops `This amendment closes`**, which the exact block owns. `At most 5 decisions` versus `This amendment closes 3 decisions` is the difference the two-announcement shape exists to communicate; using the same verb for both hides it. Measured on the prototype, this is what makes the shape read as *a bound, then a count* rather than as a promise revised mid-run.

**Step 1 prints no denominator** — `Step 1 — What changed?`. Every later step prints `Step N of M`. A denominator on step 1 would be a guess.

### 5.3 Step 1 — the cold "What changed?"

```
Step 1 — What changed?

Name the areas where this project's rules no longer match what it does. Naming
more than three defers the rest to a later run. If nothing has changed, say so
and this run ends here without writing anything.

  What this constitution covers today
    I    Code Quality & Architectural Discipline
    II   Test-Backed Change (NON-NEGOTIABLE)
    III  CLI & User-Experience Consistency
    IV   Offline-First Performance & Resource Discipline
    V    Minimal Dependencies & Safe, Idempotent File Operations

Name the areas, or say nothing changed.
```

All three requirements are answerable in one reply, and each has a mechanism:

1. **Which areas changed** — the question, plus the document's own table of contents so the user **recognises rather than recalls**. This is not a candidate list and does not anchor: it is the document being amended, read off the `###` headings inside the single predicate read. Step 1 is cold to keep the **scan's** findings from anchoring the answer, which is why the set diff is deferred; showing the user their own document is the opposite operation.
2. **Naming more than three defers the rest** — sentence 2, stated *before* the answer rather than discovered after it. The ceiling cut is defensible only because amendment is re-runnable, and a cut the user did not know about is not.
3. **"Nothing changed" ends the run without writing** — sentence 3, the same imperative as the announcement, and the prompt line names it a third time.

**Nothing in this step touches ratification**, and nothing implies it might.

### 5.4 Step 1's mandatory follow-up — the set diff

**All three measured sets are re-measured before step 1, unconditionally**, by the same declared measurement. Unconditionally, because provenance stays out of the document, so there is no recorded basis to compare against and measuring again is the only way to see drift.

**The diff is presented after the user answers, never before** — the negative-scope pattern applied unchanged, so the scan's findings cannot anchor the answer. **It costs zero decisions.**

```
Still on what changed — all three measured sets were measured again.

  Runtime dependency set
    Named here    could not be read from this document
    Measured now  click, json5, packaging, pathspec, platformdirs, pyyaml,
                  readchar, rich, typer
  Command-verb grammar
    Named here    could not be read from this document
    Measured now  add, disable, enable, info, list, remove, search,
                  set-priority, update
  Distribution-artifact ceiling
    Named here    not stated
    Measured now  1

None of this is a change you asked for, and rejecting all of it costs nothing.

Attach any of these to your answer, or reject them all.
```

**The set diff is well-defined only over sentences the interview itself wrote.** On any document it did not write — which is **every document on its first amendment**, the most common amendment run there is — the left column would otherwise be a guess, and a guess inside a declared measurement is exactly what the reproducibility rule forbids.

**The recognition rule is a substring test and never a judgment.** The document is already in memory from the predicate read, so all three tests are free:

| Set | State 1 — parse | State 2 key term |
|---|---|---|
| Runtime dependency set | a sentence beginning `The runtime dependency set is:`, members comma-separated up to the first `.` | `dependency` / `dependencies` |
| Command-verb grammar | `The measured command-verb grammar is:` | `verb` |
| Recurring flag grammar | `The measured recurring flag grammar is:` | `flag` |
| Distribution-artifact ceiling | the numeral immediately following `distribution artifacts declared in the build manifest above` | `distribution artifact` |

The ceiling needs its own row because it is the one measured set **not** written as a declarative sentence — its number lives inside the architecture obligation. Without this row the third state would fire on every interview-written document.

**Three states:**

1. **The stem is present** → parse the members and print the two-sided diff (`+ httpx  − readchar`, or `1 — unchanged`).
2. **The stem is absent and the key term occurs inside `## Core Principles`** → `Named here    could not be read from this document`, and the right column prints the **whole measured set**. One-sided.
3. **The stem is absent and the key term does not occur** → `Named here    not stated`, right column the whole measured set. One-sided.

Four properties:

- **The header sentence drops its move count when any set is one-sided** — `and two moved` is a claim about a diff, and there is no diff in states 2 and 3. With every set two-sided it reads `…measured again, and two moved.`
- **State 2 is not a failure and does not read as one.** It names what happened and stops; the measured set is still offered, so the user loses only the delta.
- **An unchanged set is still printed** (`1 — unchanged`). Showing only the moves would leave the user unable to tell *measured and unchanged* from *not measured*.
- **Empty form, one line:** `Still on what changed — all three measured sets were measured again, and none of them moved.`

**`None of this is a change you asked for, and rejecting all of it costs nothing.`** is the sentence that makes the block read as drift detection rather than as the tool changing the subject: it names the price and disclaims the intent in one sentence. Measured on the prototype, it works.

**Accepting a candidate makes it a touched area**, competing for the 3-area ceiling behind user-named areas. **Rejecting produces no artifact** — what the user leaves out of a set they leave out knowingly.

**Nothing is re-measured after accept.** The set is materialised into the sentence at accept time. A carried-forward measured set is a carried-forward principle and is not touched at all.

### 5.5 A touched area is replaced, not edited

The step is shape A, pre-filled from the existing principle — and the existing principle is re-drafted through the **full** authoring machinery: the route-out table, the admission test, the obligation quota, and the short-name default. The behaviour is correct and required; what was missing is the **disclosure**, because the pre-filled frame's whole grammar says *here is a rule I inferred, accept it*, and the honest message is *here is your principle, rebuilt to this document's rules, with clauses removed*.

Two lines, between `Where it came from` and the prompt, each printing only when its condition holds.

**Line 1 — replacement disclosure**, when the drafted rule does not carry every clause of the existing principle:

```
This replaces principle <numeral> rather than editing it. <n> of its clauses are
not carried forward: <r> are on the routing table below<, and <q> did not fit
this area's quota of <k> obligations>.
```

The `<, and q …>` half prints only when `q > 0`, so neither destination is ever named with a zero. **The split is not cosmetic**: a clause the route-out table refused is **not overridable** and will be named on the routing table; a clause the quota trimmed produces **no Deferred/Assumed row and no routing-table line** (§3.8). Saying only *"they are on the routing table"* would be false whenever `q > 0`.

**Line 2 — rename notice**, when the short-name default differs from the existing heading:

```
Its heading becomes "<numeral>. <short name>", replacing "<existing heading text>".
```

Instantiated:

```
This replaces principle II rather than editing it. 5 of its clauses are not
carried forward: 5 are on the routing table below.
Its heading becomes "II. Testing and verification", replacing "II. Test-Backed
Change (NON-NEGOTIABLE)".
```

Stripping an emphatic `(NON-NEGOTIABLE)` label is the settled behaviour: the short name defaults to the area's own name precisely so a model-invented title cannot smuggle one in.

**The amendment's promise — *nothing already in this document is rewritten unless you name it* — is a promise about every *other* area**, and it stays true.

### 5.6 The bump confirmation

**Classified mechanically from the obligation diff; the user confirms.** Charged as one decision, always last.

Compare the document's obligation set before and after, normalized for whitespace and case:

| Signal | Tier |
|---|---|
| An obligation present before and absent after; or a `###` principle or `##` section removed; or an obligation edited so its normalized clause set changes | **MAJOR** |
| Otherwise: an obligation present after and absent before; or a new `###` principle or `##` section | **MINOR** |
| Otherwise: text changed, normalized clause set unchanged | **PATCH** |

**Highest tier wins**, and the in-place edit is pinned by polarity rather than left open: an edited obligation whose normalized clause set is unchanged proposes **PATCH**; any change to the clause set proposes **MAJOR**.

**Why it costs a decision.** The classification is *mostly* derivable, and the residue — whether an edit clarified a rule or redefined it — is a judgment about intent the repo cannot see. Core's own instruction is the warrant for propose-then-confirm rather than ask-cold: *"If version bump type ambiguous, propose reasoning before finalizing."* It is last because the obligation diff is not complete until every area step has closed, and because being last keeps the one non-inferable field outside any shortening window.

**Overshooting is cheap and verified so.** No enforcing consumer reads the version, the ratification date, the last-amended date or the Sync Impact Report; they are producer-side bookkeeping. A MAJOR where PATCH was meant costs nothing mechanical.

**The fix for the ceremony risk is to show what was derived and ask only about the residue:**

```
Step 4 of 4 — Version bump

  1.0.0  →  2.0.0   MAJOR

  What forced it
    II. Testing — this obligation is in the document now and will not be after
    this run:
      "A functional requirement whose only stated evidence is manual
       verification MUST NOT be planned."

  Removing an obligation is a redefinition, which is MAJOR. If you meant it as a
  clarification rather than a change of rule, this is PATCH.

Accept 2.0.0, or say MAJOR, MINOR or PATCH.
```

- **One diff fact, the one that forced the tier**, quoted verbatim so the user can check the classification rather than trust it.
- **The middle sentence isolates the residue.** It states the derivation rule as a rule, then names the only reading that changes the answer. That is the difference between a confirmation and ceremony: the user is ruling on intent, not re-deriving arithmetic.
- **Middle sentence per tier:**
  - MINOR — `Adding an obligation is new guidance, which is MINOR.` No PATCH offer: an addition is not a clarification.
  - PATCH — `The text changed but no obligation was added or removed, which is PATCH.`
- **`**Ratified**` appears nowhere.**

### 5.7 The footer table, and the Sync Impact Report

**Amendment mode has its own footer table**, replacing the authoring one. Run unchanged, the authoring table would reset the adoption date to today and drop the version to `1.0.0`, discarding the document's whole history — silently, because a fill is logged as a change and not as a question.

| Field | Value |
|---|---|
| `**Version**` | the confirmed bump applied to the parsed existing version. **Never a constant.** |
| `**Ratified**` | **carried forward, byte for byte, from the existing footer. Never today. Never rewritten. Never re-derived.** |
| `**Last Amended**` | today, ISO `YYYY-MM-DD` |

**The token-resolution half of structural conformance is unreachable in amendment mode**, and that is a consequence of the predicate rather than a new rule: A4 and A5 guarantee zero `[ALL_CAPS]` tokens and zero `TODO(` in the input, and W4 guarantees the write emits none. Only the footer, heading and Governance clauses run.

**Exactly one `SYNC IMPACT REPORT` block exists in the file at any time.** An amendment **replaces** the block it found. Core's step 4 says *prepend*, and prepending accumulates: one report on this repo is 1,512 characters against an 11,699-character body, so ten amendments would add roughly 15,000 characters of comment. The report is producer-side bookkeeping no enforcing consumer reads, git already holds the history, and a change-history section inside the document is net-new structure. **The step survives; its accumulation behaviour is redefined.**

The block, fixed fields, deterministic content, no model prose:

```
<!--
SYNC IMPACT REPORT
==================
Version change: 1.0.0 → 1.1.0
Bump rationale: MINOR — 2 obligations added to II. Test-Backed Change; no
  obligation removed or redefined.
Principles added: (none)
Principles modified: II. Test-Backed Change
Principles removed: (none)
Sections added: (none)
Sections removed: (none)
Principles carried forward unchanged: I, III, IV, V
-->
```

- **Every field prints on every run**, `(none)` when empty. A field that disappears when empty makes two amendments structurally different documents for no gain.
- **`Bump rationale` is one deterministic line**: the tier, the diff fact that forced it, and the principle it came from. It is the reasoning core requires be proposed before finalizing, written down because the user confirmed **against** it. W3 does not reach it — W3 constrains **principle bodies**, and the report is not a body.
- **`Principles carried forward unchanged` is net-new and is the field the amendment case needs most.** It answers *"what did this run not look at"*, and it is free: the touched set is already known.
- **`Follow-up TODOs` is dropped entirely.** The Deferred/Assumed table replaces it, and the predicate guarantees there are no TODOs to carry.
- **`Templates reviewed for alignment` is not written by the interview.** That is `constitution-sync`'s propagation pass, which the rescoped supersession preserves and which may amend the report post-write with file paths only. If sync is not installed, the field does not exist. The interview never fabricates it.

**The rest of the write.** W1, W2, W4 and W5 apply unchanged to the touched areas and to the assembled document's anchors. **W3 does not reach a carried-forward principle.** And **W1's "nothing else" is not applied retroactively**: a ratified document that carries a preamble, extra `##` sections or Rationale paragraphs keeps them. That is not a loophole — it is why the predicate is an anchors test.

### 5.8 Carried-forward principles and the quota

**A carried-forward principle is a third state beyond drafted and user-typed** — ratified in an earlier run. **For every self-review check it is treated as user-typed: log-only, never rewritten.** Treating it as drafted would let the pass rewrite ratified text.

**But it does not get one row per principle.** The flood-control collapse applies:

```
4 principles carried forward unchanged and not reviewed: I, III, IV, V.
```

**The obligation quota** is unchanged on a touched area, and a **carried-forward principle is exempt entirely** — its obligations were priced when it was written, and re-pricing would mean trimming a clause out of a ratified rule. **The document-level total is not gated**, and no rule is invented to gate it: an amendment that pushes the document past 25 obligations logs it on the accounting line and does nothing else. The alternative is trimming an untouched, ratified rule, which every settled rule in this design forbids.

### 5.9 The zero-principle "What would you add?"

A zero-obligation constitution passes the amendment predicate and routes here, correctly — and *"what changed?"* is a strange question to put to a document where nothing was ever there.

**The trigger is zero *principles*, not zero obligations, and the difference is deliberate.** The accounting-line clause fires on zero obligations because that is what a consumer observes. Here the oddity is different: a document with three principles and no obligations still has content that could have changed, so `What changed?` reads fine. Only a document with **no principles at all** has nothing to have changed.

```
Found a ratified constitution — tally Constitution, v1.0.0, ratified 2026-08-26,
and it names no principles. It is a valid document; it just does not gate
anything yet.
Plan-time gate: present.

Amendment mode. To write a new one from scratch instead, remove
.specify/memory/constitution.md and run this again.

At most 5 decisions: this one, up to 3 areas you name, and one version-bump
confirmation. Follow-up turns that clarify an answer you already gave never add
a decision. If you still have nothing to add, say so and this run ends here
without writing anything.

Step 1 — What would you add?

Name up to three areas to write a rule for. Naming more defers the rest to a
later run.

  The areas this interview covers
    Negative scope · Technology and dependency boundaries · Architecture and
    structural limits · Surface contract · Irreversible and destructive
    operations · Testing and verification

Name the areas, or say there is still nothing to add.
```

- `What changed?` → `What would you add?`; `say nothing changed` → `say there is still nothing to add`. The consent mechanism survives intact, in the tense the document actually has.
- **`it names no principles. It is a valid document; it just does not gate anything yet.`** — a user landing here has a file that looks broken and is not.
- **The inventory is the six always-ask areas, and it must be.** The Deferred/Assumed table is terminal-only and provenance stays out of the document, so **no run can know which areas produced no principle in a previous run**; getting that would need net-new cross-run state. A conditional cannot fire in amendment mode, so the six always-ask areas are the complete, free, correct list.
- Everything else — the ceiling, the exclusions, the follow-up, the bump — is unchanged. The set diff still runs; on a document naming no sets it prints its empty form.

### 5.10 The tool-initiated amendment is out of scope

The interview never proposes an amendment on its own, and the reason is evidential. *"Repeatedly waived"* is unobservable at HEAD: a waiver is a free-text row in `## Complexity Tracking` inside a per-feature `specs/###-feature/plan.md`, **nothing aggregates those rows** — `/analyze` writes no files at all and neither `/analyze` nor `/converge` reads across features — and **the rows name no principle**, so mapping a row to a principle is model judgment over prose. The strings `waiv` and `deviat` return zero matches across every file under `templates/commands/`. `/analyze` also forbids it by contract: *"If a principle itself needs to change, that must occur in a separate, explicit constitution update outside `/speckit.analyze`."*

Making it observable needs a waiver ledger and a principle id on every Complexity Tracking row — net-new cross-feature state, outside a preset that must not modify core command files.

---

## 6. `$ARGUMENTS`

*Settled in [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15), correcting one clause of [Preset shape](https://github.com/DigitalWink/spec-kit-for-human-team/issues/4).*

**The shipped ruling, verbatim:**

> **Non-empty `$ARGUMENTS` are pre-supplied answers, not a bypass. Each clause pre-fills the one area step whose gate question it answers; every area still runs and still costs its decision, so the announced count never changes because of `$ARGUMENTS`. Core's `## Outline` never runs, on any argument.**

**The escape hatch is a skip, not a fallthrough.** Behaving like the current command means executing core's `## Outline`, and that is the region carrying the `TODO(<FIELD_NAME>)` emitter — a literal fallthrough would ship, inside the preset whose purpose is a well-formed constitution, a second path that writes the exact bytes `/analyze` rates CRITICAL. There is **one** write path and one owner of it.

**Why the count does not move.** The count must be **exact** and announced before the first question. If arguments could drop it, the announced number would depend on how many free-text clauses "count as answers" — a judgment over prose, made before the first question, that nobody reviews and that two runs on the same input can decide differently. Attribution *is* a judgment and cannot be made a declared measurement; what can be controlled is **what the judgment is load-bearing for**. Under this ruling it decides only *which step gets pre-filled*, whose worst case is a mis-attributed pre-fill the user corrects in one word. Under the other reading it would decide the interview's central promise.

**The key is the eleven shipped gate questions of [§4.5](#45-the-eleven-gate-questions)** — a closed, shipped set that the wrapper already carries, which is the most reproducible key available and costs nothing.

- **Clause boundary:** a newline, or a sentence-terminating `.`, `?` or `!` — the same split that makes *user-typed* per clause.
- **At most one area per clause.** A clause answering two gate questions attaches to the **earlier in walk order**. Never both: one clause writing into two principles is an overlap by construction.
- **Arguments never fire a conditional.** A trigger is a repo signal. A clause that answers an **unfired** conditional's gate question attaches to nothing, exactly like a clause that answers none. This is the sharp edge and it is stated loudly: a user who types *"we deploy to k8s and must be able to roll back"* on a repo with no deploy descriptor gets no release-and-rollback area. Letting the clause fire it would make the announced count depend on parsing prose and would break the per-run cap of three, which is measured rather than asked.

**What the user sees.** An area with an attached clause runs as **shape A, unchanged**. The clause is a named provenance source:

```
Step 2 of 9 — Technology and dependency boundaries

Proposed rule
  The runtime dependency set is: click, json5, packaging, pathspec,
  platformdirs, pyyaml, readchar, rich, typer. A plan MUST NOT add a runtime
  dependency outside that set without recording, in Complexity Tracking, the
  alternative it rejected.

What this checks, on every future plan
  "Does this plan add a runtime dependency outside the set named in this
   constitution, and if so does Complexity Tracking name the rejected
   alternative?"

Where it came from
  Your command arguments — "we must never add a runtime dependency without
  recording why"
  pyproject.toml:7-17 — 9 declared runtime dependencies, names only

Accept, edit, or replace?
```

No new frame, no new prompt line, no new element.

**Two or more clauses on one area are joined into that one pre-fill**, in the order typed, and **every attached clause is quoted under `Where it came from`**. One drafted rule, one step, one decision. Nothing is dropped off screen, which is what makes the accept informed. The area's obligation quota applies to the drafted rule as it does to any drafted rule.

**Drafted or user-typed?** The drafted rule is **drafted** — quota-charged, body fidelity live — because the accepted string is the interview's composition, not the user's characters. The one exception is decided by machinery that already runs: **a clause that passes the admission test as written is offered as written**, and then the accepted string is the user's own characters — user-typed, quota-exempt, log-only.

**A pre-fill sourced from the user's own arguments is not anchoring**, so it is legal on the two steps the design otherwise keeps cold:

- **Negative scope** is cold because a pre-filled answer there is the purest form of the satisficing failure — a failure that needs something for the user to react to instead of generate. Their own words are their own generation, arriving earlier. The mandatory follow-up still runs, unchanged.
- **Amendment step 1** is cold to keep the **scan's** findings from anchoring the answer, which is why the set diff is deferred to the follow-up. Arguments are not the scan. Step 1 is pre-filled with the areas the clauses name, still prints, still blocks, and its prompt line still carries `or say nothing changed`, so the consent mechanism is intact. Remaining clauses then pre-fill the touched-area steps, and clauses count against the 3-area ceiling as user-named areas.

**A clause that attaches to nothing lands one `Argument unplaced` Deferred/Assumed row, and nothing else.** Column 4 quotes it verbatim, because content the user supplied produced nothing and a row the user cannot read is not reversible. **It is never routed into core's `## Outline`** — this is the case where the temptation exists, because an unplaced clause looks like exactly the input core's Outline would take.

**Not a line before the first question**, which is the other obvious home: the announced count sits between the scan's last progress line and the first question with nothing between it and real content, so a third conditional block there would have to sit **above** the count, competing for the one piece of pre-count real estate the gate probe's stack fact already owns — and unlike a stack fact this is user content, not an environment fact.

---

## 7. The output floor

*Settled in [The output floor: is a zero-principle constitution legal?](https://github.com/DigitalWink/spec-kit-for-human-team/issues/13).*

**There is no output floor, and none can be added. A zero-principle constitution is legal, and the interview writes it.**

**The floor already exists one level down: it is per-principle, it is the admission test, and it runs at draft time.** A document of admitted rules is admissible by construction, so a one-principle constitution is **above** the floor rather than near it. Restating it at the document level would add nothing.

**Three independent authorities, not one:**

1. **Core's own command says there is no mandated count** — `templates/commands/constitution.md:94`: *"The user might require less or more principles than the ones used in the template."* "Less" has no stated floor. The template's five principle slots are example slots, not a schema.
2. **All three enforcing consumers pass vacuously**, each traced to the line that would fire. `/plan` loads unconditionally, fills `## Constitution Check`, and ERRORs on unjustified violations — zero principles means zero gates, zero violations, no ERROR, and the template ships only the pointer `[Gates determined based on constitution file]`, so an empty fill is a legal fill. `/analyze` extracts principle names and MUST/SHOULD statements into an empty rule set; nothing conflicts with nothing and nothing is mandated, so no CRITICAL, and it writes no files at all. `/converge` appends a remediation task only for code that contradicts a constitution **MUST**; zero MUSTs yield zero findings and zero appended tasks.
3. **No code anywhere parses constitution content.** `scripts/` has zero occurrences of the word; `src/` reads the file only for hashing; `/analyze`'s vague-adjective detector is scoped to `spec.md`, `plan.md` and `tasks.md`.

**A document-level floor could not be observed by any consumer**, so it would fail the admission test's own evidence tier — the design cannot ship a check it would refuse as a principle. And every remedy a floor could reach for is already forbidden: **asking** (no check may ask), **refusing to write** (§7.2), and **inventing** a principle for an area the user declined (that is authoring content nobody asked for).

**The observable unit is obligations, not principles.** `MUST` is the pipeline's only enforcement trigger, so a document with three principles and no normative clause is **observationally identical** to one with zero principles: both load, both extract an empty rule set, both report zero. A principle-count trigger would miss it.

### 7.1 The minimum viable constitution

It is **W1 with W2 vacuous**. Nothing new is defined.

```
# <name> Constitution

## Core Principles

## Governance

<the amendment approver the Governance confirmation collected, plus any route-out lines>

**Version**: 1.0.0 | **Ratified**: <today> | **Last Amended**: <today>
```

| | Status at the floor |
|---|---|
| **W1** | **Fully binding.** All four anchors are emitted. `## Core Principles` is mandatory structure, not a token slot, so the slot-deletion rule does not reach it and the heading is emitted **even when empty**. That is what lets a later run route to amendment instead of authoring, which overwrites. |
| **W2** | **Vacuous** — *"skipping areas that produced no principle"* already covers the case where every area is skipped. No amendment was needed. |
| **W3** | **Vacuous.** No bodies. |
| **W4** | **Fully binding, and load-bearing here.** Zero `[ALL_CAPS]`, zero `TODO(`. This is what makes the empty document *safe* rather than merely *small*. |
| **W5** | **Fully binding.** |

**The empty `## Core Principles` heading stands alone. No marker sentence goes under it.** W1 says *nothing else*; provenance was already refused entry on the same rule, and **the document states what the rules are while the closing artifacts state what the run did**; and it would be prose inside the region `/analyze` and `/converge` extract from.

Note for a reader returning to the record: *"only `## Governance` plus the version footer are structurally mandatory"* is a true statement about what **consumers** require. W1 mandates four anchors, and the amendment predicate needs all four. Both are true of different objects, and W1 is what governs this flow's output.

### 7.2 Why the interview writes it

**Refusing to write is strictly worse, and the reason is mechanical.** `specify init` has already seeded `.specify/memory/constitution.md` from the core template on **every** project, and that seeded file carries exactly **20** `[ALL_CAPS]` tokens which `/plan` and `/analyze` have no carve-out for.

| Outcome | Tokens on disk after the run | Harmful in |
|---|---|---|
| Interview refuses to write | **20** | `/plan`, `/analyze` |
| Interview writes the empty document | **0** | nothing |

Refusing is worse for every consumer on every axis, with no compensating benefit, and it leaves the user in the exact state the feature exists to fix after charging them 7 to 10 decisions. It is also foreclosed independently: refusal requires the document to be **unproducible**, which is the precondition-check bar, and an empty document is perfectly producible — every anchor resolves, the Governance confirmation was answered, the footer is a constant.

**A marker is illegal twice over**: `[NO_PRINCIPLES]` or `TODO(PRINCIPLES)` is banned by W4 and by the no-placeholder rule; a prose marker is refused by §7.1. There is no third shape.

**Writing and staying silent is refused** too: the admission test promises that every rule names the artifact that answers it, and a document that names no rules keeps no promise at all.

**So it writes it, and says what it is** — one conditional sentence on the accounting line, which already prints ([§4.7.3](#473-the-change-log-and-the-accounting-line)). That line is the **measuring** half of the self-review, so *two act and three measure* is preserved exactly and the clause gains no power to alter the document. It costs **0 decisions, 0 obligations, 0 scan, 0 new artifacts, 0 document characters** and adds no Deferred/Assumed row kind: the per-area rows already carry every reversal sentence, and what was missing was only the **sum** of those rows — which is a measurement.

**And the disclosure arrives with the remedy.** The closing sequence prints the finished document and the three artifacts, **then** opens the correction round. So the user reads *"this constitution carries no obligations"* while the round is still open, with the Deferred/Assumed table directly below carrying one *"To change it, say"* per area that produced no principle.

### 7.3 On an amendment run

**There is no floor of either kind, on any run, so nothing needs exempting.**

| Amendment shape | What prints |
|---|---|
| **"Nothing changed"** — 1 decision, no write | **Nothing.** No draft, no write, no self-review pass, no accounting line. |
| **A run that writes, ending at ≥ 1 obligation** | The ordinary accounting line. The clause does not fire. |
| **A run that writes, ending at 0 obligations** | The accounting line **plus** the clause. |

**The amendment predicate is correct as written and must not gain a principle-count condition.** A zero-principle constitution passes it and routes a later run to amendment — that is right. Failing the predicate routes to authoring, and **authoring overwrites**: a zero-principle constitution is not an empty file but a **ratified document** carrying a Governance block the user paid a charged decision for and an adoption date. A count condition would reintroduce silent re-ratification by a different door.

**Two residues, both already covered.** The 3-touched-area ceiling means filling six areas from zero takes two amendment runs — the `Beyond the ceiling` row ships for it. And a user who wants a fresh authoring run has the escape of §5.1.

### 7.4 `/converge`'s undefined predicate

`templates/commands/converge.md` contains **zero** token or placeholder syntax anywhere in its 273 lines — the *"unfilled template"* predicate at `:87-88` names no detection method, `:129` repeats it, `:192` reports its outcome, and nothing anywhere defines it.

**It is not load-bearing here.** `/converge` writes a remediation task only for a `contradicts` finding, defined as code that conflicts with a constitution **MUST**. A document with zero MUST principles yields zero `contradicts` findings under **every** reading of the predicate, and the only thing that varies is one label on a summary metric. No reading can produce a harmful write, an ERROR, or a CRITICAL — and it is safe *because* of W4 and the MUST-only rule, which is settled machinery rather than luck.

**And it cannot be fixed from here**: `converge.md` is a core command file, which locked constraint 4 forbids the preset from modifying, and the manifest declares exactly one entry. It is [out of scope](#9-out-of-scope).

---

## 8. The test plan

*Settled in [Behavioral test plan: what a test of the interview's behavior asserts](https://github.com/DigitalWink/spec-kit-for-human-team/issues/14); the traces its three defect-derived tests need are shipped by [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).*

**Most of the interview's behaviour is genuinely unassertable by an automated test, and the useful work is drawing the line in the right place.** The reason is mechanical: the preset ships **no executable code** — one `wrap` entry, no `scripts:` — so every rule this design settled is prose an agent reads. Nothing in `src/` will ever call the gate probe, the amendment predicate, the announced count, the scan budget, the walk order or the write contract, so nothing in `tests/` can call them either.

**26 new automated tests in 4 homes, 6 manual checks, 5 fixture recipes, ~1 s added CI runtime, and 0 changes to `pyproject.toml`, workflows or markers.**

### 8.1 The four assertable surfaces

| Surface | Assertable? | Why |
|---|---|---|
| **Packaging** — catalog entry, wheel force-include | **Yes** | already tested; extends itself |
| **Shipped data** — `data/strictness-ladders.yml` | **Yes** | it is YAML, which is why it lives there |
| **Composition** — what the resolver produces from the installed stack | **Yes** | ordinary Python, and where the install-order claim lives |
| **Prompt text** — that the shipped wrapper *states* a settled rule | **Yes, weakly** | proves the design survived an edit, never that an agent obeyed it |
| **Run behaviour** — that an agent executing the prompt does the thing | **No** | needs an agent, a human answering, and a non-deterministic model |

**Two terms keep the line visible**, and conflating either with a behavioural test is the overclaim this plan exists to avoid.

- A **prompt-text invariant** asserts that the shipped wrapper *states* a rule — a required string, a required structure, an arithmetic relation between numbers printed in its own tables. It fails when the **design** is edited.
- A **fixture-validity assertion** asserts that a tree the design *measured against* still has the property that made it a fixture. It never runs the interview. It fails when the **repo** is edited.

They look similar and fail for opposite reasons. *"The wrapper's trigger-4 procedure conjoins a path search with a content condition"* and *"this repo has no database client"* guard the same number — this repo's announced count of 9 — against opposite threats, and neither catches the other's.

**Agent-in-CI is refused.** This repo does run agents in CI — seven `gh-aw` workflows — and every property is wrong for a test gate: label-triggered on issues rather than on a pull request; five required secrets; pinned firewall/proxy containers; a generated `.lock.yml` marked DO NOT EDIT; and output that is a PR or a comment, not a pass/fail. `gh-aw` is precedented here as **automation**, not as **testing**.

**Two house precedents carry the plan.** `TestConstitutionSyncPreset` in `tests/test_presets.py` — five tests over the working precedent preset, whose `test_wrapper_uses_core_template_and_propagates` is *already* a prompt-text invariant. And `tests/hooks/TESTING.md` — a checked-in fixture plus numbered manual tests, each giving the exact sentence to type at an agent and an **Expected Behavior** list, with no `test_*.py` so pytest never collects it. That is the repo's existing answer to testing a prompt an LLM executes.

### 8.2 The 26 automated tests

**Home 1 — `tests/contract/test_wheel_bundled_presets.py` — 0 new tests.** `test_every_bundled_preset_is_force_included` reads `presets/catalog.json`, selects every `bundled` entry, and asserts the matching `pyproject.toml` force-include. Adding the catalog entry extends it with no edit, and it goes **red first**. One caveat: it asserts the mapping only, not that `presets/constitution-interview/` exists on disk — a missing source directory is caught at wheel-build time by hatchling.

**Home 2 — `tests/contract/test_constitution_interview_ladders.py` — 7 tests** over `data/strictness-ladders.yml`.

| | Test | Asserts |
|---|---|---|
| B1 | `test_every_ladder_has_one_to_three_rungs` | `1 <= len(rungs) <= 3` |
| B2 | `test_areas_are_named_areas_and_unique` | every `area` is one of the eleven; no duplicates |
| B3 | `test_gate_question_on_first_rung_only` | rung 1 has `gate_question`; no lower rung does |
| B4 | `test_drops_on_every_rung_after_the_first` | rung 1 has no `drops`; every lower rung has a non-empty one |
| B5 | `test_rung_obligations_within_the_area_quota` | per rung, `1 <= count <= quota`, quota 3 / 4 / 2, counted by the accounting-line grep |
| B6 | `test_rungs_weaken_monotonically` | obligation count non-increasing down the ladder; character length strictly decreasing |
| B7 | `test_rung_text_carries_no_template_tokens` | no `[ALL_CAPS]`, no `TODO(` in any `text` or `drops` |

**B6 is a deliberate weakening of what the packaging and menu tickets claimed the test could reach.** *"Each rung forbids a strict subset of the one above"*, *"passes the route-out table"* and *"passes the admission test"* are judgments about the meaning of English sentences — a test would have to decide whether one sentence forbids less than another. They are **authoring-time judgments**, and B6 ships two monotonicity proxies instead. The underlying decision — ladders ship as a data file *because* a data file is assertable and prose is not — is untouched, and is the reason this home exists.

*Recorded, not asserted:* building each rung by **deleting a sentence** from the one above makes `drops:` fall out for free and makes sentence-containment reviewable. That is an authoring recommendation, not a shipped constraint — rewording a rung weaker is also legal.

**Home 3 — `tests/test_presets.py::TestConstitutionInterviewPreset` — 7 tests**, modelled on `TestConstitutionSyncPreset` and placed immediately after it.

| | Test | Asserts |
|---|---|---|
| A1 | `test_manifest_provides_single_wrap_of_constitution` | exactly one `provides.templates` entry; `type: command`, `name: speckit.constitution`, `strategy: wrap`; no `constitution-template`; no `speckit.tasks` |
| A2 | `test_catalog_lists_bundled_preset` | `bundled: true`; version matches the manifest; `provides.commands == 1`, `provides.templates == 0` |
| A3 | `test_wrapper_frontmatter_declares_description_and_handoffs_and_no_scripts` | frontmatter parsed as YAML: `strategy: wrap`, `description` present, `handoffs` present, **`scripts` absent** |
| A4 | `test_core_template_placeholder_comes_first` | `{CORE_TEMPLATE}` is in the body and its index **precedes** the supersession block |
| A5 | `test_wrap_composes_over_core_constitution` | after install, layer 0 is `wrap` and the last layer is `core (bundled)` |
| A6 | `test_resolved_content_embeds_core_and_supersedes_outline` | no literal `{CORE_TEMPLATE}` survives; core's `## Scope Guard` and both hook sections survive; the supersession sentence names core's `## Outline` |
| A7 | `test_composes_with_constitution_sync_in_either_install_order` | install both presets into two tmp projects in opposite orders; the layer order is **identical**, the interview is outermost, and sync's `## Constitution Template Sync` section survives |

**A7 is the one genuinely behavioural claim in the whole settled record that a test can actually settle.** Both halves — deterministic composition independent of install order, and sync's post-write pass surviving the rescoped supersession — are ordinary Python over the resolver, and the alphabetical tie-break at equal priority is already tested in the house.

**Home 4 — `tests/test_presets.py::TestConstitutionInterviewWrapperText` — 8 tests.** A separate class, because the class name is the honest label. Its docstring, verbatim:

> These assert that the shipped wrapper **states** a settled rule. They do not assert that an agent executing it does anything. Behavioural checks live in `tests/constitution-interview/TESTING.md`.

| | Test | Asserts |
|---|---|---|
| C1 | `test_gate_status_line_ships_all_three_states` | `Plan-time gate: present`, `Plan-time gate: absent on this preset stack`, `Plan-time gate: not determined` all ship; the third names `specify preset resolve speckit.plan` |
| C2 | `test_gate_absent_stack_fact_ships_and_is_conditional` | the stack-fact sentence ships, with its print condition (probe returns *absent*) and its placement (before the count) |
| C3 | `test_extension_hook_clause_ships_and_names_its_condition` | the hook exclusion clause ships and names `.specify/extensions.yml` and both `before_constitution` / `after_constitution` |
| C4 | `test_zero_obligation_clause_ships_with_its_drop_condition` | the clause's three sentences ship, and the wrapper states that sentence 3 drops when the document has principles but no obligations |
| C5 | `test_trigger_four_conjoins_path_search_with_a_content_condition` | the trigger-4 row does not stand on a bare path search; the **same row** names a database-client dependency or an ORM base class |
| C6 | `test_scan_allocation_rows_sum_to_the_stated_budget` | parse the allocation table; the reads column sums to the stated read budget, the searches column to the stated search budget, and one row is the gate probe |
| C7 | `test_area_unanswered_row_names_the_step` | the `Area unanswered` row spec uses `Step N` in `Where`, and does not use `Not asked` for that row kind |
| C8 | `test_arguments_are_answers_and_never_fall_through_to_core_outline` | the wrapper states that `$ARGUMENTS` are **pre-supplied answers** and that core's `## Outline` never runs |

**Matching discipline, binding on all eight:** match on the **stable token** the design fixed — `Plan-time gate: present`, `before_constitution`, `Step N`, `pre-supplied answers` — after normalising whitespace, **never** on a full paragraph. A test that matches a reflowed 60-word paragraph fails on the first line-wrap and teaches the next implementer to delete it.

**C6 is the only test that constrains the wrapper's format**, and it is stated as such: the scan allocation must ship as a markdown table with numeric reads and searches columns and a stated total. That is how §3.2 already writes it. **It asserts internal consistency, never a specific number** — which is why it passes unchanged now that the total is settled at 40 and 13, and why the table's fifth row costs it nothing. Whether to pin the number is a later call, not a required change.

**Home 5 — `tests/contract/test_constitution_interview_fixtures.py` — 4 tests.** Fixture-validity assertions, numbered `V` to keep them clear of the defect ids. They never run the interview and never read the preset.

| | Test | Asserts | Protects |
|---|---|---|---|
| V1 | `test_dogfood_repo_does_not_fire_the_data_lifecycle_trigger` | `src/specify_cli/bundler/models/` exists (so the path search still hits), `pyproject.toml` declares no database client, and no module under it defines or imports an ORM base | this repo's announced count of **9**. If it fails, trigger 4 fires, the count becomes 10, and every measured number in the map's index silently becomes wrong |
| V2 | `test_git_extension_declares_a_constitution_hook` | `extensions/git/extension.yml` declares `hooks.before_constitution` | fixture F5 |
| V3 | `test_seeded_constitution_template_fails_the_amendment_predicate` | shipped `templates/constitution-template.md` carries at least one `[ALL_CAPS]` token | **upstream drift.** This is a fork; if upstream detokenises the template, F2 silently routes to amendment and the authoring half of the round-trip check stops existing |
| V4 | `test_this_repos_constitution_satisfies_the_amendment_predicate` | H1, `## Core Principles`, `## Governance`, a parsing footer, zero `[ALL_CAPS]`, zero `TODO(`, and **exactly one** `SYNC IMPACT REPORT` | F1 as the amendment fixture, and the N=1-suffices argument |

**V3 and V4 together are the discrimination test** — they assert the two documents still fall on **opposite sides** of the amendment predicate, and the margin is narrower than it looks. `templates/constitution-template.md` already has an H1, `## Core Principles`, `## Governance` and a parsing footer; it fails the predicate on the **`[ALL_CAPS]` condition alone**, 20 tokens, one of them inside the H1. One upstream commit filling those tokens with examples flips `specify init` from authoring to amendment. Both tests re-implement the predicate in Python; that is deliberate, and it tests the fixtures rather than the agent.

**What runs them:** `uv run pytest`, unchanged. `testpaths = ["tests"]` collects all four homes with no configuration change, and CI runs the whole suite on 3 OSes × 2 Python versions. **No new marker, and that is load-bearing**: `--strict-markers` is set and no `markers` key is declared, so any `@pytest.mark.manual` would fail collection. Putting the manual checks in Markdown rather than in skipped pytest functions is what keeps `pyproject.toml` untouched.

### 8.3 The 6 manual checks and the 5 fixture recipes

`tests/constitution-interview/TESTING.md`, modelled on `tests/hooks/TESTING.md`: a numbered check, the exact sentence to type at the agent, an **Expected Behavior** list of the literal lines to look for, and the fixture recipe. No `test_*.py` in the directory.

| | Fixture | Recipe | Cost |
|---|---|---|---|
| **F1** | **This repo, as it stands** | nothing | **zero** |
| **F2** | Bare `specify init` tree | `mkdir tally && cd tally && git init && specify init --here --ai claude && specify preset add constitution-interview` | one command |
| **F3** | Empty repo | F2 in a directory with no source files | same command, different cwd |
| **F4** | Project-override tree | F2 **+** write `.specify/templates/overrides/speckit.plan.md` containing no `Constitution Check` | +1 file |
| **F5** | Extension-hook tree | F2 **+** `specify extension add git` | +1 command |

**All five are recipes, none checked in.** `tests/hooks/` checks its tree in because the fixture *is* the content under test; here the fixtures are ordinary Spec Kit projects that one or two commands reproduce exactly, so checking them in would add a second copy of `.specify/` that rots against every core change.

**F1 is free, and it is the fixture the record kept getting wrong.** `.specify/` in this repo holds only `memory/constitution.md`; there is no installed `speckit.plan` for any agent, and `presets/lean/commands/speckit.plan.md` is preset source, not an installed command. So the *not determined* probe state — the one that looked hardest to reach — **needs no fixture at all**. The earlier suggestion that the *absent* state is reachable "with `lean` installed" is wrong by that ticket's own analysis: on any `lean` stack the interview either never runs or stops at the precondition. The clean *absent* fixture is F4's project override.

**Teardown** is `rm -rf` on a tmp directory for F2 to F5 and nothing for F1 — the F1 checks run **read-only**, which `TESTING.md` states as a precondition: do not let the agent write `.specify/memory/constitution.md`; copy the repo to a scratch directory if the run reaches a write.

| | Check | Fixture | Turns |
|---|---|---|---|
| M1 | Amendment run over a ratified document | F1 (read-only copy) | 3 |
| M2 | Authoring run over a seeded template | F2 | 7 |
| M3 | Floor run, every area *"I don't know"* | F3 | 7, all one word |
| M4 | Announcement on a gate-absent stack | F4 | 1, then abandon |
| M5 | Announcement with a constitution hook declared | F5 | 1, then abandon |
| M6 | Closing artifacts on a gate-absent stack, run to completion | F4 | 7 |

**M4 and M5 are one turn each**, because both clauses print on the announcement line before the first question: read it, check the clause, abandon the run. Placing the gate clause before the count so the user learns it before spending decisions is what makes its own check cheap.

**M6 exists because the routing table does not print early.** The *absent* status line is a closing artifact, so unlike the announcement clause it cannot be checked by abandoning. M6 is the expensive one and the only reason F4 needs a completed run.

**`TESTING.md` should cite the four prototype transcripts as the expected-output oracle.** They are on the local branch `prototype/constitution-interview-transcripts` under `docs/prototypes/constitution-interview/`, which is **not pushed** — either the branch is pushed or the checklist cites nothing. Do **not** merge the transcripts to `main` or check golden documents into `tests/`: they are a prototype of a design, not the expected output of an implementation, and they will diverge the moment the wrapper is actually worded. Cite the branch; do not freeze it.

### 8.4 The seven behavioural assertions, one verdict each

| # | Assertion | Verdict | Fixture | Automated guard beside it |
|---|---|---|---|---|
| **1** | the gate status line prints in all three states | **Manual** ×3 arms | *present* F2 · *absent* F4 · *not determined* F1 | **C1** |
| **2** | an `init`-seeded template routes to authoring; a ratified document routes to amendment | **Manual** ×2 arms | F2, F1 | **V3, V4** |
| **3** | `**Ratified**` is byte-identical across an amendment | **Manual**, folds into 2's amendment arm | F1 | none possible; the write is the agent's |
| **4** | exactly one `SYNC IMPACT REPORT` after N amendments | **Manual at N=1 only** | F1 | **V4** |
| **5** | a floor run writes four anchors, zero `[ALL_CAPS]`, passes the amendment predicate, and prints the zero-obligation clause | **Manual** | F3 | **C4** |
| **6** | the announced count carries the stack fact only when *absent*, and names hook turns only when declared | **Manual** ×2 arms | F4, F5 | **C2, C3** + **V2** |
| **7** | the accounting clause drops its third sentence on principles-without-obligations | **Retired as a standing check; kept as an optional variant** | F3 with one substituted answer | **C4** |

**N=2 is retired, and the reason is a property of the fixture rather than the replace rule.** Arguing that N=2 follows from N=1 *because* the report is replaced rather than prepended is circular — it assumes the rule the test exists to check. The real reason: **this repo's constitution already carries a `SYNC IMPACT REPORT` block**, so the N=1 run takes a document with one block as input and must produce a document with one block as output. That transition **is** replace-not-prepend. **The caveat is the useful half, and it belongs in `TESTING.md` beside the check:** if the amendment fixture is ever changed to a freshly-authored document with no prior block, N=1 would only prove that a first block is written and **N=2 becomes mandatory again**.

**Assertion 7 is retired on cost, not on reachability.** The cheapest route is F3 with exactly one answer substituted — answer *"I don't know"* to five of the six cold areas, and on the sixth type a rule with no modal verb (*"we keep dependencies small"*) and decline the checkable rewrite after the push-back. The document then holds one principle and zero obligations, which is exactly the shape needed, at one manual run of seven one-word turns. But what fails if it regresses is one extra sentence pointing at a table empty of the row kind it names — a cosmetic defect, bought at a whole manual run every release cycle forever. The drop condition ships as **C4**, which catches the only regression that matters (someone deleting the conditional), and `TESTING.md` records the variant as *optional, run once when the wrapper's closing artifacts are first authored, then never again*.

### 8.5 Which defects became regression assertions

**Three of eight**, and the reason is the criterion: those three are the only defects whose fix leaves a **checkable trace in shipped content**. The other five are fixed by adding or rewording a sentence whose absence a reader notices and no parser can.

| | Defect | Becomes | Trace to match |
|---|---|---|---|
| **D1** | trigger-4 path search over-fires on `models/` | **C5 + V1** | in the trigger-4 row: `migrations`, `database client`, `ORM base class` |
| **D2** | the gate probe had no room in the allocation | **C6** | `Gate probe` as a row label; both column headers; the `Total` row |
| **D3** | three greps priced as two searches | **C6**, free | a mispriced row makes the column disagree with the total |
| **D7** | `Area unanswered`'s `Where` said `Not asked` | **C7** | `Area unanswered`, `Step N` |
| D4 | what a rejectable candidate *is* | no test | a wording choice between two readings; both parse |
| D5 | the set diff's `Named here` column | no test | the fix adds a state whose correctness depends on a document nobody ships |
| D6 | a touched area is replaced, undisclosed | no test | one disclosure sentence; a prompt-text check would be a tautology on the string it adds |
| D8 | the correction-round example | no test | the fix is a rule, not a string |

**All four traces are shipped**, and **C5, C6, C7 and V1 pass exactly as specified**.

### 8.6 Cost

| | |
|---|---|
| New automated tests | **26** — 7 ladder, 7 manifest/composition, 8 prompt-text, 4 fixture-validity |
| Tests that write themselves | **1** — the force-include contract, and it goes red first |
| New test files | **2** — `test_constitution_interview_ladders.py`, `test_constitution_interview_fixtures.py` |
| New classes in an existing file | **2**, both in `tests/test_presets.py` |
| New manual checks | **6**, plus 1 optional variant run once |
| Fixtures | **5 recipes, 0 checked in** |
| Manual turns per full pass | **26** |
| Added CI runtime | **~1 s**, measured against 7 analogous tests at 3.99 s wall |
| Changes to `pyproject.toml` | **0** |
| New CI workflows / markers | **0** |
| Prompt-format constraints on the wrapper | **1** — the scan allocation ships as a table (C6) |

**Where the effort actually goes is the six manual checks**, which need a human with a coding agent and roughly 26 turns of answering — once before release, and again whenever the wrapper's closing artifacts change. That is the honest recurring price of shipping behaviour as a prompt. What the plan does is make sure the price is paid **once per release** rather than once per commit.

**Write Home 4 as you word each block of the wrapper, not afterwards.** A prompt-text invariant written from the settled record is a specification; one written from the prompt you just wrote is a tautology.

---

## 9. Out of scope

From the map. These do not graduate — they would need a fresh effort. **A builder needs this list to know what not to build.**

- **A non-gated `## Domain Vocabulary` section.** Real constitutions carry one and teams value it, but it is not checkable by `/plan` or `/analyze`, so it breaks the locked content model.
- **Writing a condensed restatement into `AGENTS.md` / `CLAUDE.md`.** Spec Kit treats agent context files as user-owned; only an opt-in extension touches them.
- **Grandfathering machinery** for when a new MUST would flag existing code. A large sub-feature, and the critics argued it targets an enforcement surface that barely exists.
- **A precedence order for colliding principles.** Net-new document structure that no downstream command reads.
- **A tool-initiated amendment** — §5.10. The trigger is unobservable at HEAD, and making it observable needs a waiver ledger and a principle id on every Complexity Tracking row, which is net-new cross-feature state outside a preset that must not modify core command files. **One consequence lands on this design:** the dossier's proposed Governance clause *"principles are re-checked when a principle is waived twice"* must **not** ship, because it states an unimplementable trigger as a MUST (§3.10).
- **Defining `/converge`'s "unfilled template" predicate** — §7.4. A real defect, unreachable from here, and not load-bearing for this design.
- **Multi-person consensus.** A separate, much larger feature.
- **A `/speckit.specify` handoff.** Deliberate: running Spec Kit's own commands inside the Spec Kit repo collides on vocabulary, so Claude Code implements directly from the map.

---

## 10. Stated residues

Recorded because the design names what it does not repair. **None of these blocks the build.** Each is a place where a builder should follow the record's own framing rather than settle it silently.

**In the machinery:**

- **A drafting-invented modal sentence outside every accepted string, in a body that otherwise passes containment, survives the self-review** and is reported rather than removed. Assembly can therefore mint an obligation the draft-time quota never priced. Removing it would need the sentence surgery body fidelity refuses (§3.11.2).
- **A thresholded set is a grammar, not an inventory**, so a plan adding a below-threshold member trips the gate once. The remedy is the designed one, and the near-miss band is the user's chance to prevent it (§3.7.5).
- **The artifact ceiling's permissive direction has no detector**, and none is warranted: removing a distribution artifact leaves the ceiling generous, and a generous ceiling permits without misleading.
- **Whether a route-out verdict breaks the satisficing-collapse run is not settled.** It is not in the transparent list, not an edit, and not an answer. The prototype ruled that it **breaks** the run — the user proposed replacement prose, which is engagement — and marked the ruling. That is a live question about the collapse's own definition, and it is deliberately left open.
- **`Left verbatim` has no flood control** and degenerates to one row per principle on a fully cold run. The mechanism already exists — flood control is stated for the no-evidence check and already applied to a second row kind — so a third application is the same move; the prototype applied it and marked it. Left open rather than settled.
- **Nothing says which source supplies the runtime version on the platform-matrix step** when the CI matrix and the manifest floor disagree. The prototype took the OS list from CI and the floor from the manifest, on the durability rule, and recorded that the other choice writes a pin dressed as a floor — which is exactly the row that went stale in today's document. That reading follows from the durability rule and needs no new decision.

**In the experience:**

- **On greenfield the interview is an interrogation**, because there is nothing to confirm: 0 of 7 decisions closable in one word, against 6 of 9 on brownfield. The wording principle has nothing to work with, and the `Why it matters` line carries the entire informational load.
- **The strictness ladder is most valuable exactly where the menu gate refuses to open it** (§3.4.2). If effort goes anywhere after the first build, it goes on making an **early** repo — not an empty one — reach its ladders.
- **The batch summary is very hard to reach**, and cannot fire at all on the repo the whole effort dogfooded against. Whether the mechanism earns its specification is a live question.
- **At the floor, the closing artifacts and the correction round arrive together in one screen**, and a user who has just answered *"I don't know"* six times is the least likely reader to read a table carefully — while that table is the only remedy they will get. There is no mechanism that draws the disengaged user back to it, and **by construction there cannot be one: no check may ask.** A real limit, not fixable by wording.
- **On an amendment, the routing table is the loudest thing that happened and it arrives after the write.** Five clauses of a ratified principle can leave the document with named destinations, and the correction round cannot put any of it back, because a route-out verdict is not overridable. On an authoring run that is fine — nothing was there before. On an amendment it is a design limit that follows from the two settled rules together.
- **Overlap fires structurally rather than by accident.** Negative scope holds an exclusive claim over *"we do not add X"* rules so it will not collide with the dependency area — but the collision the prototype actually produced was with **irreversible operations**, through a candidate the scan proposed and the user attached at step 1. The overlap check catches it, log-only, which is the right behaviour; the exclusive claim is simply stated against the wrong neighbour.

---

## Appendix: what the tracker superseded

The body above is self-sufficient. This appendix serves a reader going back to the tickets, which stay the primary sources: it says **which later resolution overrode which earlier one**, so a claim found in an older comment can be checked against the current state.

| Later ticket | Overrode |
|---|---|
| [Interview mechanic](https://github.com/DigitalWink/spec-kit-for-human-team/issues/3) | **Taxonomy** — supersedes the typed acceptance priced as an 8th decision; corrects *"the one area a repo scan cannot pre-fill"* to *hardest to infer* |
| [Stop rule](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2) | **Taxonomy** — retires the 8-decision cost and the "11–13 with conditionals" band; charges the Governance confirmation. **Interview mechanic** — `Area N of 6` → `Step N of M`; the batch summary amends *"a fired conditional is indistinguishable in the walk"*; the Deferred/Assumed table amends the two-artifact closing sequence to three. Replaces any global obligation cap with the tiered per-area quota |
| [Self-review pass](https://github.com/DigitalWink/spec-kit-for-human-team/issues/7) | **Stop rule** — the obligation quota counts **case-insensitively**. **Core** — the comment-removal judgment is superseded by unconditional deletion; the core-supersession list grows from four lines to nine |
| [Inferred sets](https://github.com/DigitalWink/spec-kit-for-human-team/issues/10) | **Taxonomy** and the map — the plan template's `3` is an *example row*, not a shipped ceiling; the wheel force-includes **seven** independently versioned units, not nine |
| [Menu candidates](https://github.com/DigitalWink/spec-kit-for-human-team/issues/11) | **Interview mechanic** — the candidate label loses *"typical for this project type"*; *"up to five typical rules"* narrows to nested strictness variants. Corrects *"if a scan fills all six always-ask areas"* — at most five are ever scan-fillable, and a cold area is not a menu |
| [Preset shape](https://github.com/DigitalWink/spec-kit-for-human-team/issues/4) | **Self-review pass** — the nine-line supersession list is subsumed by block supersession; `replaces:` is **not** a schema field, so lean replaces by the `strategy` default. Records four override strategies, not two, and that **ten** ladders are reachable, not eleven. **Taxonomy's handoff** — the quoted `tasks.md:147` line was truncated; it carries a second trigger |
| [Gate absent](https://github.com/DigitalWink/spec-kit-for-human-team/issues/9) | **Preset shape** — `{CORE_TEMPLATE}` goes **first**, not last; *"no conflict with `constitution-sync`"* fails, so supersession is **rescoped to producing and writing**; *"wrapping lean still produces the interview"* is false; *"writes exactly once"* narrows to one writer of the **body**. Lean's constitution stub is **33** lines; lean's plan is **19** lines, not 17 |
| [Amendment mode](https://github.com/DigitalWink/spec-kit-for-human-team/issues/6) | **Self-review pass** — its token table would silently re-ratify, so amendment mode gets its own footer table. **Core** — the Sync Impact Report is **replaced**, not prepended. Corrects the semver citation to `constitution.md:100-104`; records that `templates/constitution-template.md` carries **no** Sync Impact Report; records that `specify init` seeds the constitution **unconditionally** at `commands/init.py:931`, which narrows nothing to `constitution-sync` |
| [Output floor](https://github.com/DigitalWink/spec-kit-for-human-team/issues/13) | **Self-review pass** — the accounting line gains a conditional zero-obligation sentence. **Amendment mode** — its A4 justification (*"`/converge`'s own predicate"*) is unsupported, though A4 itself stands. Corrects the no-mandated-count citation to `constitution.md:94`. Adds one `## Out of scope` entry |
| [Per-area question wording](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12) | **Preset shape** — the ladder schema gains `drops:`. **Self-review pass** — the Deferred/Assumed kind set grows from ten to fourteen, and one kind renders in three columns. **Gate absent** — two zero-principle variants of the status line. **Inferred sets** — the wider prompt is scoped to the two *inventory*-bearing steps and the scalar step gets its own. **Amendment mode** — both announcement blocks reworded. **Taxonomy** — five conditional gate questions minted. Corrects *"nine area steps"*: **eleven** areas can be asked; nine is this repo's measured run length |
| [Prototype](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8) | Records eight defects against **Inferred sets** (D1, D3), **Gate absent** (D2), **Per-area wording** (D4, D5, D7, D8) and **Amendment mode** (D6). Corrects: 84 command declarations across **12** files, not 13; `check` and `init` sit at **2** sites each in the near-miss band; the negative-scope citation is `README.md:376-382`, not `:290-293`; the floor produces **6** Deferred/Assumed rows, not nine; the Sync Impact Report is 1,512 characters and the body 11,699 |
| [Behavioral test plan](https://github.com/DigitalWink/spec-kit-for-human-team/issues/14) | **Preset shape** and **Menu candidates** — nesting, the route-out table and the admission test are **authoring judgments**, not assertions. Retires N=2 with its caveat. Records that the map's Notes never contained the word *parity*, and that the parity test family is not the relevant precedent |
| [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15) | **Eighteen amendments over eight tickets.** **Stop rule** — the search budget moves 12 → **13**. **Inferred sets** — a fifth allocation row, phase 1 `6/6`→`5/5`, phase 2 searches `2`→`3`, phase 3 reads `30`→`31`; the surface-grammar cost becomes 3 searches; trigger 4 gains its content condition and a charged read; the cost of keeping provenance out of the document is named. **Gate absent** — the probe gets its own row, ordered last, and a fifth progress line. **Per-area wording** — a rejectable candidate is a **drafted rule**; a fifth progress line; the correction-round example quotes column 6 of the first row printed and `reverse`→`act on`; `Area unanswered` renders in three columns with `Where` = `Step N — <area>`; a fifteenth kind, `Argument unplaced`; three states for `Named here`; the touched-area replacement disclosure and rename notice. **Self-review pass** — the kind set reaches **fifteen** and one kind takes a rendering exception. **Amendment mode** — the touched-area disclosure; the one-sided set diff; the scan becomes **2 reads and 4 searches**. **Preset shape** — *"the announced count drops accordingly"* does not survive. **Interview mechanic** — *"explicitly rejectable candidates"* is sharpened to drafted rules |

**One propagation applied here, not settled here.** The `Trigger unresolved` row's `Why` column quotes the scan budget. [Errata and loose ends](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15) amends that budget to **40 reads and 13 searches**, so the row quotes the amended number — the same mechanical propagation that ticket performed itself when the three-grep correction moved the amendment scan to 2 reads and 4 searches.
