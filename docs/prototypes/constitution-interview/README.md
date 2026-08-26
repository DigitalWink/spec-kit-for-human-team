# Prototype — full sample interview transcripts

**THROWAWAY PROTOTYPE. Do not ship. Do not merge to `main`.**

Primary source for [issue #8 — Prototype: a full sample interview transcript](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8), on map [#1 — guided constitution authoring](https://github.com/DigitalWink/spec-kit-for-human-team/issues/1).

Lives on branch `prototype/constitution-interview-transcripts`, out of `main`, per the `/prototype` skill's rule 6 and `/wayfinder`'s primary-source convention.

**Skill-branch note.** The `/prototype` skill offers two branches — a single-file HTML logic demo, or UI variations on a route. Neither fits: the question this prototype answers is *what does a text interview read like*, and the artifact that answers it is the text. The skill's other five rules are followed: throwaway, clearly marked, no persistence, no polish, state surfaced after every turn, captured as a primary source on a branch out of `main`.

---

## What this is

The settled design on map #1 is eleven closed tickets. Nothing had executed it end to end. These are four transcripts that do, against real measured facts, plus the documents each run writes and the analysis the ticket exists for.

| File | What |
|---|---|
| [arm-a-brownfield.md](arm-a-brownfield.md) | **The scoring arm.** Authoring against this repo. 9 decisions. |
| [arm-b-greenfield.md](arm-b-greenfield.md) | Authoring against an empty repo. 7 decisions, all cold. Plus variant B′, the early repo where the menu actually lives. |
| [arm-c-floor.md](arm-c-floor.md) | Every area answered "I don't know". 0 principles. Assigned by #13. |
| [arm-d-amendment.md](arm-d-amendment.md) | Amendment over this repo's ratified constitution. 3 decisions. Assigned by #6. |
| [output-a-constitution.md](output-a-constitution.md) | The document arm A writes. 8 principles, 22 obligations. |
| [output-b-constitution.md](output-b-constitution.md) | The document arm B writes. 4 principles, 5 obligations. |
| [output-c-constitution.md](output-c-constitution.md) | The document arm C writes. 0 principles, 0 obligations. |
| [output-d-constitution.md](output-d-constitution.md) | The document arm D writes. 5 principles, 41 obligations. |
| [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8) | **The verdict.** The four questions, the comparison against today's constitution, the seven behavioural assertions, and every defect found. |

Start with [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8) if you want the answer; start with [arm-a-brownfield.md](arm-a-brownfield.md) if you want to see the flow.

---

## Marking legend

There is no human on the other side of these transcripts. Every line the *user* says is invented. Telling an invention apart from a measurement is what makes the artifact usable, so three markers run throughout:

| Marker | Meaning |
|---|---|
| `[FIXTURE]` | **An answer I invented.** The reply a competent maintainer of that repo would plausibly give. Never a measurement. Always on the user's side of the turn. |
| `[DEVIATION]` | Where the transcript departs from a string or a number the settled record fixes, with the reason. |
| `[DEFECT]` | Where executing the design exposed a gap, a contradiction, or an unspecified case in the settled record. |
| `[FINDING]` | An observation the transcript makes possible — the thing the ticket exists for. Not necessarily a problem. |

Everything **not** marked `[FIXTURE]` on the tool's side of a turn is measured against the repo at HEAD with a named command. No inference in these transcripts is invented. Where a number in the settled record disagreed with a measurement, the measurement wins and the disagreement is marked.

---

## Fixture premises, stated once

Each arm names its own premise at the top. Two are load-bearing enough to state here:

- **Arm A requires `.specify/memory/constitution.md` to be removed first.** Today's document passes all five conditions of #6's amendment predicate — measured — so a run against this repo as it stands routes to *amendment*, not authoring. That is arm D.
- **This repo is not spec-kit-initialized.** `.specify/` holds only `memory/constitution.md`: no `templates/`, no installed agent command files, no `extensions.yml`. Arms A and D assume `specify init` has run. The literal uninitialized state is reported in [the resolution comment on #8](https://github.com/DigitalWink/spec-kit-for-human-team/issues/8) rather than smuggled into an arm.

---

## Reproducing the measurements

Every number in these files came from a command. The load-bearing ones:

```bash
# runtime dependency set, artifact ceiling
sed -n '1,60p' pyproject.toml

# command-verb grammar, both forms, and the near-miss band
grep -rhoP '\.command\(\s*"[^"]+"' src --include=*.py | grep -oP '"[^"]+"' | tr -d '"' | sort | uniq -c | sort -rn
grep -rn -A2 '\.command()' src --include=*.py | grep -P 'def '

# recurring flag grammar
grep -rhoP '"--[a-z0-9][a-z0-9-]*"' src --include=*.py | tr -d '"' | sort | uniq -c | sort -rn

# command declaration sites and files
grep -rho '\.command(' src --include=*.py | wc -l
grep -rl  '\.command(' src --include=*.py | wc -l

# triggers
grep -rlE 'subprocess|Popen|os\.system' src --include=*.py | wc -l
sed -n '30,36p' .github/workflows/test.yml

# obligations, either document, by #7's S3 rule
grep -oiE '\bMUST NOT\b|\bMUST\b|\bSHOULD NOT\b|\bSHOULD\b' <file> | wc -l

# the amendment predicate
grep -cE '\[[A-Z][A-Z0-9_]{3,}\]' .specify/memory/constitution.md
grep -c 'TODO(' .specify/memory/constitution.md
```

---

## Headline numbers

| | Today's `.specify/memory/constitution.md` | Arm A output |
|---|---|---|
| Principles | 5 | 8 |
| **Obligations** | **46** | **22** |
| Body characters | 11,699 | 3,646 |
| Whole file | 13,213 | 4,003 |
| Sync Impact Report | 1,512 | 355 |
| Plan-visible principles | — | 6 of 8 |

Obligations are counted on both sides by the same rule — #7's S3, as amended: MUST / MUST NOT / SHOULD clauses, case-insensitive, file-wide. This is **not** #5's "93 atomic obligations" unit, which decomposes a clause into its separately-checkable parts. Using one unit on both sides is the whole point.
