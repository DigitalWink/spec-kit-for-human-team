# Arm C — The floor: every area answered "I don't know"

**THROWAWAY PROTOTYPE.** Not shipped content. Primary source for [issue #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

Assigned by [#13](https://github.com/DigitalWink/spec-kit-for-human-team/issues/13)'s handoff as *"the run where the closing artifacts carry the document's whole weight"*.

**Do not score the feature on this arm.** #13 is explicit and it is right: a zero-obligation document is trivially shorter than today's and gates nothing, so the comparison degenerates. Arm A decides. This arm is a robustness check.

---

## Fixture premise

Arm B's fixture, unchanged: empty `tally/`, `git init`, `specify init`, `specify preset add constitution-interview`. Every area answered *"I don't know"*; the Governance confirmation accepted.

[FINDING C-1] **The floor requires a greenfield fixture. It is not reachable on a brownfield one.** On this repo, "I don't know" on a *pre-filled* step means **accept the inferred rule, logged as assumed** (#2 §5). Arm A's fixture has eight area steps, seven of which pre-fill; answering "I don't know" to all of them produces a document with seven principles and 20-odd obligations, every one of them assumed — a maximally *full* document, not an empty one. It also feeds the collapse run on every step, so the walk shortens. The floor needs cold steps, and only greenfield supplies six in a row.

That is worth stating plainly because it inverts the intuition the ticket carries: **on a mature repo, the least engaged user gets the longest constitution, not the shortest.**

---

## The walk

The announcement, the scan and the routing are arm B's exactly — 7 decisions, 6 cold areas plus Governance, no menus, no conditionals. Only the answers differ.

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

> [FIXTURE] **I don't know.**

```
Still on negative scope - the scan found nothing to add to your answer.

Noted. This area produces no principle, and nothing is written in its place.
It is row DA1 below, with the sentence that adds one later.

Step 2 of 7 - Technology and dependency boundaries
```

Area 1 is the one step in the walk where the acknowledgement is **deferred until the follow-up closes** (#12 §2.1, on #2 §5's exception). The follow-up runs, finds nothing, and only then does the area produce no principle. Both lines print, in that order.

Steps 2 through 6 are identical in shape. Each prints its cold question, its `Why it matters` fact, its prompt line, and then:

```
Noted. This area produces no principle, and nothing is written in its place.
It is row DA<n> below, with the sentence that adds one later.
```

[FINDING C-2] **`nothing is written in its place` is the load-bearing phrase and it earns its place six times over.** Read once, it looks like reassurance. Read six times in a row, it is the only thing standing between this run and the user's reasonable assumption that a skipped area leaves a gap the tool will fill. It is also literally true and mechanically important: the alternative on disk is the `specify init` scaffold's 20 tokens, which `/plan` and `/analyze` have no carve-out for.

The repetition is the problem worth naming. Six identical acknowledgements differing only in a row id is the flood S4 has flood control for and this string does not. See [FINDING C-6].

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

> [FIXTURE] **Accept.**

[FINDING C-3] **Governance is the only decision in this run that produced anything, and that is the design working exactly as #2 §2 argued it would.** #2 placed Governance last and outside any collapse window so that "the one genuinely non-inferable field is asked on every run, including the most collapsed one". At the floor it is not merely asked — it is the *sole* content. The document exists as a ratified document because of it, and that is what routes the next run to amendment instead of to an overwrite.

---

## Self-review

```
Self-review - no changes. The document is exactly the rules you accepted.

Measured - 0 principles, 0 obligations, 207 characters of body,
537 characters whole file (Sync Impact Report: 328).

No MUST, MUST NOT or SHOULD reached the document, so there is nothing for a gate
to check: /speckit.plan, /speckit.analyze and /speckit.converge will each load
this file, extract an empty rule set, and report zero. The file is written and
complete - it carries all four required sections and its adoption date, so the
next run will open as an amendment. Every area that produced no principle is
listed in the Deferred/Assumed table below, with what to say to add one.
```

#12 §4.4's clause, verbatim, all three sentences, with the measured numbers substituted. The third sentence prints because there *are* such areas — six of them.

[FINDING C-4] **The clause reads as a measurement, not a verdict, and the mechanism is `The file is written and complete`.** That is #12's stated design intent and it survives contact with the page. Cover that sentence and the paragraph becomes a list of things the tool will not do for you; leave it in and the paragraph is a status report with a next step. The three moves #12 named — opening with what was measured, stating the outcome positively, ending on a remedy the user can type — are each doing visible work. This is the single clearest wording win in the whole prototype.

**S1** — all four clauses it verifies are checkable except W2, which goes vacuous. `## Core Principles` is emitted **empty**, with no marker sentence under it. On screen the document has a heading with nothing under it, which looks broken and is not; nothing in the flow explains it except the accounting clause four lines below. That is the right trade — #13 Part 2 argues it at length — but the transcript is where you can see how much weight the accounting clause is carrying.

**S4** — fires on **nothing**. #7's greenfield flood-control line (*"No principle in this document has supporting repo evidence"*) does **not** print, because there are zero accepted rules and therefore zero rows to collapse. [FINDING C-5] Arm B prints that line and arm C does not, so the run with *less* evidence says *less* about evidence. Correct by the letter of #7 and mildly odd on the page.

**S5** — nothing to overlap.

---

## Routing table

```
Routing table - nothing was routed out. Every candidate this run produced is in
the document.

  Plan-time gate: present. This document names no principles, so
  /speckit.plan's Constitution Check has nothing to gate.
```

#12 §4.4's zero-principle variant of #9's status line, `gate present` form. It reads better than `0 of 0 principles are plan-visible` would, which is what #13's handoff item 4 asked for and got.

---

## Deferred / assumed — the artifact carrying the whole run

```
Deferred / assumed - 6 rows. Each says what was assumed and what to say to change it.

ID   Kind             Where                                     Default applied                        Why                                To change it, say
DA1  Area unanswered  Step 1 - Negative scope                   The area produces no principle.        You answered "I don't know" on a   "add a rule for negative scope:
                                                                Nothing was written in its place.      cold step, where no default        <your rule>"
                                                                                                       exists.
DA2  Area unanswered  Step 2 - Technology and dependency        The area produces no principle.        (same)                             "add a rule for technology and
                      boundaries                                Nothing was written in its place.                                         dependency boundaries: <your rule>"
DA3  Area unanswered  Step 3 - Architecture and structural      The area produces no principle.        (same)                             "add a rule for architecture and
                      limits                                    Nothing was written in its place.                                         structural limits: <your rule>"
DA4  Area unanswered  Step 4 - Surface contract                 The area produces no principle.        (same)                             "add a rule for surface contract:
                                                                Nothing was written in its place.                                         <your rule>"
DA5  Area unanswered  Step 5 - Irreversible and destructive     The area produces no principle.        (same)                             "add a rule for irreversible and
                      operations                                Nothing was written in its place.                                         destructive operations: <your rule>"
DA6  Area unanswered  Step 6 - Testing and verification         The area produces no principle.        (same)                             "add a rule for testing and
                                                                Nothing was written in its place.                                         verification: <your rule>"
```

**Six rows, not nine.** [#12](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12)'s handoff says *"The Deferred/Assumed column 6 is the thing under test there, **nine rows** of it."* Derived: the floor needs cold steps, cold steps need greenfield, greenfield fires no conditional, so the run has six area steps and produces six rows. Nine would need three fired conditionals, which needs a repo with facts, which is not a floor run. Fifth carried count in this effort that does not survive derivation.

[FINDING C-6] **Column 6 works, and columns 4 and 5 do not survive six repetitions.** This is the judgement #13 assigned and the answer splits.

- **Column 6 is genuinely a remedy.** Every row hands the user a sentence they can type, naming their own area, inside the correction round that is still open. It is not bookkeeping. A user who reads DA5 and types `add a rule for irreversible and destructive operations: a plan that deletes user files MUST name its dry-run` gets a principle, at zero decisions, immediately. That affordance is real and it is the artifact's whole justification.
- **Columns 4 and 5 are byte-identical across all six rows.** "The area produces no principle. Nothing was written in its place." / "You answered 'I don't know' on a cold step, where no default exists." Six times. This is precisely the N-identical-rows failure #7's S4 flood control exists to prevent, arriving through a different row kind that has no flood control. The table's information content is entirely in columns 2, 3 and 6.

**The fix is not to collapse the rows.** Collapsing them would destroy column 6, which is per-area and is the point. The fix is to print columns 4 and 5 once, as a header sentence, and leave the rows as `ID | Where | To change it, say`. That is a rendering change to #7's fixed column set on one row kind, and it is a wording fix. Recorded in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8).

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

[FINDING C-7] **The opener's example reply is wrong for this run.** `"DA3: put it back"` reverses an *obligation dropped* row. Every row in this table is an `Area unanswered` row, whose column 6 is `"add a rule for ...: <your rule>"`. A user who follows the example literally types a sentence no row accepts. The opener is a fixed string in #12 §3.9 and its example is drawn from a row kind that cannot occur at the floor. Wording fix: make the example quote column 6 of the first row actually printed.

> [FIXTURE] **done**

---

## The document this run produced

See [output-c-constitution.md](output-c-constitution.md).

```
# tally Constitution

## Core Principles

## Governance

Amendments to this constitution are approved by the project maintainers.

**Version**: 1.0.0 | **Ratified**: 2026-08-26 | **Last Amended**: 2026-08-26
```

**0 principles, 0 obligations, 207 characters of body, 537 whole file.** All four anchors present, zero `[ALL_CAPS]` tokens, zero `TODO(`.

---

## The round trip — behavioural assertion 5, measured

Running #6's amendment predicate against the file this run just wrote:

| | | |
|---|---|---|
| A1 | file exists | pass |
| A2 | H1, `## Core Principles` (empty), `## Governance`, footer | pass |
| A3 | footer parses — `1.0.0`, `2026-08-26`, `2026-08-26` | pass |
| A4 | `\[[A-Z][A-Z0-9_]{3,}\]` matches | **0** — pass |
| A5 | `TODO(` | **0** — pass |
| | **routes to** | **AMENDMENT** |

The assertion holds. A second run opens as an amendment and does not overwrite. #13 §6.1's argument is confirmed against a real file rather than a described one: a count-condition on A2 would have routed this document to authoring and destroyed the Governance answer the user paid a charged decision for.

And the second run's opening is #12 §5.6's zero-principle variant, whose trigger is zero *principles* rather than zero obligations:

```
Found a ratified constitution - tally Constitution, v1.0.0, ratified 2026-08-26,
and it names no principles. It is a valid document; it just does not gate
anything yet.
Plan-time gate: present.

Amendment mode. To write a new one from scratch instead, remove
.specify/memory/constitution.md and run this again.

At most 5 decisions: this one, up to 3 areas you name, and one version-bump
confirmation. Follow-up turns that clarify an answer you already gave never add
a decision. If you still have nothing to add, say so and this run ends here
without writing anything.

Step 1 - What would you add?
```

---

## Does the floor read as an honest run or as a failed one?

**Honest — and one string does almost all of the work.** That is the judgement #13 assigned to this arm.

The run reads as failed if you stop at the document. A heading with nothing under it, a Governance line, a footer. What converts it is the accounting clause: it names what was measured, states the file is complete, says what the three consumers will do with it, and points at six rows that each carry the sentence that fixes one. The user finishes the run knowing the tool did what it said and knowing exactly how to get more.

**The residual risk is not tone, it is timing.** The three closing artifacts and the correction round arrive together in one screen. A user who has just answered "I don't know" six times is the least likely reader in the design to read a table carefully — and that table is the only remedy they will get, because the round closes after it. There is no mechanism that draws the disengaged user back to column 6, and by construction there cannot be one: no check may ask.

That is a real limit of the design and it is not fixable by wording. It is worth stating plainly rather than leaving as an implication of "the artifacts carry the weight".
