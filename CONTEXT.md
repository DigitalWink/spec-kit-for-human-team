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
The interview's one-search test for whether the plan-time gate exists on this stack: the installed `speckit.plan` command for this agent either carries a `Constitution Check` instruction or it does not. Three outcomes — present, absent, not determined — and the routing table names which. It asks nothing, blocks nothing, and changes no step. Settled in [issue #9](https://github.com/DigitalWink/spec-kit-for-human-team/issues/9).
_Avoid_: stack check, preset detection

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
How one area is presented. **Pre-filled** — a rule inferred from the repo, shown with its gate question and its source. **Menu** — up to five candidate rules, nothing pre-selected. **Cold** — an open question, no candidates. Every shape costs one decision.

**Decision**:
The interview's budget unit: one area step, closed by one answer. A push-back, a disambiguation, and the correction round are not decisions.
_Avoid_: question, turn, prompt

**Menu**:
The step shape used when the repo yields no rule for an area but can still name a fact that says which rules are worth offering. Nothing is pre-selected, and choosing none is a valid answer. A menu candidate never carries a measured set or number. What it offers is the area's **strictness ladder**.

**Strictness ladder**:
The candidate rules a menu step offers for one area. Shipped with the preset, keyed to the area and never to a project type. Defined by nesting: **each rung forbids a strict subset of what the rung above forbids**, so the rungs are totally ordered and are shown strictest first. Rung 1 is the area's own settled rule; the ladder ends at the last rung that still forbids something, so it runs one to three rungs and is never padded. Settled in [issue #11](https://github.com/DigitalWink/spec-kit-for-human-team/issues/11).
_Avoid_: starter set, candidate library, project-type preset

**Rung**:
One candidate rule on a strictness ladder. A whole rule, not a clause to be combined with others — the user picks at most one. It passes the route-out table and the admission test at authoring time, before the preset ships, which is where its genericity is screened.

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
A statement offered for the constitution, before the admission test judges it.

**Route-out**:
Refusing a candidate rule and naming where it belongs instead — a linter config, `AGENTS.md`, an ADR, the spec, or nothing.
_Avoid_: reject, drop, exclude

**Routing table**:
The report the interview prints after writing the constitution: one line per routed-out candidate, saying what it was and where it went. Lives outside the constitution file. It also carries the gate probe's status line, so a stack with no plan-time gate is stated rather than left silent.

**Surface contract**:
The area covering the product's own external surface — command verbs, flag names, endpoint names, output shapes, exit codes, their stability, and the documentation that ships with a change.

**Amendment interview**:
The short "what changed?" flow that runs when a constitution already exists, instead of authoring one from scratch.

**Announced count**:
The exact number of decisions the interview will cost, stated before the first question. Knowable because the scan completes first. Names what it excludes, because follow-up turns are real typing the number does not cover. Settled in [issue #2](https://github.com/DigitalWink/spec-kit-for-human-team/issues/2).
_Avoid_: budget, cap, estimate

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
What the pre-walk scan may spend: forty file reads and twelve searches, one pass. Triggers are resolved first, because an unevaluated trigger changes the announced count.

**Deferred/Assumed table**:
The third closing artifact. Records content admitted but unanswered, assumed, or dropped for budget — as against the routing table, which records content **refused**, and the change log, which records content **changed**. Every default that left the document short of what the user might have wanted lands here, reversible in the correction round.

**Write contract**:
What the write step must emit: the template's mandatory structure, filled with the accepted rule text verbatim, plus the pipeline-fixed Governance block and footer, and nothing else. Owned by the preset that owns the write; the self-review verifies it. Settled in [issue #7](https://github.com/DigitalWink/spec-kit-for-human-team/issues/7).

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
