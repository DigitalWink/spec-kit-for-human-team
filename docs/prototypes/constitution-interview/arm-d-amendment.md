# Arm D — Amendment, over this repo's ratified constitution

**THROWAWAY PROTOTYPE.** Not shipped content. Primary source for [issue #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

Assigned by [#6](https://github.com/DigitalWink/spec-kit-for-human-team/issues/6)'s handoff, which grew this ticket's scope: *"There are two interviews to transcribe, not one."*

---

## Fixture premise

This repo at HEAD, spec-kit-initialized, `constitution-interview` installed, and `.specify/memory/constitution.md` **left in place**. This is the run the repo actually gets today — see [arm A](arm-a-brownfield.md#fixture-premise), where the same measurement shows arm A is the one that needs a premise.

The amendment predicate passes all five conditions (measured in arm A). The run opens in amendment mode.

---

## The scan, and the first announcement

Amendment mode spends **2 reads and 3 searches** (#6 Part 8). Derived here: 1 read of `.specify/memory/constitution.md` for the predicate and the pre-fills; 1 read of `pyproject.toml` plus 2 searches for the three measured sets; 1 search for #9's gate probe. That is 2 reads and 3 searches exactly — except that #10's surface-grammar measurement is three greps, not two (see [arm A](arm-a-brownfield.md#the-scan)), so the honest figure is **2 reads and 4 searches**, one over #6's stated budget, for the same reason arm A runs at 12 of 12.

```
Found a ratified constitution - Spec Kit Constitution, v1.0.0, ratified
2026-06-19, last amended 2026-06-19, 5 principles.
Plan-time gate: present.

Amendment mode. Nothing already in this document is rewritten unless you name
it. To write a new one from scratch instead, remove
.specify/memory/constitution.md and run this again.

At most 5 decisions: this one, up to 3 areas you name, and one version-bump
confirmation. Follow-up turns that clarify an answer you already gave never add
a decision. If nothing has changed, say so and this run ends here without
writing anything.

Step 1 - What changed?
```

#12 §5.1 verbatim, instantiated. Every fact in the header line is measured from the file: `Spec Kit Constitution` (`:34`), `1.0.0`, `2026-06-19`, `2026-06-19` (`:214`), 5 principles (`:44`, `:73`, `:99`, `:125`, `:148`).

[FINDING D-1] **The two-announcement shape reads as honest, and the reason is the verb, not the structure.** `At most 5 decisions` and, later, `This amendment closes 3 decisions` are different sentences making different claims — #12 §5.1 dropped `This amendment closes` from the ceiling block precisely so they could not be read as the same promise revised. On the page that works. What a user sees is a bound, then a count; not a number that moved. #6's worry — that it would read as the tool revising its own promise mid-run — does not materialise, and the fix that prevents it is one clause of wording.

---

## Step 1 — What changed?

```
Step 1 - What changed?

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

The five `###` headings are read off the single predicate read, verbatim, and they match `.specify/memory/constitution.md` byte for byte.

> [FIXTURE] **Testing. Principle II claims CI runs `ubuntu + windows × 3.11, 3.12, 3.13`. It has not for months.**

The maintainer is right, and it is measurable: `.github/workflows/test.yml:33-34` runs `os: [ubuntu-latest, windows-latest, macos-latest]` and `python-version: ["3.13", "3.14"]`. Three OSes, not two; 3.13 and 3.14, not 3.11 to 3.13. The document has been factually wrong since the matrix changed and nothing surfaced it.

---

## Step 1's mandatory follow-up — the set diff

```
Still on what changed - all three measured sets were measured again, and one moved.

  Runtime dependency set
    Named here    click, json5, packaging, pathspec, platformdirs, pyyaml,
                  readchar, rich, typer
    Measured now  no change
  Command-verb grammar
    Named here    add, disable, enable, info, install, list, remove, search,
                  set-priority, update
    Measured now  - install, now declared at only 2 sites
  Distribution-artifact ceiling
    Named here    (not stated)
    Measured now  1

None of this is a change you asked for, and rejecting all of it costs nothing.

Attach any of these to your answer, or reject them all.
```

> [FIXTURE] **Reject all. `install` is still a public alias and I want it named; the artifact count is not something I want to gate on yet.**

[FINDING D-2] **The set diff reads as drift detection, not as changing the subject — and the sentence that earns it is the price disclaimer.** #6 handed this ticket the worry that a user who came to change one thing is handed three they did not ask about, immediately after being told the run is short. On the page the worry does not land, and three things prevent it: the `Still` opener and the missing `Step` header say it is free; `None of this is a change you asked for, and rejecting all of it costs nothing` says it twice more, in the user's terms; and the whole block is three short rows the user can scan in one pass. Rejecting all of it is one word. #12 §5.3's wording does exactly what it was written to do.

[DEFECT D-3] **`Named here` is prose extraction, and on a hand-written document it is a judgment.** #10 §7 keeps provenance out of the file, so the amendment must re-measure and compare against "what the constitution currently names". On an interview-written document that is trivial: the set is a declarative sentence and the members are comma-separated. On **this** document it is not:

- The dependency set is a parenthetical inside a bullet — `.specify/memory/constitution.md:153-156`, `"(typer, click, rich, pyyaml, packaging, platformdirs, pathspec, json5, readchar)"` — inside a sentence about maintainer agreement.
- The verb grammar is backticked prose at `:102-104` — `` "the established verbs — `list`, `add`/`install`, `remove`, `search`, `info`, `update`, plus `enable`/`disable` and `set-priority`" `` — with two slash-joined pairs a parser has to split.
- The artifact ceiling is **not named at all**, so `Named here (not stated)` is a third state neither #10 nor #6 describes.

Extracting the first two is model judgment over prose, which #10 §2 forbids by name as a **declared measurement** rule — and the whole point of a declared measurement is that two runs agree. Two runs will agree about the *measured* side and may not agree about the *named* side.

**The set diff is only well-defined over sentences the interview itself wrote.** On any document it did not write — which is every document on its first amendment — the left-hand column is a guess. Wording fix in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8): print `Named here — could not be read from this document` rather than a guessed member list, and treat the diff as one-sided.

Note this is a real consequence of #10 §7's decision to keep provenance out of the file, taken on grounds that were correct for the write contract. The cost lands here, one run later, and no ticket priced it.

---

## The second announcement

```
This amendment closes 3 decisions: what changed, 1 area (testing and
verification), and the version bump. Same exclusions as above.

Step 2 of 3 - Testing and verification
```

Count 3, per #6 §2.2: step 1, one touched area, the bump. A count of 2 is unreachable.

---

## Step 2 of 3 — Testing and verification

```
Step 2 of 3 - Testing and verification

Proposed rule
  A plan MUST state, in Technical Context, the automated evidence for each
  behavior change it introduces. A functional requirement whose only stated
  evidence is manual verification MUST NOT be planned. A plan that adds an
  integration MUST name, in its tasks, the
  tests/integrations/test_integration_<key>.py module it adds.

What this checks, on every future plan
  "Does Technical Context name automated evidence for every behavior change in
   this plan?"

Where it came from
  .specify/memory/constitution.md:73-97 - the existing principle, rewritten to
  this interview's admission test and this area's quota of 3 obligations.
  tests/integrations/ - 143 test modules, 4,581 test functions; the parity
  invariant is the one clause of the existing principle a plan's tasks can
  answer.

Five clauses of the existing principle are not carried forward. They are on the
routing table below.

Accept, edit, or replace?
```

[DEFECT D-4] **A touched area does not "amend" its principle. It replaces it, and nothing in the record says so out loud.** #6 §2.1 says a touched area is *"pre-filled from the existing principle"*, and #6's own framing is that *"nothing already in this document is rewritten unless you name it"*. Both are true of the untouched areas and misleading about the touched one. What actually happens is that the existing principle is re-drafted through the full authoring machinery: the route-out table, T1/T2/T3, the obligation quota (3, unchanged per #6 §7.2), and W2's short-name default. The user named "testing" to correct one stale sentence and is shown a rule with **8 of the old principle's obligations gone**.

This is not wrong behaviour — the quota has to apply or a touched area becomes a way around it, and #6 §7.2 says so explicitly. It is a **disclosure gap**: the step's frame is the pre-filled frame, whose whole grammar says *here is a rule I inferred, accept it*, and the honest message here is *here is your principle, rebuilt to this document's rules, with five clauses removed*. The added sentence above (`Five clauses of the existing principle are not carried forward`) is this transcript's fix and is marked as a deviation — #12 has no string for it because no ticket noticed the case.

**And the rename is invisible.** `II. Test-Backed Change (NON-NEGOTIABLE)` becomes `II. Testing and verification`, because W2 defaults the short name to the area's name and #7's S1 forbids a model-invented title *"which would... smuggle in an emphatic `(NON-NEGOTIABLE)` label"*. Stripping the label is the settled behaviour and it is right. Nothing on this step tells the user their principle is being renamed.

> [FIXTURE] **Accept.**

---

## Step 3 of 3 — Version bump

```
Step 3 of 3 - Version bump

  1.0.0  ->  2.0.0   MAJOR

  What forced it
    II. Test-Backed Change (NON-NEGOTIABLE) - this obligation is in the
    document now and will not be after this run:
      "Every integration MUST be present in the registry, have a
       CommandRegistrar config entry where required, and ship a dedicated
       tests/integrations/test_integration_<key>.py"

  Removing an obligation is a redefinition, which is MAJOR. If you meant it as a
  clarification rather than a change of rule, this is PATCH.

Accept 2.0.0, or say MAJOR, MINOR or PATCH.
```

> [FIXTURE] **Accept 2.0.0.**

[FINDING D-5] **The bump confirmation reads as a real question here, and it is one — but only because the diff is large.** #6 §3.2 concedes the classification is mostly derivable and charges a decision anyway, and #12's fix is to show what was derived and ask only about the residue. On this run the residue is genuinely live: eight obligations vanish, and whether that is a redefinition or a cleanup of restated CI facts is exactly the judgment the repo cannot make. A maintainer who thinks "we never really enforced the matrix claim, this is a clarification" would say PATCH, and that is a defensible reading of the same diff.

**The ceremony risk is real on the other runs, and it is the common case.** On a PATCH run — text changed, clause set unchanged — the step prints a derivation, a tier, a rule, and no residue at all, then asks the user to accept the arithmetic. #12's middle-sentence variant for PATCH (`The text changed but no obligation was added or removed, which is PATCH`) has nothing to isolate, because the ambiguity #6 §3.2 charges the decision for only exists on an edit. **The step earns its decision on MAJOR, half-earns it on MINOR, and does not earn it on PATCH.** That is a live cost — one of a maximum of five decisions — and the fallback #6 names is not free either.

---

## The write

```
Self-review - no changes. The document is exactly the rules you accepted.

Measured - 5 principles, 41 obligations, 10,359 characters of body,
10,841 characters whole file (Sync Impact Report: 480).
```

The zero-obligation clause does not fire.

**S2 and the carried-forward state.** Four principles are carried forward. #6 §7.1 treats them as user-typed — log-only, never rewritten — and applies #7's S4 flood control rather than four `Left verbatim` rows:

> `4 principles carried forward unchanged and not reviewed: I, III, IV, V.`

That is the new Deferred/Assumed row kind, and on this run it is the **only** row in the table. The whole `## Security & Cross-Platform Constraints` section, the whole `## Development Workflow & Quality Gates` section and the preamble paragraph are untouched and **not even mentioned**, because they are not principles and the row kind counts principles. See [FINDING D-7].

### The footer, and behavioural assertions 3 and 4 — measured

| | Before | After |
|---|---|---|
| `**Version**` | `1.0.0` | `2.0.0` |
| `**Ratified**` | `2026-06-19` | `2026-06-19` |
| `**Last Amended**` | `2026-06-19` | `2026-08-26` |
| `SYNC IMPACT REPORT` blocks in file | 1 | **1** |

**Assertion 3 holds.** `**Ratified**` is byte-identical. #6 §3.3's replacement footer table does the job #7's S1 table would have got wrong: run unchanged, S1 would have written today into `**Ratified**` and `1.0.0` into `**Version**`, silently re-ratifying a two-month-old document and discarding its history.

**Assertion 4 holds.** The report is **replaced**, not prepended. The old block's 1,512 characters are gone; the new block is 480. Under core's `constitution.md:112` (*"prepend"*) the file would now carry 1,992 characters of comment and would grow by roughly 500 per amendment.

---

## Routing table

```
Routing table

  Routed out - 5 candidates that belong somewhere else
    "CI runs pytest across a matrix of ubuntu + windows x Python 3.11, 3.12 and
    3.13; changes must pass on every cell"
      -> .github/workflows/test.yml, which already declares the matrix; at most
         one line in ## Governance saying the gate must stay green
    "Test modules/classes/functions use the test_* / Test* naming the project
    configures, run under --strict-markers"
      -> pytest configuration in pyproject.toml, plus AGENTS.md
    "Platform-specific tests must be guarded (e.g. @requires_bash) rather than
    left to fail"
      -> AGENTS.md; answering it needs a repo-wide sweep, which nothing in the
         pipeline does
    "Changes touching file writes, path handling, or setup scripts must extend
    (never reduce) that coverage"
      -> nothing; "never reduce" needs a comparison against a previous revision,
         and nothing in the pipeline reads diffs
    "No test may make a real outbound network call; HTTP must be stubbed"
      -> AGENTS.md, or a pytest fixture that fails the suite on a live socket

  Plan-time gate: present. 3 of 5 principles are plan-visible and are gated by
  /speckit.plan's Constitution Check.
```

[FINDING D-6] **The routing table is the amendment's most valuable output, and the design does not treat it as one.** Five clauses of a ratified principle leave the document on this run, each with a named destination. That is a bigger, more actionable result than the one stale sentence the user came to fix. But it is printed *after* the write, in an artifact whose stated purpose (#5's R4) is preventing the user from feeling their rule was dropped — and the correction round that follows cannot put any of it back, because a route-out verdict is not overridable (#3 §6).

The user learns, after the fact and irreversibly, that five things they wrote are gone. On an authoring run that is fine: nothing was there before. On an amendment it is the loudest thing that happened, and it happens silently until the table prints.

---

## Deferred / assumed

```
Deferred / assumed - 1 row. Each says what was assumed and what to say to change it.

ID   Kind             Where           Default applied                          Why                            To change it, say
DA1  Carried forward  Whole document  4 principles carried forward unchanged   You did not name their areas,  run /speckit.constitution again
                                      and not reviewed: I, III, IV, V.         so this run did not open them. and name the area
```

No row for the rejected set-diff candidates (#6 Part 6: a rejected candidate produces no artifact). No `Beyond the ceiling` row: one area was named, not four.

---

## [FINDING D-7] Amendment cannot converge a legacy document — and this is the prototype's most consequential structural result

The amendment ran, correctly, and produced a **41-obligation** document. Arm A, authoring the same repo from scratch, produced **22**. The gap does not close by amending, and it cannot, for two independent reasons that compound:

**1. The 3-area ceiling.** At most three areas per run. Five principles plus the areas they map onto is more than three, so it takes at least two runs — which #13 already recorded, in the opposite direction.

**2. Thirteen of today's 46 obligations are unreachable by any amendment run, at any ceiling.** They live in `## Security & Cross-Platform Constraints` (5), `## Development Workflow & Quality Gates` (3) and `## Governance` (5). The amendment walks **areas**, and a `##` section is not an area. There is no step that opens them, no row kind that mentions them, and #6 §5.4 explicitly preserves them (*"W1's 'nothing else' is not applied retroactively"*), which is the right call for a document the flow did not write.

So the only route from today's document to arm A's document is the one #6 §1.3 documents as a convenience: **remove `.specify/memory/constitution.md` and re-run.** That is a migration path, and the record frames it as an escape hatch. It is worth naming as what it is, because it is the *only* way an existing project gets the benefit this feature is being built for — and it means the first thing a user of this preset must do, on any repo that already has a constitution, is delete it.

The `Carried forward` row does not say this. It names four principles and is silent about three sections and 13 obligations.

---

## The document this run produced

See [output-d-constitution.md](output-d-constitution.md).

**5 principles, 41 obligations, 10,359 characters of body, 10,841 whole file, exactly one Sync Impact Report block, `**Ratified**` unchanged.**

Against today's 46 obligations and 13,213 characters: the amendment removed 5 net obligations and about 2,400 characters, most of the character saving coming from replacing the 1,512-character Sync Impact Report with a 480-character one.

**That is the honest measure of what one amendment run buys on a legacy document: about 11% of the obligations, in one area, at three decisions.**
