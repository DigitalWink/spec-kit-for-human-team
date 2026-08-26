# Arm A — Brownfield authoring, against this repo

**THROWAWAY PROTOTYPE.** Not shipped content. Primary source for [issue #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

Read [README.md](README.md) first for the marking legend and the fixture premises.

---

## Fixture premise

This repo (`DigitalWink/spec-kit-for-human-team`, `specify-cli`) at HEAD, with three stated conditions:

1. **The project is spec-kit-initialized.** `specify init` has run, so `.specify/templates/` and the agent's `speckit.*` command files exist, and `{SCRIPT}` resolves `constitution-template`.
2. **`constitution-interview` is installed**, at default priority, with no other preset.
3. **`.specify/memory/constitution.md` has been removed.** Required, and not cosmetic — see the finding below.

[DEFECT A-0] **Arm A is unreachable on this repo as it actually stands.** The ticket asks for an authoring transcript against this repo. Today's `.specify/memory/constitution.md` passes every condition of #6's amendment predicate, measured:

| | | |
|---|---|---|
| A1 | file exists | pass |
| A2 | H1 at `:34` (`# Spec Kit Constitution`), `## Core Principles` at `:42`, `## Governance` at `:194`, footer at `:214` | pass |
| A3 | footer parses — `1.0.0`, `2026-06-19`, `2026-06-19` | pass |
| A4 | `\[[A-Z][A-Z0-9_]{3,}\]` matches | **0** — pass |
| A5 | `TODO(` occurrences | **0** — pass |

So a run here routes to **amendment**, not authoring. That is correct behaviour and it is arm D. Arm A therefore states removal as a premise. Recorded because the ticket's own framing ("write a sample transcript once against this repo") assumes an authoring run that the settled predicate will not give it.

[FINDING A-0b] **The literal repo state fails a different check first.** `.specify/` here contains only `memory/constitution.md` — no `templates/`, no installed agent command files, no `.specify/extensions.yml`. On the uninitialized tree the template precondition check (#4 Part 2, as sharpened by #9's build obligation 3) stops the run before any count is announced, and the gate probe returns **not determined**. Both are recorded as fixture-construction facts, not as arm A's behaviour. The *not determined* status line is exercised in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

---

## The scan

```
Reading the build manifest and the workflow directory...
Resolving conditional triggers - 5 of 5.
Measuring the dependency set, the artifact ceiling and the surface grammar.
Reading source for negative scope, testing and irreversible operations - 3 files.
Scan complete - 8 files, 12 searches.
```

[DEVIATION A-1] **The ledger is derived here, not carried from #2 §3's `34 files, 9 searches`.** That figure is a mock-up. #12 §7 named carried counts as this effort's repeat failure mode; #10 Correction 1, #4 Correction 7 and #12's own Part 7 are the four prior instances. The full ledger:

| Phase | Reads spent | Listings + searches spent | What |
|---|---|---|---|
| 0 — shared sources | 1 | 2 | `pyproject.toml`; repo-root listing; `.github/workflows/` listing |
| 1 — trigger resolution | 4 | 5 | T1 two searches; T2 one search; T3 read `test.yml`; T4 one search **+ one read**; T5 two reads + one search |
| 2 — measured sets | 0 | 3 | three greps: named verbs, defaulted verbs, flags |
| 3 — content inference | 3 | 1 | `AGENTS.md`, `README.md`, `integrations/base.py`; one search for destructive operations |
| #9 gate probe | 0 | 1 | `Constitution Check` in the installed `speckit.plan` |
| **Total** | **8 of 40** | **12 of 12** | |

Three things fall out of the ledger, all reported in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8):

- **The search budget binds exactly and the read budget does not come close** — 12 of 12 against 8 of 40.
- **#9's gate probe has no allocated room.** #10 §3's allocation sums to exactly 12 with, in #11's own words, "zero slack"; #9 then charges `+1 search ... against its 12`. It fits here only because phase 1 underspends by one and phase 3 by one, and only if the probe is ordered *after* phase 3 so forward-only spillover can reach it.
- **#10 §2b names three greps and prices them as two searches.**

### Trigger resolution, measured

| # | Conditional area | Fires? | Evidence |
|---|---|---|---|
| 1 | Failure semantics and operating envelope | **yes** | 29 `subprocess.run` sites across 10 modules; 40 `write_text` + 18 `write_bytes` sites outside a temp directory |
| 2 | Security and privacy at the data boundary | no | no auth, identity, session, password-hashing, crypto or payment package in `pyproject.toml:7-17`; no redaction or masking helper in source. `SECURITY.md`, `codeql.yml` and `security.yml` are present and #5 forbids firing on any of them |
| 3 | Supported platform and runtime matrix | **yes** | `.github/workflows/test.yml:33-34` — `os: [ubuntu-latest, windows-latest, macos-latest]`, `python-version: ["3.13", "3.14"]` — 3 OSes and 2 runtimes |
| 4 | Data lifecycle | no | see [DEFECT A-2] |
| 5 | Release and rollback | no | the only workflow `environment:` keys are `github-pages` (`docs.yml:61-63`) and `pypi` (`publish-pypi.yml:65`); no `Dockerfile`, compose file, `*.tf` or k8s descriptor anywhere |

Two fired, so this repo lands at **9 decisions** — reproducing #10 §3a exactly.

[DEFECT A-2] **#10 §3a's trigger-4 procedure is broader than #5's trigger-4 signal, and on this repo the gap is live.** #10 prices trigger 4 as "phase-0 manifest **+** one search for `migrations` / `models` / `seeds` / `fixtures` paths". Run literally, that search hits `src/specify_cli/bundler/models/` — which holds `catalog.py`, `manifest.py` and `records.py`, plain `@dataclass` types for bundle manifests and catalogs, with no ORM, no database client and no user-supplied data. #5's signal is narrower: "a store of user-supplied or user-identifying data — a migrations directory, **ORM model files**, a database client dependency, or a seed/fixture script."

So the *signal* says no and the *procedure* says yes, and something has to choose. Choosing is exactly the "model judgment over prose" #10 §2 forbids. **This transcript spends one phase-1 read on `bundler/models/catalog.py` to disambiguate and rules that trigger 4 does not fire.** It costs 1 read of a 40-read budget with 32 unspent, and it makes the decision a measurement.

The stake is the announced number. Fired the other way, T1, T3 and T4 all fire, three conditionals run, and this repo announces **10 decisions**, not 9 — so #10's own headline measurement depends on a disambiguation #10 did not specify. Wording fix in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

A weaker sibling: T5's search finds two `environment:` keys and is excluded only because #5 named this repo's two by hand.

---

## The announcement

```
This interview closes 9 decisions: 6 standard areas, 2 your repo triggered
(failure semantics, platform matrix), and 1 Governance confirmation. Follow-up
turns that clarify an answer you already gave never add a decision.

Step 1 of 9 - Negative scope
```

Neither conditional clause prints. The gate probe returns **present** (the installed `speckit.plan` is core's, which carries `Constitution Check` at `:68` and `:72` — measured, 2 occurrences), so #9's stack-fact paragraph is omitted. `.specify/extensions.yml` does not exist, so #4's hook-turn clause is omitted.

---

## Step 1 of 9 — Negative scope

```
Step 1 of 9 - Negative scope

What is this project deliberately not going to do - something a reasonable
engineer might propose, that this project would refuse?

Why it matters
  A rule that forbids nothing gates nothing, and what a project refuses is the
  part hardest to read off its code. You answer first; what the scan found
  comes after.

Answer in your own words, or say there is no rule here.
```

> [FIXTURE] **A plan MUST NOT add a code path in the Specify CLI that creates, updates, removes, resolves, or migrates an agent context or instruction file. That is the `agent-context` extension's job.**

```
Still on negative scope - the scan found these too. They cost you nothing to reject.

  1  "The Specify CLI carries no agent-context state whatsoever; the CLI never
      creates, updates, removes, resolves, or migrates a context/instruction file."
      - AGENTS.md:213
  2  "Installs are idempotent and confined to the project root; all consume/author
      commands work offline."
      - README.md:380-382

Attach any of these to your answer, or reject them all.
```

[DEVIATION A-3] **`README.md:380-382`, not `:290-293`.** #3 Correction 1 and #12 §2.1 both cite `README.md:290-293` for the idempotent/offline sentence. Measured at HEAD, `:290-293` is the template-resolution stack table; the sentence is at `:376-382`, and the quoted clauses at `:380` and `:382`. `AGENTS.md:213` is exact. Sixth stale citation in this effort's record.

> [FIXTURE] **Reject 1 — that is what I just typed. Attach 2.**

[DEFECT A-4] **The record does not say what a rejectable candidate *is*.** #12 §2.1 prints the candidates as **verbatim repo quotes with citations**. #7's W3 requires the principle body to be "the accepted rule strings, verbatim, joined." Attaching candidate 2 therefore writes either

- the quote, which is descriptive, carries no modal, and contributes **zero obligations** — a sentence that gates nothing inside the one principle #5 built to gate the most; or
- a rule drafted from the quote, which is not the string that was on screen, so S2's containment test fails against it on every area-1 run.

Nothing in #3 §3, #7, #10 or #12 chooses. **This transcript takes the second reading and shows the drafted rule at the moment of attachment**, because the first makes area 1 produce a principle with no obligations on the very run where the user engaged most:

```
Attached, as a rule:
  "A plan MUST NOT introduce a file write outside the project root, and MUST NOT
   make a consume or author command depend on network access."
  - drafted from README.md:380-382, which you attached.

Step 2 of 9 - Technology and dependency boundaries
```

Wording fix in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8). Cost of getting it wrong: one of the eight principles silently carries no obligation, and #13's zero-obligation clause is the only thing that would ever notice.

**Accepted strings for principle I:** the user's typed rule (user-typed, quota-exempt, S2 log-only) plus the drafted attachment. **3 obligations.**

---

## Step 2 of 9 — Technology and dependency boundaries

```
Step 2 of 9 - Technology and dependency boundaries

Proposed rule
  The runtime dependency set is: click, json5, packaging, pathspec,
  platformdirs, pyyaml, readchar, rich, typer.
  A plan MUST NOT add a runtime dependency outside that set. A plan that must
  add one MUST record it in Complexity Tracking, naming the standard-library
  API or the already-listed package it rejected. Runtime versions are declared
  as floors, never as pins.

What this checks, on every future plan
  "Does this plan add a runtime dependency outside the set named in this
   constitution, and if so does Complexity Tracking name the rejected
   alternative?"

Where it came from
  pyproject.toml:7-17 - the [project] dependencies table, names only, no
  version specifiers. Declared set, exhaustive by construction.

Runtime dependency set (9)
   1 click          4 pathspec       7 readchar
   2 json5          5 platformdirs   8 rich
   3 packaging      6 pyyaml         9 typer

Accept, drop by number, add members, or replace the rule?
```

No near-miss band: a **declared** set has no threshold, so the label line is omitted rather than printed empty (#12 §1.9).

> [FIXTURE] **Accept.**

One word. **3 obligations.** Collapse run: 1.

---

## Step 3 of 9 — Architecture and structural limits

```
Step 3 of 9 - Architecture and structural limits

Proposed rule
  The distribution-artifact ceiling is 1.
  A plan MUST NOT raise the number of distribution artifacts declared in the
  build manifest above 1. A plan MUST NOT introduce an interface, abstract
  base class, or protocol with a single planned implementation, unless it is a
  declared extension point registered in the project's integration registry.

What this checks, on every future plan
  "Does this plan's Structure Decision add a distribution artifact beyond the
   declared ceiling, or add a single-implementation abstraction that is not a
   registered extension point?"

Where it came from
  pyproject.toml:26 - [tool.hatch.build.targets.wheel], the only declared
  build target.
  src/specify_cli/integrations/base.py - base class plus INTEGRATION_REGISTRY,
  the registered extension point the carve-out names.

Counted
  pyproject.toml - 1 declared build target ([tool.hatch.build.targets.wheel]).
  Not counted: 7 independently versioned units force-included into that wheel.

Accept, change the number, or replace the rule?
```

The `Not counted` sentence is #12 §1.8's mandatory second half, and it is load-bearing here: the seven force-included units (`extensions/git`, `extensions/agent-context`, `extensions/assess`, `extensions/bug`, `workflows/speckit`, `presets/lean`, `presets/constitution-sync`) are exactly the ones #10 Correction 1 caught two tickets hand-counting as nine.

> [FIXTURE] **Replace: every new module MUST begin with `from __future__ import annotations` and use modern type syntax — `dict[str, Any]`, `str | None` — never `Dict`, `List` or `Optional`.**

```
Still on this step - that one is already enforced outside this document, so it
does not go in: enable ruff rules UP, FA, I and N in pyproject.toml. It is on
the routing table.

Accept, change the number, or replace the rule?
```

This is the rule the ticket names. It is **not** an R1 refusal: measured, `[tool.ruff.lint]` at `pyproject.toml:82-90` is exactly `extend-select = ["S602", "S604", "S605"]`, and `.github/workflows/test.yml:27` runs `uvx ruff@0.15.0 check src tests`. `UP`, `FA`, `I` and `N` are all off, so nothing enforces it today. It is refused by the **style route-out**, which runs *before* T1/T2/T3, and the route-out is constructive — it names the rules to switch on. Route-out verdicts are not overridable (#3 §6), so the step re-prints its prompt and the area stays open.

> [FIXTURE] **Accept.**

**2 obligations.**

[DEFECT A-5] **#2 §4 does not say whether a route-out verdict breaks the satisficing-collapse run.** It lists push-backs, disambiguations and the negative-scope follow-up as transparent, and a route-out verdict is in none of the three lists — not transparent, not an edit, not an answer. The step's final answer is a bare `Accept.`, which by the letter feeds the run. This transcript rules that it **breaks** the run: the user proposed replacement prose, which is engagement, and #2 §4's own justification for the detector is that "satisficing is a current state". It changes nothing in arm A, because step 4 breaks the run anyway. It would change a run where steps 3, 5 and 6 were bare accepts.

---

## Step 4 of 9 — Surface contract

```
Step 4 of 9 - Surface contract

Proposed rule
  The measured command-verb grammar is: add, disable, enable, info, list,
  remove, search, set-priority, update. The measured recurring flag grammar
  is: --author, --commands-dir, --dev, --force, --from, --integration,
  --integration-options, --json, --model, --name, --offline, --output-format,
  --priority, --script, --skills, --tag.
  A plan MUST NOT introduce, in its Technical Context or its tasks, a command
  verb, flag name, endpoint name, or output shape outside those sets. A plan
  MUST NOT remove or rename a published verb, flag, endpoint, field, or exit
  code without naming the replacement. A plan that changes user-visible
  behavior MUST name the user-facing document it updates in the same change.

What this checks, on every future plan
  "Does this plan add a verb, flag, endpoint or output shape outside the named
   grammar; remove a published one without naming its replacement; or change
   user-visible behavior with no documentation task?"

Where it came from
  src/**/*.py - 84 command declarations across 12 files; 61 distinct flags.
  Members declared at 3 or more sites.

Set 1 - command verbs (9)
   1 add        4 info      7 search
   2 disable    5 list      8 set-priority
   3 enable     6 remove    9 update
  Below the threshold, not included: build, check(2), init(2), install(2),
  resolve(2), resume, run(2), scaffold, status(2), switch, uninstall,
  upgrade(2), use, validate, version

Set 2 - recurring flags (16)
   1 --author            7 --integration-options   13 --priority
   2 --commands-dir      8 --json                  14 --script
   3 --dev               9 --model                 15 --skills
   4 --force            10 --name                  16 --tag
   5 --from             11 --offline
   6 --integration      12 --output-format
  Below the threshold, 45 more, including: --here(2), --all(2), --dry-run(1),
  --format(1), --debug(1)

Accept, drop by number, add members, or replace the rule?
```

[DEVIATION A-6] **`12 files`, not `13`.** #10 §6's provenance line says "84 command declarations across 13 files". Measured at HEAD, `.command(` appears in **12** files under `src/`. The 84 sites and the 61 flags are exact.

[DEVIATION A-7] **`check(2)` and `init(2)`, not unannotated.** #10 §2b's near-miss band lists `check` and `init` without a count, meaning one site each. Both are at two: `init` at `commands/bundle/__init__.py:592` (named) and `commands/init.py:257` (defaulted); `check` at `_version.py:1150` (named) and `__init__.py:390` (defaulted). #10's Correction 6 warned that a single-pattern search misses the defaulted form; the two forms were then counted as separate populations rather than merged per member. Neither member crosses the threshold, so the set is unaffected.

> [FIXTURE] **Add `install`.**

```
Added. install is at 2 sites, below the threshold of 3, and you are adding it
knowingly.

  The measured command-verb grammar is: add, disable, enable, info, install,
  list, remove, search, set-priority, update.

Step 5 of 9 - Irreversible and destructive operations
```

**This is claim 1 landing positively, and it is the transcript's most load-bearing observation.** `drop by number` was not used; `add members` was, and it was used because the near-miss band put `install(2)` on screen. That is exactly the member #10 §2b predicted: the hand-written `.specify/memory/constitution.md:102-104` names `install`, the threshold-3 measurement excludes it, and the band is what recovers it. Measured, not invented — see [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

A member edit is not user-typed (#10 §8), so S2 stays live over the set sentence. It breaks the collapse run (#10 §8). Collapse run: 0.

**3 obligations.**

---

## Step 5 of 9 — Irreversible and destructive operations

```
Step 5 of 9 - Irreversible and destructive operations

Proposed rule
  A plan that adds a file removal, an in-place overwrite of user-supplied
  content, or an uninstall path MUST name, in Technical Context, the hash
  check that gates it and its no-clobber behaviour for user-modified files. A
  plan MUST NOT introduce a destructive action that runs without a preview of
  what it will change. A plan MUST NOT plan a file write or removal that
  resolves outside the project root, and a symlink that escapes the project
  root MUST be refused.

What this checks, on every future plan
  "Does this plan add a migration or a destructive operation, and if so does
   Technical Context name the reverse path and the idempotency guarantee?"

Where it came from
  src/specify_cli/**/*.py - 36 shutil.rmtree and 76 unlink( sites across 20
  modules; 62 sha256 sites.
  No migrations directory and no schema migration anywhere in the tree, so the
  reverse-migration clause was not offered.

Accept, edit, or replace?
```

The migration half of #5's exemplar is dropped because the repo has none: offering it would be a rule about behaviour the project does not have, which S4 would then flag on the closing run. The quota for this area is 4 and the rule uses 4.

> [FIXTURE] **Accept.**

**4 obligations.** Collapse run: 1.

---

## Step 6 of 9 — Testing and verification

```
Step 6 of 9 - Testing and verification

Proposed rule
  A plan MUST state, in Technical Context, the automated evidence for each
  behavior change it introduces. A functional requirement whose only stated
  evidence is manual verification MUST NOT be planned.

What this checks, on every future plan
  "Does Technical Context name automated evidence for every behavior change in
   this plan?"

Where it came from
  tests/ - 143 test modules, 4,581 test functions.
  .github/workflows/test.yml:55 - uv run pytest, on every push.

Accept, edit, or replace?
```

> [FIXTURE] **Edit: add "Slash-command-affecting changes MUST be manually exercised through a coding agent and the results reported in the PR."**

That sentence is lifted from today's `.specify/memory/constitution.md:189-190`, so the push-back is being tested against real ratified content rather than a straw rule.

```
Still on this step - no gate can check that as written.

  What you typed  "Slash-command-affecting changes MUST be manually exercised
                  through a coding agent and the results reported in the PR."
  Why             It names no artifact that answers the checkbox. A PR
                  description is not a plan section, not a source file, and not
                  a named person at a named moment.
  A checkable version
                  "A plan that changes a command template under
                   templates/commands/ MUST name, in Technical Context, the
                   agent it will be exercised through and the task that records
                   the result."

Take the rewrite, or keep yours? Keeping yours costs no decision, and the
routing table will record that no command can observe it.
```

> [FIXTURE] **Take the rewrite.**

**3 obligations**, quota met. Collapse run: 0 (an edit breaks it).

The rewrite is the design working: the same intent, moved from a PR body onto `plan.md`'s Technical Context, which `/plan` writes and `/analyze` audits. It is also the one accepted rule in this document naming behaviour the scan found no evidence of, which S4 catches at the end.

---

## Step 7 of 9 — Failure semantics and operating envelope

```
Step 7 of 9 - Failure semantics and operating envelope

Proposed rule
  A plan that adds an operation which can fail at runtime MUST state, in
  Technical Context, what the user sees on failure and whether the operation
  retries under a bounded attempt count, degrades to a named fallback, or
  aborts with a non-zero exit status. An unbounded retry MUST NOT be planned.

What this checks, on every future plan
  "Does this plan add an operation that can fail at runtime, and if so does
   Technical Context name what the user sees and how the operation bounds its
   retries?"

Where it came from
  src/specify_cli/**/*.py - 29 subprocess.run sites across 10 modules; 40
  write_text and 18 write_bytes sites outside a temp directory.

Accept, edit, or replace?
```

> [FIXTURE] **Accept.**

**2 obligations**, quota 2 for a fired conditional. Collapse run: 1.

---

## Step 8 of 9 — Supported platform and runtime matrix

```
Step 8 of 9 - Supported platform and runtime matrix

Proposed rule
  The supported set is Linux, macOS and Windows on Python 3.11 or later.
  A plan MUST state its Target Platform and minimum runtime in Technical
  Context, and MUST NOT declare a runtime or platform outside that set without
  adding the matching CI matrix cell in the same change.

What this checks, on every future plan
  "Does this plan state a Target Platform and minimum runtime, and does it stay
   inside the supported set unless it adds the matching CI matrix cell in the
   same change?"

Where it came from
  .github/workflows/test.yml:33-34 - matrix os: ubuntu-latest, windows-latest,
  macos-latest; python-version: "3.13", "3.14".
  pyproject.toml:6 - requires-python = ">=3.11", taken as a floor per R2.

Accept, edit, or replace?
```

[DEFECT A-8] **Nothing in the record says which source supplies the runtime version.** #5's exemplar needs an OS list *and* a runtime floor. #10 §3a resolves the trigger from the CI matrix; R2 requires a version to be a floor, never a pin; and here the two sources disagree — CI tests 3.13 and 3.14, the manifest declares `>=3.11`. This transcript takes the OS list from the CI matrix and the floor from the manifest, and marks it. The consequence of choosing the other way is not cosmetic: `Python 3.13 or later` would be a pin dressed as a floor, and it is exactly the row that went stale in today's document (`.specify/memory/constitution.md:171-172` claims `Python 3.11-3.13`).

**What is genuinely good here, and worth reporting as a design win:** the disagreement is *on screen*, in the provenance, at the moment the user confirms. Today's document carries the same disagreement invisibly and nothing surfaced it for two months.

> [FIXTURE] **Accept.**

**2 obligations.** Collapse run: 2. The walk ends before it reaches 3, so the collapse never arms — as expected, since two of the eight area steps were edits.

---

## Step 9 of 9 — Governance

```
Step 9 of 9 - Governance

Governance is filled, not asked. It is not a principle, and no plan gates on it.

  Version        1.0.0
  Ratified       2026-08-26
  Last amended   2026-08-26
  Amendments     approved by the project maintainers

One field here is not something the repo can tell me: who approves an amendment
to this constitution.

Name the approver, or accept?
```

`Carried here` does not print: no candidate this run was routed to `## Governance`. The style route-out at step 3 went to `pyproject.toml`, and R1's Governance line is reserved for restating a gate that already exists — which is precisely what ruff does not do for that rule here.

> [FIXTURE] **Accept.**

---

## Self-review

```
Self-review - no changes. The document is exactly the rules you accepted.

Measured - 8 principles, 22 obligations, 3,646 characters of body,
4,003 characters whole file (Sync Impact Report: 355).
```

Two act, three measure, and the acting half is a no-op — #7 Part 1's predicted shape on a well-formed run. What the three measuring checks found:

- **S2** — principle I contains user-typed clauses, so its body is log-only. One `Left verbatim` row.
- **S4** — one accepted rule names behaviour the scan found no evidence of: the step-6 rewrite. One `No evidence found` row.
- **S5** — principle I and principle V both forbid a file write resolving outside the project root. One `Overlap` row.

[FINDING A-9] **S5 fires on this run, and it fires structurally rather than by accident.** #5 gives negative scope an "exclusive claim" over "we do not add X" rules precisely so it will not collide with the dependency area — but the collision that actually happened is with **irreversible operations**, through a candidate the *scan* proposed and the user attached at step 1. The exclusive claim is stated against the wrong neighbour. S5 catches it, log-only, which is the settled behaviour and the right one; but the overlap is a predictable product of the walk order rather than a user error, and #5's clause implies it should not arise.

---

## Routing table

```
Routing table

  Routed out - 3 candidates that belong somewhere else
    from __future__ import annotations in every new module; modern type syntax
    over Dict/List/Optional
      -> ruff, by enabling rules UP, FA, I and N in pyproject.toml
    "Slash-command-affecting changes must be manually exercised through a
    coding agent and the results reported in the PR"
      -> rewritten and admitted as VI's third clause; the PR-body half goes to
         AGENTS.md, because no command in the pipeline reads a PR description
    The reverse-migration clause of the irreversible-operations rule
      -> not offered; this repo declares no schema migration

  Plan-time gate: present. 6 of 8 principles are plan-visible and are gated by
  /speckit.plan's Constitution Check.
```

`Weak anchors` does not print: no admitted rule is anchored only on `data-model.md`, `contracts/` or `## Complexity Tracking`. Principle II's second clause names Complexity Tracking, but its first clause is answered from Technical Context, so the principle has a strong anchor.

The tier split, per #5's R3 method: **6 plan-visible** (I, II, III, IV, VI, VIII — all answerable from `plan.md` Technical Context, Structure Decision or tasks), **2 converge-visible** (V and VII, which need feature-scoped source to check the runtime behaviour they name). Loud gate sees 6 of 8, against 22 of 46 on today's document.

---

## Deferred / assumed

```
Deferred / assumed - 3 rows. Each says what was assumed and what to say to change it.

ID   Kind              Where                   Default applied                                          Why                                                   To change it, say
DA1  Left verbatim     I. Negative scope       Written exactly as you typed it. The self-review          A clause here is text you typed, and fidelity to      "DA1: <your rewrite>"
                                               changed nothing in it.                                    your text is verbatim.
DA2  No evidence found VI. Testing and         Written as accepted. The scan found no code doing this.   Searched src/ and .github/workflows/ for a recorded   "DA2: drop it"
                       verification                                                                      agent-exercise step; found none.
DA3  Overlap           I. Negative scope /     Both were written. Neither was merged.                    They share the obligation "MUST NOT ... outside the   "DA3: drop V"
                       V. Irreversible and                                                               project root".
                       destructive operations
```

No row for the style route-out (that is the routing table), none for the push-back the user accepted (nothing was assumed), none for a quota trim (draft-time, #7), none for the unfired conditionals (an unfired trigger produces no row).

---

## The correction round

```
That is your constitution, written to .specify/memory/constitution.md.

One correction round, and it costs no decision. Change anything above - a rule,
a principle name, the Governance block - or reverse any row by its id, like
"DA3: put it back". Say "done", or say nothing more, and this run is finished.
After that, changing the document is a fresh /speckit.constitution run, which
opens in amendment mode.
```

> [FIXTURE] **done**

```
Applied. Version 1.0.0, ratified 2026-08-26. Anything further is a fresh
/speckit.constitution run, which will open in amendment mode.
```

[DEVIATION A-10] `Applied.` is #12 §3.9's post-correction line, printed here after a no-op correction round. #12 does not say what prints when the user corrects nothing; the alternative — printing nothing — would make the run end in silence, which #7's own no-op rationale argues against everywhere else.

---

## The document this run produced

See [output-a-constitution.md](output-a-constitution.md) — the file as written, in full.

**8 principles, 22 obligations, 3,646 characters of body, 4,003 whole file.**

Against `.specify/memory/constitution.md` as it stands today: **5 principles, 46 obligations, 11,699 characters of body, 13,213 whole file.** The comparison is [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

---

## Variant A' — the batch summary, and why arm A cannot reach it

The satisficing collapse needs three consecutive accept-without-edit answers on area steps, live at the moment the first conditional step would begin, with at least two fired conditionals still unasked. Arm A never gets there: step 3 attempted a replacement, step 4 added a set member, step 6 edited. The longest live run is 2.

That is not a defect. It is the mechanism working: a user who reviews a 16-item set has demonstrably not collapsed, and #10 §8 says so. But it means **the batch summary is unreachable on the arm the ticket scores**, and #2 Part 2 assigned its legibility to this ticket. So the variant below is a marked what-if: the same fixture repo, with steps 3, 5 and 6 answered `Accept.` unchanged and step 4 accepted too. The run is then still 9 decisions, the collapse arms when step 7 would begin, and one conditional remains — which is **below #2 §4's floor of two**, so it still does not fire.

**To fire it at all this fixture needs a third conditional**, which only DEFECT A-2's other reading supplies. So:

> **Variant A'' premise:** trigger 4 is read as firing (the `models/` path hit is taken at face value). Announced count becomes 10; the walk reaches steps 7, 8, 9 as failure semantics, platform matrix, data lifecycle; steps 3 through 6 are all bare accepts.

```
Steps 7 to 9 of 10 - Failure semantics, Supported platform and runtime matrix,
Data lifecycle

You accepted the last three proposed rules unchanged, so the rest of the walk is
one block instead of three steps. It still costs three decisions - what this
saves is round-trips, not decisions.

  Failure semantics and operating envelope
    Rule        A plan that adds an operation which can fail at runtime MUST
                state, in Technical Context, what the user sees on failure and
                whether the operation retries under a bounded attempt count,
                degrades to a named fallback, or aborts with a non-zero exit
                status. An unbounded retry MUST NOT be planned.
    Where from  src/specify_cli/**/*.py - 29 subprocess.run sites across 10
                modules; 40 write_text and 18 write_bytes sites outside a temp
                directory.
  Supported platform and runtime matrix
    Rule        The supported set is Linux, macOS and Windows on Python 3.11 or
                later. A plan MUST state its Target Platform and minimum runtime
                in Technical Context, and MUST NOT declare a runtime or platform
                outside that set without adding the matching CI matrix cell in
                the same change.
    Where from  .github/workflows/test.yml:33-34 - 3 OSes, 2 runtimes;
                pyproject.toml:6 - requires-python = ">=3.11".
  Data lifecycle
    Rule        A plan that adds a store of user-supplied data MUST declare its
                retention period and its deletion mechanism in plan.md prose. A
                task MUST NOT seed a non-production environment, test fixture, or
                demo dataset from production data.
    Where from  src/specify_cli/bundler/models/ - 3 model modules.

All three go in the Deferred/Assumed table either way, with the sentence that
reverses each one.

Accept all three, or name any you want to drop or change?
```

[FINDING A-11] **The batch summary needs a run this design makes hard to reach.** It needs three bare accepts *and* three fired conditionals *and* no engagement anywhere in steps 2 to 6 — while the two steps most likely to draw engagement, the inventory-bearing ones, sit at positions 2 and 4 by design. On the fixture the whole effort dogfooded against, it cannot fire at all: only two conditionals trigger, and #2 §4's floor of two remaining is unreachable with two. Whether the cost sentence reads as honesty is answered in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8); whether the mechanism earns its specification is a separate question and the answer is closer to no.

Note the third rule above: it is a **real** consequence of DEFECT A-2 being resolved the other way. A data-lifecycle principle about retention and production data, on a CLI that stores neither, written from three dataclass modules. That is the platitude failure #11's genericity screen exists to prevent, arriving through a trigger rather than through a menu.
