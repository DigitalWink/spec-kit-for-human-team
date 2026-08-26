# Spec Kit

A toolkit for Spec-Driven Development: templates, command files, and the `specify` CLI that installs them for a chosen coding agent.

## Language

### The constitution and its consumers

**Constitution**:
The project's standing policy document at `.specify/memory/constitution.md`. Durable technical and process policy, not a values charter.
_Avoid_: charter, tenets, principles doc

**Consumer**:
A command template that loads the constitution at runtime. Ten reference it; four carry enforcement teeth.
_Avoid_: reader, client

**Gate**:
A point where a consumer can block on a constitution violation. `/plan` fills `## Constitution Check` and errors; `/analyze` rates a MUST conflict as CRITICAL. Preset-dependent: a preset that replaces `speckit.plan` removes the plan-time gate, and the section it leaves behind in `plan-template` is inert without the command instruction that fills it.

**Gate probe**:
The interview's one-search test for whether the plan-time gate exists on this stack: the installed `speckit.plan` command for this agent either carries a `Constitution Check` instruction or it does not. Three outcomes — present, absent, not determined — and the routing table names which. It asks nothing, blocks nothing, and changes no step. It is the scan's **last** spend, charged to its own row and its own progress line: it is the one measurement that changes no number, only a conditional paragraph, so it must never compete with trigger resolution. Settled in [issue #9](https://github.com/DigitalWink/spec-kit-for-human-team/issues/9); given its row in [issue #15](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).
_Avoid_: stack check, preset detection

**Vacuous pass**:
What every consumer does against a constitution carrying no obligations: it loads the file, extracts an empty rule set, finds nothing to conflict with, and reports zero. `/plan` fills a Constitution Check with no gates and does not error, `/analyze` rates nothing CRITICAL, `/converge` appends no task. Not an error and not a skip — `/converge`'s unfilled-template skip is a separate carve-out, and the two are indistinguishable from outside because both report nothing. It is what makes a document with no rules safe rather than merely small. Settled in [issue #13](https://github.com/DigitalWink/spec-kit-for-human-team/issues/13).
_Avoid_: passes, no-op, silently ignored

**Sync Impact Report**:
The HTML comment at the top of the constitution recording what the last run changed — version change, bump rationale, principles and sections added, modified and removed, and what was carried forward. Producer-side bookkeeping: no enforcing consumer reads it. Its shape comes from the command, not the template, which carries none. Exactly one block exists in the file at any time; a run replaces the one it found rather than prepending another.
_Avoid_: change log (that is the self-review's terminal artifact), impact report

**Tier**:
Which consumer can observe a violation of a given rule: **plan-visible** (answerable from `plan.md` or `tasks.md`), **converge-visible** (answerable from feature-scoped source), or **human-gate**. Decides admission; never written into the constitution itself.

### The constitution interview

**Interview**:
The bounded, inference-first flow that produces a constitution. Brownfield-primary: it infers from the repo, then asks the user to confirm or correct. It runs as a per-area walk.

**Area**:
One topic the interview probes. **Always-ask** areas run on every project; **conditional** areas run only when a repo signal fires their trigger.

**Per-area walk**:
The interview's shape: one area at a time, each closed by a single answer before the next appears. Not a whole-document draft reviewed in one pass. Settled in [issue #3](https://github.com/DigitalWink/spec-kit-for-human-team/issues/3).
_Avoid_: questionnaire, wizard

**Step shape**:
How one area is presented. **Pre-filled** — a rule inferred from the repo, shown with its gate question and its source. **Menu** — up to five candidate rules, nothing pre-selected. **Cold** — an open question, no candidates. Every shape costs one decision, and every shape ends in a prompt line. A step carries **either** a gate question or a *why it matters* sentence, never both and never neither: the two fill one slot, and the gate question is the stronger filler wherever a rule already exists.

**Gate question**:
The rule restated as a yes/no question about one specific implementation plan — what the rule will do to the user on every future plan. It is a by-product of the admission test's gate-restatement test, so it costs nothing to surface. Printed on a pre-filled step and for a menu's first rung; never written into the constitution, where the principle body is the accepted rule text alone.
_Avoid_: checkbox (that is what the plan fills), check, validation

**Prompt line**:
The sentence that closes a step. It names **every** legal reply and no illegal one, which is what makes an affordance real rather than decorative — a set inventory the user is never told they can `drop 7, 12` from is a blind accept. A closed set of seven: one per step shape, a wider one for each of the two inventory-bearing steps and the scalar step, plus Governance and the batch summary. Settled in [issue #12](https://github.com/DigitalWink/spec-kit-for-human-team/issues/12).
_Avoid_: prompt (that is the whole step), call to action

**Decision**:
The interview's budget unit: one area step, closed by one answer. A push-back, a disambiguation, and the correction round are not decisions.
_Avoid_: question, turn, prompt

**Uncharged turn**:
A turn the announced count does not cover — a push-back, a disambiguation, the negative-scope follow-up, the amendment set diff, the correction round. It is real typing, so the announcement names it, and it is marked on screen rather than only in the accounting: an uncharged turn prints **no `Step` header**, and one that continues the current step opens with the word **`Still`**. One learnable signal for "this is not a new decision".
_Avoid_: free turn, follow-up question, extra question

**Menu**:
The step shape used when the repo yields no rule for an area but can still name a fact that says which rules are worth offering. Nothing is pre-selected, and choosing none is a valid answer. A menu candidate never carries a measured set or number. What it offers is the area's **strictness ladder**. The naming fact is a hard gate, not a preference: with no fact the area falls through to a cold step, so a menu opens on an **early repo** and on neither of the two the design talks about most.

**Early repo**:
A project with facts but no practice — a manifest, or a workflow directory, or some source, and no convention in the area being asked about. It is the third state between greenfield and brownfield, and it is where the **menu** and the **strictness ladder** actually live. Named because the record had only two states and assigned the menu to the wrong one: a **greenfield** repo is not merely poor in facts, it has none, so it can name no fact to open a menu with and every area falls to a cold step. A mature **brownfield** repo pre-fills instead. Measured in [issue #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8): zero menu steps on an empty repo, zero on this one, and the ladder reachable only in between.
_Avoid_: new project, small repo, sparse repo

**Strictness ladder**:
The candidate rules a menu step offers for one area. Shipped with the preset, keyed to the area and never to a project type. Defined by nesting: **each rung forbids a strict subset of what the rung above forbids**, so the rungs are totally ordered and are shown strictest first. Rung 1 is the area's own settled rule; the ladder ends at the last rung that still forbids something, so it runs one to three rungs and is never padded. Settled in [issue #11](https://github.com/DigitalWink/spec-kit-for-human-team/issues/11).
_Avoid_: starter set, candidate library, project-type preset

**Rung**:
One candidate rule on a strictness ladder. A whole rule, not a clause to be combined with others — the user picks at most one. It passes the route-out table and the admission test at authoring time, before the preset ships, which is where its genericity is screened. Only the first rung carries a gate question; every rung below it carries a drop delta instead.

**Drop delta**:
What a lower rung stops forbidding, said in one line — the **gate** consequence of dropping a clause, not the text difference, which the nesting already puts on screen. It ships with the rung rather than being derived per run, because a delta worded differently on two runs is the model judgment the design refuses everywhere else. It is why a menu never prints three near-identical gate questions.
_Avoid_: diff, relaxation, weaker option

**Measured set**:
An enumeration produced from the repo and written inline into the constitution, because no consumer reads the file it came from. Three exist: the runtime dependency set, the surface verb and flag grammar, and the distribution-artifact ceiling — which is a scalar, not a list. A measured set is written as a **declarative sentence** and the obligation refers to it, so the set itself costs no obligations. Settled in [issue #10](https://github.com/DigitalWink/spec-kit-for-human-team/issues/10).
_Avoid_: inferred list, inventory

**Declared measurement**:
How a measured set is produced: a named source, a named extraction, and a stated threshold, deterministic and re-runnable. A **declared** set is copied from a manifest that already enumerates it. An **observed** set is derived by search from source that declares its members nowhere, at a stated recurrence threshold. Never model judgment over prose — two runs on the same repo must produce the same set.

**Near-miss band**:
The members an observed set's recurrence threshold excluded, shown at confirmation so the user can add them back. Labelled *below the threshold, not included*, never *rejected* — they were never candidates. It is what makes reviewing a long set a judgment about one boundary rather than a blind accept.

**Push-back**:
The interview's single challenge, at most once per area, when a rule the user typed fails the admission test. The user may insist; the rule is then written as they wrote it and the routing table records that no consumer can observe it. A route-out verdict, unlike an admission failure, is not overridable.

**Correction round**:
The one pass after the constitution is written, where the user can change anything in the assembled document. Further changes need a fresh run in amendment mode.

**Trigger**:
The repo signal that fires a conditional area. A product-surface fact, never the presence of a tool that would already enforce the rule.

**Admission test**:
The three tests that decide whether a candidate rule becomes a principle — **gate-restatement** (it restates as a plan checkbox), **forbidden-alternative** (it names the cheaper thing an engineer would otherwise do), and **evidence** (it names the artifact that answers the checkbox). Defined in [issue #5](https://github.com/DigitalWink/spec-kit-for-human-team/issues/5).
_Avoid_: quality check, filter

**Candidate rule**:
A statement offered for the constitution, before the admission test judges it. Always a **rule**, never a quotation of the repo: a candidate drawn from repo text is drafted into a rule first and shows the quote beneath it as provenance, because a descriptive sentence carries no obligation and cannot pass the admission test. What is on screen is what gets written.

**Route-out**:
Refusing a candidate rule and naming where it belongs instead — a linter config, `AGENTS.md`, an ADR, the spec, or nothing.
_Avoid_: reject, drop, exclude

**Routing table**:
The report the interview prints after writing the constitution: one line per routed-out candidate, saying what it was and where it went. Lives outside the constitution file. It also carries the gate probe's status line, so a stack with no plan-time gate is stated rather than left silent.

**Surface contract**:
The area covering the product's own external surface — command verbs, flag names, endpoint names, output shapes, exit codes, their stability, and the documentation that ships with a change.

**Amendment interview**:
The short "what changed?" flow that runs when a constitution already exists, instead of authoring one from scratch. The same per-area walk over a set the **user** names rather than one the taxonomy fixes: one cold "what changed?", at most three touched areas, then the bump confirmation. It costs one decision, or three to five. Settled in [issue #6](https://github.com/DigitalWink/spec-kit-for-human-team/issues/6).
_Avoid_: amendment mode, update flow, re-run

**Amendment predicate**:
The test that routes a run to the amendment interview instead of authoring, evaluated before the announcement: the live constitution carries all four mandatory anchors, its footer parses, and no `[ALL_CAPS]` token or `TODO(` survives. It reads the **document**, never the provenance sidecar and never a bare file-exists check — a materialized template and a ratified constitution are both files.
_Avoid_: existing-constitution check, file check

**Materialized scaffold**:
The constitution `specify init` writes when none exists: the resolved template copied to the live path, with a `.constitution-template.json` provenance sidecar. It is a file, not a constitution, and the amendment predicate routes it to authoring. Not the same as an authored document that happens to have been seeded by a preset.
_Avoid_: generated constitution, empty constitution, stub

**Zero-obligation constitution**:
A constitution the interview authored that carries no MUST, MUST NOT, or SHOULD clause — because every area produced no principle, or because the principles it holds state none. A legal, ratified document: it has all four anchors, a Governance block the user confirmed, and an adoption date, so the amendment predicate routes a later run to the amendment interview. **The discriminator against a materialized scaffold is tokens, not emptiness**: the scaffold carries twenty `[ALL_CAPS]` tokens and routes to authoring, this carries none and routes to amendment. Every consumer gives it a vacuous pass, and the accounting line says so.
_Avoid_: empty constitution (claimed by materialized scaffold), zero-principle constitution (a principle count hides the obligation count)

**Touched area**:
An area the amendment interview walks, because the user named it in step one or accepted it from the set diff. At most three per run; the rest go in the Deferred/Assumed table with a re-run pointer. Every other area is carried forward. Its principle is **replaced, not edited** — re-drafted through the route-out table, the admission test, the obligation quota and the short-name default — so the step says so out loud, names where the clauses that did not survive went, and discloses the rename. The amendment's promise that nothing is rewritten unless the user names it is a promise about every *other* area.

**Set diff**:
The re-measurement of all three measured sets against what the constitution currently names, shown as rejectable candidates in step one's follow-up. It costs zero decisions. It exists because provenance stays out of the document, so measuring again is the only way to see drift. It is two-sided only over sentences the interview itself wrote: what a document *names* is read from the shipped stem, never extracted from prose, so on any document the flow did not write — every document on its first amendment — the diff is **one-sided** and says which of the two reasons applies, could-not-be-read or not-stated.

**Carried-forward principle**:
A principle in an area the amendment did not touch. A third state beyond drafted and user-typed — ratified in an earlier run, so every self-review check treats it as user-typed and log-only. Reported as one summary line, never one row per principle.
_Avoid_: untouched principle, existing principle

**Bump confirmation**:
The amendment interview's last step, and its counterpart to the Governance confirmation. The flow classifies MAJOR / MINOR / PATCH mechanically from the obligation diff, and the user confirms. Charged as one decision, because the residue — whether an edit clarified a rule or redefined it — is a judgment about intent the repo cannot see.

**Announced count**:
The exact number of decisions the interview will cost, stated before the first question. Knowable because the scan completes first. Names what it excludes, because follow-up turns are real typing the number does not cover. Settled in [issue #2](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2). The **amendment interview** is the one flow that cannot know it in advance, because the count depends on the user's first answer — so it announces a ceiling, then the exact count once that answer closes. Both name the same exclusions. **An argument clause never changes it**: a count that moved with the arguments would depend on parsing prose, and the number is the interview's central promise.
_Avoid_: budget, cap, estimate

**Argument clause**:
One sentence or line of `$ARGUMENTS`, and the unit the command's arguments are attached by. A clause **pre-fills** the one area step whose **gate question** it answers — it never removes a step, never fires a conditional, and never changes the announced count. It is a pre-supplied answer the user still confirms in the walk, not a bypass of it, and it never routes back into core's `## Outline`. A clause that answers no gate question is not written from and not silently dropped: it lands one Deferred/Assumed row. A pre-fill drawn from the user's own arguments is not anchoring, so it is legal even on the two steps the design otherwise keeps cold. Settled in [issue #15](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15).
_Avoid_: argument, parameter, flag, prompt input

**Walk order**:
The fixed sequence of areas. Derived from one-way feeds — an area whose answer can invalidate an earlier rule runs before it — not from impact or attention.

**Satisficing collapse**:
The escape hatch. After three consecutive verbatim accepts, the fired conditionals not yet asked are presented as one batch summary instead of separate steps. It saves round-trips, never decisions: each covered area is still charged.

**Obligation**:
One MUST, MUST NOT, or SHOULD clause, counting each semicolon-separated sub-rule separately. The unit the document is sized in — a principle count hides it.
_Avoid_: rule, clause, instruction

**Obligation quota**:
The per-area ceiling on obligations: three for an always-ask area, four for irreversible operations, two for a fired conditional. Enforced at draft time, before the user sees the rule. A rule the user typed is exempt.

**Scan budget**:
What the pre-walk scan may spend: forty file reads and thirteen searches, one pass, divided across five rows whose numbers are derived from the procedures each row names rather than carried. Spending runs forward only: an earlier row's unspent budget rolls into a later one, never the reverse. Triggers are resolved first, because an unevaluated trigger changes the announced count; the **gate probe** is last, because it changes no number. Re-derived in [issue #15](https://github.com/DigitalWink/spec-kit-for-human-team/issues/15), which found the earlier twelve was the sum of a table with one row mispriced and one row missing.

**Deferred/Assumed table**:
The third closing artifact. Records content admitted but unanswered, assumed, or dropped for budget — as against the routing table, which records content **refused**, and the change log, which records content **changed**. Every default that left the document short of what the user might have wanted lands here, reversible in the correction round.

**Write contract**:
What the write step must emit: the template's mandatory structure, filled with the accepted rule text verbatim, plus the pipeline-fixed Governance block and footer, and nothing else. Owned by the preset that owns the write; the self-review verifies it. Settled in [issue #7](https://github.com/DigitalWink/spec-kit-for-human-team/issues/7). It governs what this flow may **emit**; it never defines what a valid constitution **is** — a ratified document may legally carry a preamble, extra sections and rationale paragraphs the contract would not have written.

**Output floor**:
The minimum the interview may produce. **There is none at the document level, and none can be added.** The floor is per-principle and already runs at draft time — it is the admission test — so a document of admitted rules is admissible by construction, and one principle is above the floor rather than near it. A document-level floor could only be reached by asking, refusing to write, or inventing content, all three forbidden; and no consumer could observe it, so it would fail the admission test's own evidence tier. The unit that would matter if one existed is **obligations, not principles**. Recorded so the question is closed rather than re-asked. Settled in [issue #13](https://github.com/DigitalWink/spec-kit-for-human-team/issues/13).
_Avoid_: minimum viable constitution (that is the shape W1 emits, not a gate), quality bar

**Self-review**:
The one pass over the assembled draft, before the single write. It checks the interview's own assembly against the write contract — it never re-judges a rule the user accepted. On a well-formed run it changes nothing.
_Avoid_: review, validation, QA pass

**Self-review check**:
One check inside that pass. Five exist: structural conformance, body fidelity, the accounting line, no-evidence rows, and overlap. Two act; three only measure. A self-review check never asks the user anything — it applies a stated default and logs it.
_Avoid_: quality check

**Fidelity**:
What the self-review restores. Fidelity is to the content the user accepted and to the template's structure. For text the user typed, fidelity is **verbatim**, so every check on it is log-only.

**User-typed**:
Any characters the user typed — including an edit of a pre-filled rule, and a single clause inside a body that also holds drafted clauses. Per clause, never per principle.

**Change log**:
The closing artifact that records what the self-review changed. Terminal output only, never written into the constitution. Prints even when nothing changed.
_Avoid_: report, diff, summary

### Packaging

**Preset**:
A bundled layer that overrides or wraps core command templates without modifying them. `presets/lean` and `presets/constitution-sync` are the working precedents. A layer declares one of four strategies per template — `replace` (the default), `prepend`, `append`, `wrap` — in its `strategy` field. `replaces:` is not a schema field and nothing reads it.

**Bundled preset**:
A preset shipped with Spec Kit rather than downloaded. Three mechanical facts, not one: a directory in `presets/<id>/`, an entry in `presets/catalog.json` with `"bundled": true`, and a `pyproject.toml` force-include line. The alternative is a community catalogue entry, which needs an external repository and a release archive and is discovery-only by default.

**`constitution-interview`**:
The preset this design ships as. One `wrap` override of `speckit.constitution`, bundled. Settled in [issue #4](https://github.com/DigitalWink/spec-kit-for-human-team/issues/4).
_Avoid_: guided-constitution, constitution-guided

**Block supersession**:
How the wrapper displaces core behavior: it names the core section it replaces and the sections that survive, rather than overriding core line by line. Line-level supersession depends on core's line numbers and rots on the next core edit; a named region does not. Scoped to **producing and writing** the constitution, so a lower `wrap` layer's section that is explicitly scoped to run after the write is preserved and runs post-write — without that scope, the wrapper swallows every layer beneath it. Sharpened in [issue #9](https://github.com/DigitalWink/spec-kit-for-human-team/issues/9).

**Integration**:
A supported coding agent, as a self-contained subpackage under `src/specify_cli/integrations/`.
_Avoid_: agent adapter, provider

**Extension**:
An opt-in add-on that owns behavior the CLI deliberately does not, such as `agent-context` owning the managed section of `CLAUDE.md` and `AGENTS.md`.

### Testing the interview

**Prompt-text invariant**:
An automated assertion that the shipped wrapper **states** a settled rule — a required string, a required structure, or an arithmetic relation between numbers printed in its own tables. It proves a decision survived the next edit of a several-hundred-line markdown command file. It proves nothing about what an agent does with it. `tests/test_presets.py`'s existing `test_wrapper_uses_core_template_and_propagates` is one. Named so it cannot be mistaken for a behavioral test. Settled in [issue #14](https://github.com/DigitalWink/spec-kit-for-human-team/issues/14).
_Avoid_: behavioral test, prompt test

**Fixture-validity assertion**:
An automated assertion that a tree the design **measured against** still has the property that made it a fixture. It never runs the interview. Example: this repo's announced count of 9 decisions holds only while `src/specify_cli/bundler/models/` contains no ORM and the manifest declares no database client. A prompt-text invariant fails when the design is edited; a fixture-validity assertion fails when the repo is. Settled in [issue #14](https://github.com/DigitalWink/spec-kit-for-human-team/issues/14).
_Avoid_: fixture test, premise check

**Manual behavioral check**:
The only thing that asserts what an agent executing the interview actually does. A numbered entry in a `TESTING.md`, giving the fixture recipe, the sentence to type at the agent, and the literal lines to look for. `tests/hooks/TESTING.md` is the working precedent. Not collected by pytest, run once per release, never in CI.
_Avoid_: manual test, smoke test
