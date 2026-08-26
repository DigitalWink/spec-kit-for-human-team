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
A point where a consumer can block on a constitution violation. `/plan` fills `## Constitution Check` and errors; `/analyze` rates a MUST conflict as CRITICAL.

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
The step shape used when the repo yields no rule for an area but can still name a fact that says which rules are worth offering. Nothing is pre-selected, and choosing none is a valid answer. A menu candidate never carries a measured set or number.

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
The report the interview prints after writing the constitution: one line per routed-out candidate, saying what it was and where it went. Lives outside the constitution file.

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
The third closing artifact. Records content admitted but unanswered, assumed, or dropped for budget — as against the routing table, which records content refused. Every default the interview applies lands here, reversible in the correction round.

### Packaging

**Preset**:
A bundled layer that overrides or wraps core command templates without modifying them. `presets/lean` and `presets/constitution-sync` are the working precedents.

**Integration**:
A supported coding agent, as a self-contained subpackage under `src/specify_cli/integrations/`.
_Avoid_: agent adapter, provider

**Extension**:
An opt-in add-on that owns behavior the CLI deliberately does not, such as `agent-context` owning the managed section of `CLAUDE.md` and `AGENTS.md`.
