# Arm B — Greenfield authoring, against an empty repo

**THROWAWAY PROTOTYPE.** Not shipped content. Primary source for [issue #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

Read [README.md](README.md) first for the marking legend and the fixture premises.

---

## Fixture premise

An empty directory `tally/`, `git init`, then `specify init`, then `specify preset add constitution-interview`. Nothing else. No manifest, no source, no CI, no tests, no README.

After `specify init` the tree is not literally empty — it holds `.specify/templates/`, `.specify/scripts/`, the agent's `speckit.*` command files, and `.specify/memory/constitution.md`, which `ensure_constitution_from_template` (`src/specify_cli/commands/init.py:207-250`, called at `:931`) wrote from the core template. That file carries **20** `[ALL_CAPS]` tokens, measured at HEAD across `templates/constitution-template.md`'s 50 lines.

None of that scaffolding is a fact about the **project**. That distinction is what this arm turns on.

---

## The routing decision, and the authoring-mode line

The amendment predicate runs first. A1 passes — the file exists. A4 fails: 20 tokens. So the run routes to **authoring**, and #12 §5.5's tokens-present variant prints where the announced count would go.

```
There is a file at .specify/memory/constitution.md and it is still an unfilled
template: 20 placeholder tokens are in it, starting with [PROJECT_NAME] on line 1.
This run replaces it, which is what it is for.
```

Measured: `[PROJECT_NAME]` is on line 1 of the core template, and the token count is exactly 20. #12 §5.5's string instantiates without adjustment.

This is the first half of behavioural assertion 2 exercised, naturally, on the fixture the assertion names.

---

## The scan

```
Reading the build manifest and the workflow directory...
Resolving conditional triggers - 5 of 5.
Measuring the dependency set, the artifact ceiling and the surface grammar.
Reading source for negative scope, testing and irreversible operations - 0 files.
Scan complete - 0 files, 4 searches.
```

| Phase | Reads | Searches | What |
|---|---|---|---|
| 0 | 0 | 2 | no build manifest to read; repo-root listing; `.github/workflows/` listing returns nothing |
| 1 | 0 | 2 | no manifest, so triggers 1, 2 and 4 resolve to *not fired* off the manifest alone; one search for subprocess, one for a data store. Triggers 3 and 5 resolve off the empty workflow listing at zero cost |
| 2 | 0 | 0 | no manifest, no source: nothing to measure |
| 3 | 0 | 0 | no source to read |
| #9 probe | 0 | 1 | wait — see below |

[DEFECT B-1] **The gate probe's cost is not in the scan's four phases, so the progress line cannot report it.** #12 §3.2's format is four lines mapped to #10 §3's four phases, and `Scan complete — N files, M searches` is the fifth. #9's probe is a search that runs before the announcement and belongs to no phase. On arm A the discrepancy is invisible (8 and 12 are the totals either way). Here it is one search out of five, and the `Scan complete` line either under-reports by one or reports a number no progress line accounted for. Same root cause as arm A's ledger gap: the probe was priced against #10's budget without being placed in #10's allocation. Wording fix in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8). This transcript reports **5 searches** and accepts that line 2's `5 of 5` is about triggers, not searches.

**No conditional trigger fires.** No manifest, no source, no workflows, no data store.

---

## The announcement

```
Scan complete - 0 files, 5 searches.

This interview closes 7 decisions: 6 standard areas and 1 Governance
confirmation. Follow-up turns that clarify an answer you already gave never add
a decision.

Step 1 of 7 - Negative scope
```

The no-conditional form of the count sentence, per #2 §3 as instantiated by #12 §3.1.

---

## [FINDING B-2] Every one of the six area steps is cold. The greenfield run reaches no menu.

This is the arm's most consequential result and it contradicts what the settled record expects of it.

#12's handoff to this ticket states: *"the brownfield transcript cannot exercise the menu at all. The greenfield transcript is the only place the strictness ladder, the `drops:` delta and the menu stem are visible."* #11 Part 4 says the menu's main home is *"Greenfield, where the scan finds no code."*

Both are wrong on the fixture the ticket names, and the reason is #3 §2, which neither ticket contradicts:

> A menu "is offered **only** when the scan can name the repo fact that justifies offering it. With no such fact, the area falls through to shape C."

An empty repo has no facts. Not "few" — none. It has no manifest to declare a dependency group, no workflow directory to declare jobs, no source to declare a verb, no store to declare a lifecycle. So every area falls to shape C and the ladder is never read.

**#11's own worked example proves it.** Its menu opens on a repo where `.github/workflows/*.yml` declares three jobs — *"so this project already runs automated gates, it just runs none over its own behaviour."* That is an **early** repo, not an empty one.

The menu's real domain is a repo with facts but no practice: a project a few days old, or a brownfield area whose measured set cannot be produced, or a fired conditional whose trigger fact exists but whose convention is unobservable. All three are real. None of them is greenfield as this ticket defines it.

Consequence for this transcript: the ladder, the `drops:` delta and the two-clause menu stem would ship **unexercised** if arm B were left as the ticket specifies. Variant B′ below supplies the fixture that reaches them.

---

## The walk

Six cold steps, one frame, one prompt line. Shown in full for steps 1, 2 and 5; the rest are abbreviated to their variable parts, because the frame is identical by design and the point of the arm is what varies.

### Step 1 of 7 — Negative scope

```
Step 1 of 7 - Negative scope

What is this project deliberately not going to do - something a reasonable
engineer might propose, that this project would refuse?

Why it matters
  A rule that forbids nothing gates nothing, and what a project refuses is the
  part hardest to read off its code. You answer first; what the scan found
  comes after.

Answer in your own words, or say there is no rule here.
```

> [FIXTURE] **This stays a single-user local tool. A plan MUST NOT introduce a user account, a login, a session, or a server component.**

```
Still on negative scope - the scan found nothing to add to your answer.

Step 2 of 7 - Technology and dependency boundaries
```

The empty form of the mandatory follow-up (#12 §2.1). It prints, which is right — silence would read as the step not running — and it is the first of six turns in this arm that tell the user the tool found nothing.

### Step 2 of 7 — Technology and dependency boundaries

```
Step 2 of 7 - Technology and dependency boundaries

What is this project's runtime stack allowed to contain, and what does adding
to it require?

Why it matters
  Nothing in the plan pipeline reads your manifest - "pyproject" appears zero
  times under templates/commands/ - so a dependency rule that points at one
  cannot be checked. The set has to be written down here.

Answer in your own words, or say there is no rule here.
```

> [FIXTURE] **A plan MUST NOT add a runtime dependency without recording, in Complexity Tracking, the standard-library API it rejected.**

[FINDING B-3] **The cold step's `Why it matters` sentence is the strongest line in this arm, and it is the one that makes the answer possible.** It states a mechanical fact — `pyproject` appears zero times under `templates/commands/`, verified — and that fact is what tells a founder why a dependency rule has to enumerate rather than point. Compare it with what a pre-filled step would have shown here: nothing, because there is nothing. On greenfield, the `Why it matters` line carries the entire informational load of the interview.

### Step 3 of 7 — Architecture and structural limits

Cold question: `How many separately shipped pieces should this project have, and what would justify one more?`

> [FIXTURE] **I don't know. It is three days old.**

```
Noted. This area produces no principle, and nothing is written in its place.
It is row DA1 below, with the sentence that adds one later.

Step 4 of 7 - Surface contract
```

**No principle. Nothing invented.** This is the answer to the ticket's third question, arriving at the first opportunity: a cold step has no default, so "I don't know" produces silence rather than content. #2 §5 is what makes it so, and #13 proved the same thing at the limit.

### Step 4 of 7 — Surface contract

Cold question: `Which parts of this project's surface - command names, flags, endpoints, output shapes - are a promise you cannot quietly break?`

> [FIXTURE] **A plan MUST NOT introduce a command verb outside `init`, `run` and `status`, and MUST NOT change the shape of `--json` output without naming the replacement.**

The user supplied a set. It is user-typed, so it is quota-exempt, written verbatim, and log-only for every self-review check. Note what the design did **not** do: it did not offer a set, because #3 §2 forbids a canned one and there was nothing to measure. The set in this document exists because the founder typed it.

### Step 5 of 7 — Irreversible and destructive operations

```
Step 5 of 7 - Irreversible and destructive operations

What has to be true before this project deletes, overwrites, or migrates
something it cannot get back?

Why it matters
  These are the changes a plan describes in one line and a rollback cannot undo.

Answer in your own words, or say there is no rule here.
```

> [FIXTURE] **I don't know.**

```
Noted. This area produces no principle, and nothing is written in its place.
It is row DA2 below, with the sentence that adds one later.

Step 6 of 7 - Testing and verification
```

[FINDING B-4] **This is the area where the absence of a menu costs the most, and it is measurable.** Irreversible operations is the area #2 §2 calls the constitution's "killer items" and gives the largest obligation quota, 4. On a greenfield repo it produces nothing, because the founder has not yet met the problem and the tool has nothing to offer. A shipped ladder — one rung saying a destructive action names its dry-run and its idempotency guarantee — is *exactly* the content this step wants. It is unreachable, because a repo with no destructive operations gives the scan no fact to open a menu with. **The strictness ladder is most valuable precisely where #3 §2's gate refuses to open it.**

### Step 6 of 7 — Testing and verification

Cold question: `When this project changes how it behaves, what has to prove the change works?`

`Why it matters` names `tasks-template.md:12` — "Tests are OPTIONAL" — verified at HEAD.

> [FIXTURE] **A plan MUST state, in Technical Context, the automated test for each behavior change it introduces.**

### Step 7 of 7 — Governance

```
Step 7 of 7 - Governance

Governance is filled, not asked. It is not a principle, and no plan gates on it.

  Version        1.0.0
  Ratified       2026-08-26
  Last amended   2026-08-26
  Amendments     approved by the project maintainers

One field here is not something the repo can tell me: who approves an amendment
to this constitution.

Name the approver, or accept?
```

> [FIXTURE] **Me, until there is someone else.**

Written as the approver line. This is the one field #5 says is genuinely not inferable, and on a solo greenfield project it is the one field with a genuinely different answer from the pre-fill — which is the best available evidence that charging it a decision was right.

---

## Self-review

```
Self-review - no changes. The document is exactly the rules you accepted.

Measured - 4 principles, 5 obligations, 851 characters of body,
1,189 characters whole file (Sync Impact Report: 336).
```

The zero-obligation clause does **not** print: 5 is not 0.

S4 fires on every principle, because every principle came from a cold answer and has no scan evidence by definition. Its greenfield flood control collapses four rows to one, verbatim from #7:

> `No principle in this document has supporting repo evidence — the scan found no code.`

S2: all four bodies are entirely user-typed, so all four are log-only. #7 caps `Left verbatim` at one row per principle — **four rows**, on a document with four principles. [FINDING B-5] **On a fully cold run, `Left verbatim` degenerates into one row per principle and says nothing**: every row reads identically except the `Where` column, and the information content is already carried by S4's one-line collapse directly above it. #7 gave S4 flood control and did not give S2 the same. This transcript applies the same collapse and marks it:

> `4 principles were written exactly as you typed them; the self-review changed nothing in any of them.`

---

## Routing table

```
Routing table - nothing was routed out. Every candidate this run produced is in
the document.

  Plan-time gate: present. 4 of 4 principles are plan-visible and are gated by
  /speckit.plan's Constitution Check.
```

`4 of 4` is true and it is the highest ratio in any arm. It is also an artefact: a cold step can only produce a rule the user can state, and a rule a person states from memory is almost always one a plan can answer. The converge-visible tier needs source to observe, and there is no source.

---

## Deferred / assumed

```
Deferred / assumed - 3 rows. Each says what was assumed and what to say to change it.

ID   Kind             Where                          Default applied                     Why                                To change it, say
DA1  Area unanswered  Not asked - Architecture and   The area produces no principle.     You answered "I don't know" on a   "add a rule for architecture and
                      structural limits              Nothing was written in its place.   cold step, where no default        structural limits: <your rule>"
                                                                                         exists.
DA2  Area unanswered  Not asked - Irreversible and   The area produces no principle.     You answered "I don't know" on a   "add a rule for irreversible and
                      destructive operations         Nothing was written in its place.   cold step, where no default        destructive operations: <your rule>"
                                                                                         exists.
DA3  Left verbatim    Whole document                 4 principles were written exactly   Every clause in this document is   "DA3: <your rewrite>"
                                                     as you typed them; the self-review  text you typed, and fidelity to
                                                     changed nothing in any of them.     your text is verbatim.
```

[FINDING B-6] `Where` reads `Not asked — <area>` on DA1 and DA2, which is #12's own rule 4 for the column. It is **wrong here**: the areas *were* asked. They were asked and answered "I don't know". `Not asked` is the string #12 reserves for an area that never ran, and an `Area unanswered` row is by definition an area that ran. The right value is the interview location, `Step 3 — Architecture and structural limits`. Wording fix, no mechanism change.

---

## The document this run produced

See [output-b-constitution.md](output-b-constitution.md).

**4 principles, 5 obligations, 851 characters of body, 1,189 whole file.** Densely numbered I to IV in walk order with two areas skipped, per W2 — so the document's principle IV is the *testing* area, not the sixth area. Nothing anywhere in the file says which two are missing; the Deferred/Assumed table is the only record, and it is terminal output.

---

## Variant B′ — the early repo, where the menu actually lives

**Premise:** a three-day-old Python project. `pyproject.toml` declares `dependencies = ["httpx", "typer"]`. `.github/workflows/ci.yml` declares two jobs, `lint` and `build`. Six source modules under `src/tally/`. **No `tests/` directory, no test runner in any workflow, no test framework in any dependency group.**

This is #11's own worked-example fixture, made concrete. It reaches a menu at step 6, and it is the only fixture in this prototype that does.

The dependency step now pre-fills (a manifest declares two members), so the run is not uniformly cold. Trigger 1 fires (`httpx` is a network client). Announced count: **8 decisions** — 6 areas, 1 conditional, 1 Governance.

```
Step 6 of 8 - Testing and verification

Nothing to infer
  No tests/ directory; no test runner in .github/workflows/*.yml; no test
  framework in any dependency group.

Why you are being asked anyway
  .github/workflows/ci.yml declares 2 jobs, so this project already runs
  automated gates - it just runs none over its own behaviour.

Each option below forbids less than the one above it. None was found in your
repo, and none is selected.

  1  strictest
     A plan MUST state, in Technical Context, the automated evidence for each
     behavior change it introduces. A functional requirement whose only stated
     evidence is manual verification MUST NOT be planned.

  2  drops: a plan whose only stated evidence is manual verification
     A plan MUST state, in Technical Context, the automated evidence for each
     behavior change it introduces.

  none  This project adopts no testing rule.

What rung 1 checks, on every future plan
  "Does Technical Context name automated evidence for every behavior change in
   this plan?"

Pick a number, edit one, write your own, or pick none.
```

> [FIXTURE] **2**

[FINDING B-7] **The `drops:` delta is what makes the ladder read as a choice rather than a repetition, and it is doing the work on one line.** Read without it, rungs 1 and 2 are one sentence apart and the second looks like the first with a sentence lost — #11's own worry, *"the tool is offering me the same rule three times"*, in its two-rung form. Read with it, the second rung says what changes about every future plan: a plan whose only evidence is manual verification now passes. That is the sentence the one documented reviewer in the whole dossier needed and did not have (`docs/research/constitution-interview.md:648` — *"for my hobbyist app I am happy with manual verification"*). Judged in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

**Two things about this step that only a transcript shows:**

- **`none` is co-equal on screen and it does not read that way.** It sits at the same indent as the numbered rungs, per #11, but it is last, unnumbered, and it is the only option whose text is a sentence about the project rather than a rule. It reads as the escape it is supposed not to be. Not a defect — #11 chose the rendering deliberately — but the claim that it is co-equal is not what the screen shows.
- **Rung 1 is longer than rung 2 and printed first**, which is #11's anti-anchoring concern realised: the strictest option is also the most substantial-looking one. Whether that anchors as hard as #3 feared is not answerable from one fixture, and this transcript does not claim to answer it.

**The `nothing to infer` line is stronger than the menu.** It names three specific absences, each one a thing the scan looked for. A founder reading it learns what the tool checked — which is more information than the ladder itself carries.

---

## What arm B answers

**The greenfield path does not degenerate into invention.** It degenerates into **silence**, which is a different and much better failure. Every mechanism that could have invented is closed: a cold step offers nothing, a menu will not open without a repo fact, "I don't know" writes no placeholder, and the write contract emits only accepted strings. Two of six areas produced nothing and the document simply has four principles.

The cost is real and is the honest complaint: **on greenfield the interview is an interrogation, because there is nothing to confirm.** Six cold questions in a row, six times a founder generating from memory, and the tool contributing exactly one fact per step in the `Why it matters` line. #12's whole wording principle — *a confirmation, not an interrogation, one word from a user who agrees* — has nothing to confirm here. Not one of the seven decisions in arm B is answerable in one word. In arm A, six of nine are.
