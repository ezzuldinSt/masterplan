---
name: masterplan
description: Turns a project idea or short prompt into a researched, ranked implementation plan that another AI agent can execute. Clarifies intent with targeted questions, brainstorms approaches until each one fully satisfies the user's goal, designs an implementation methodology per approach, updates every step with current best practices from web research, ranks the options on professionalism, efficiency, performance and quality of outcome, and, once the user chooses, writes a complete plan folder (introduction, STATUS tracking, main plan, milestone folders with one file per step, skills-usage map) with an outcome judgement after every milestone. Use this whenever someone wants to plan, scope, architect or spec an app, product, feature or system before building it, or wants an implementation plan, roadmap or milestones for an agent to follow, even if they only say "I have an idea for..." or "how should I build...". Also use it to resume a plan folder it created or to judge a finished milestone.
license: MIT
metadata:
  author: ezzuldinSt
  version: "1.0.0"
---

# Masterplan

Turn a rough idea into two deliverables:

1. **A decision for the user:** a ranked, researched set of implementation options (`planning/OPTIONS.md`) to choose from.
2. **A plan another agent can execute:** a folder of linked markdown files that a different agent, with no access to this conversation, can pick up and carry out milestone by milestone, with the user judging the outcome of each milestone.

Two qualities decide whether this works:

- **Intent fidelity.** Every approach, score and milestone judgement is measured against what the user actually wants, captured once in `planning/INTENT.md` and kept current. A polished plan for the wrong goal is worthless.
- **Zero-context executability.** The plan's reader starts cold. Anything that exists only in this conversation is lost to it, so every file has to stand on its own: no "as discussed above", no unstated assumptions.

## Ground rules

- **"The user"** is whoever invoked this skill: usually a person, sometimes an orchestrating agent. If no one can answer (an unattended run), take the recommended choice, mark it `ASSUMPTION`, and surface it prominently the next time the user can review.
- **Respect the gates.** The user owns the decision points in the workflow table. When someone can answer, don't move past a gate without their answer; work built on an unconfirmed premise is the most expensive rework there is.
- **Ask efficiently.** Batch questions, lead with the ones that would change the plan most, give options with a recommended default, and don't ask anything you can infer or look up. Use the environment's structured question tool if there is one (for example AskUserQuestion); otherwise use a numbered list the user can answer in one line ("1b, 2 default, 3: weekly").
- **Research beats memory** for anything version-, vendor- or practice-specific. Training data ages; cite sources with dates.
- **One home per fact.** Progress lives only in STATUS files, intent only in INTENT.md, decisions only in DECISIONS.md. Other files link to them instead of copying, so nothing drifts out of sync.
- **Scale to the idea.** A weekend script doesn't need five approaches and forty steps. For small ideas, use fewer approaches, lighter research and fewer milestones, but keep every gate and every file type so every plan executes the same way.
- **Keep the chat light.** Long content belongs in files. In messages, lead with what you need from the user, then give a short summary and point to the file.

## Workflow

| # | Phase | Produces | Gate (the user decides) |
|---|---|---|---|
| 1 | Clarify intent | `planning/INTENT.md` | The intent is right |
| 2 | Brainstorm approaches | Confirmed shortlist | Each kept approach fully satisfies the goal |
| 3 | Design methodologies | One methodology per approach | — |
| 4 | Research best practices | `planning/RESEARCH.md`, research-updated steps | — |
| 5 | Rank | Scores with justifications | — |
| 6 | Write the options report | `planning/OPTIONS.md` | Which option to build |
| 7 | Write the plan | The plan folder | The plan is approved |
| 8 | Execute and judge | STATUS updates, milestone judgements | Each milestone is what they intended |

Phases 1–7 are the planning job. Phase 8 is written into the plan itself so every executing agent follows the same protocol; follow it yourself when asked to execute, resume or judge a plan.

Create the plan folder early, where the user wants it (ask in the first round; default `./<project-slug>-plan/`, or `docs/plan/` inside an existing repository). If a plan folder for this project already exists, read its README.md and STATUS.md and continue from where it stands instead of starting over. If you can't write files, deliver the folder as a zip archive or as clearly labelled file blocks.

## Phase 1 — Understand the idea and clarify intent

1. **Restate the idea** in one or two sentences so the user sees what you understood.
2. **Map what's known and what's missing** across the dimensions that change architecture and scope:
   - Problem and purpose: why this should exist, whose pain it removes
   - Users and context: who uses it, where, how often, at what scale
   - Must-have outcomes versus nice-to-haves
   - Success criteria: how the user will decide it's done and good
   - Scope boundaries: what it deliberately won't do
   - Constraints: budget, deadline, platforms, required or ruled-out technology, existing systems, hosting, compliance and privacy, the builder's skills
   - Execution: which agents, tools and skills will build it
   - Priorities: how to weight the four ranking criteria (default: equal)
3. **Ask in rounds** of 3–6 questions, highest impact first, each with options and a recommended default. Fill what you can from context yourself and list it as an assumption to confirm instead of asking. Stop when no open question would change which approaches you'd propose, usually after one to three rounds. If the user says "you decide", decide, and record it as an assumption.

   One question from a good round:
   > **2. Who will use it?** a) only you *(recommended: simplest to build and secure)* · b) a small invited team · c) the public, which needs sign-up, moderation and scaling

4. **Write `planning/INTENT.md`** from the template below. Make every success criterion observable and testable ("a first-time user can publish a listing in under three minutes", not "easy to use"). These criteria are the yardstick for approach fit and for every milestone judgement; vague ones produce milestones nobody can judge.
5. **Gate 1:** show a short summary of the intent and ask the user to confirm or correct it.

```markdown
# Intent Brief — <Project name>
Confirmed by user: <YYYY-MM-DD> · Last changed: <YYYY-MM-DD>

## The idea, in the user's words
> <original prompt, verbatim>

## Problem and purpose
## Users and context
## Success criteria
| ID | Criterion (observable, testable) | Priority |
|---|---|---|
| SC-1 | … | Must / Should / Could |

## Scope
- In: …
- Out (non-goals): …

## Constraints
Budget · deadline · platforms · required / ruled-out tech · integrations · hosting · compliance · who builds it

## Ranking weights
Professionalism __% · Efficiency __% · Performance __% · Quality of outcome __%

## Assumptions to confirm
- A-1: …

## Change log
- <YYYY-MM-DD>: <what changed and why>
```

## Phase 2 — Brainstorm approaches until each one satisfies the goal

1. **Generate 3–5 genuinely different approaches**: different strategies or architectures, not cosmetic variants (build vs. buy vs. no-code; web vs. native vs. cross-platform; monolith vs. modular services; managed platform vs. self-hosted; rules vs. machine learning). Include the simplest thing that could work and the most robust option. The contrast shows the user the real trade-offs, and the best answer often sits between them.
2. **For each approach,** write the concept in a paragraph, how it works at a high level, its main trade-offs and its biggest risk, and check it against every success criterion: **Full**, **Partial** (say what's missing) or **None**.
3. **Drop approaches that can't meet a Must criterion** even with changes, giving the reason in one line so the user can object.
4. **Present them together** with a fit matrix (approaches × success criteria), and ask about each one: *"Does this fully satisfy your goal? If not, what's missing or wrong?"* With a structured question tool, ask one question per approach with the options "Fully satisfies", "Partly (I'll say what's missing)" and "Doesn't fit".
5. **Refine and ask again.** Modify, merge, split, add or drop approaches based on the answers, and re-check them. Repeat until every approach you keep has been confirmed by the user as fully satisfying their goal, or the user tells you to move on. Usually 2–4 approaches survive.
6. **Keep intent current.** If an answer reveals a new or changed requirement, update INTENT.md first (with a change-log line), then re-check every approach; a new requirement can sink one the user liked.
7. **Gate 2:** the confirmed shortlist.

## Phase 3 — Design an implementation methodology for each approach

Brainstorm before committing: for each shortlisted approach, consider a couple of ways to implement it (stack choices, build order, hosting), keep the strongest as its methodology, and note the runner-up in a line. Give every methodology the same structure so they can be compared fairly in Phase 5:

- **Architecture:** components, data flow, external services (a small Mermaid diagram helps)
- **Stack:** languages, frameworks and services, each with a one-line reason (versions are confirmed in Phase 4)
- **Milestones:** usually 3–8, each ending in something the user can see, run or test, mapped to success criteria. Prefer vertical slices (a thin feature working end to end) over horizontal layers (all of the database, then all of the API): the user can judge a slice, but not a layer.
- **Steps:** one line each, with dependencies
- **Cross-cutting concerns:** testing, security and privacy, data and migrations, CI/CD and environments, observability, documentation
- **Effort and cost:** rough ranges to build and to run
- **Risks:** the main ones, each with a mitigation

## Phase 4 — Research the latest best practices for every step

The aim is to replace what you remember with what is current and sourced, step by step.

1. **List what to research:** every step of every methodology. Many steps recur across methodologies (authentication, CI, deployment); research each once and reuse the findings.
2. **Spend effort where it matters.** Fast-moving tools, security, data handling, payments and external APIs get a deep look; well-established routine steps get a quick check that nothing has changed.
3. **For each step, look for** the current stable version and recent breaking changes, the officially recommended setup, deprecations, security advisories and hardening guidance, known pitfalls, performance advice and reference implementations. Include the current year in queries. Prefer primary sources (official documentation, release notes, standards bodies, maintainers' repositories, vendor engineering blogs) over listicles, and confirm anything that drives a decision with a second source.
4. **Update the steps** with what you found: concrete versions, commands, configuration, recommended patterns and pitfalls to avoid. Cite inline as `[R12]`.
5. **Let research change the plan.** If a step turns out to be outdated (a deprecated library, a better-supported pattern), rewrite it and list the change under the methodology's "Changed by research". If an approach can no longer meet a Must criterion, tell the user before ranking.
6. **Log every source** in `planning/RESEARCH.md`: ID, title, URL, publisher, published or last-updated date, date accessed, what it supports, and which steps cite it.
7. **Parallelize when you can.** With sub-agents available, give each methodology its own researcher along with its step list and the RESEARCH.md format, then merge and de-duplicate the results.
8. **No web access?** Say so, mark the affected steps `UNVERIFIED`, and carry on; verifying them becomes the first task of the milestone that contains them.

## Phase 5 — Rank the methodologies

Score each methodology from 1 to 10 on the four criteria. Fixed definitions keep scores comparable:

| Criterion | What it measures |
|---|---|
| **Professionalism** | Alignment with current industry standards and the Phase 4 research: security, maintainability, testability, conventions, documentation. How a senior reviewer would rate it. |
| **Efficiency** | Effort, time to first value, cost to build and run, and number of moving parts, relative to what is delivered |
| **Performance** | Speed, scalability, resource use and reliability at the load described in INTENT.md |
| **Quality of outcome** | How completely and how well the result meets the success criteria and the user's experience goals, and how well it will age |

Anchors: 9–10 exemplary · 7–8 strong · 5–6 adequate with notable gaps · 3–4 weak · 1–2 unacceptable.

- Justify each score in a sentence or two that points to evidence: a source, a success criterion, an estimate. An unjustified score is just an opinion.
- Weighted total = Σ (weight × score), using the weights in INTENT.md.
- Break ties on Must-criteria fit, then on lower risk.
- Give each methodology a confidence level (High / Medium / Low) and say what would raise it.
- Before finalizing, argue against your top pick: what would make it the wrong choice? If that argument holds up, revisit the scores.

## Phase 6 — Write the options report and let the user decide

Write `planning/OPTIONS.md`:

```markdown
# Implementation Options — <Project name>
<YYYY-MM-DD> · Intent: [INTENT.md](INTENT.md) · Sources: [RESEARCH.md](RESEARCH.md)

## Recommendation
<Three sentences: which option, why, and when the runner-up would be the better choice>

## Ranking
| Rank | Option | Professionalism | Efficiency | Performance | Quality | Weighted | Confidence |
|---|---|---|---|---|---|---|---|

## How to choose
- Choose <A> if … · Choose <B> if …

## Option A — <name> (rank #)
### Approach and architecture
### Stack (with versions)
### Milestones and steps (research-updated, cited)
### Fit against each success criterion
### Pros and cons
### Risks and mitigations
### Effort and cost
### Score justifications
### Changed by research

## Option B — …
```

Present the ranking table and the recommendation in the conversation together with the file, and ask the user to choose one option or to combine parts of several. For a combination, write the hybrid out explicitly, re-check its fit and its research where it changed, score it, and confirm it with the user.

**Gate 3:** record the choice in `planning/DECISIONS.md` (`D-001 — <date> — Chose <option> because …`). Wait for this decision before writing the plan; the plan is large, and rewriting it for a different option is wasted work.

## Phase 7 — Write the implementation plan folder

```
<project-slug>-plan/
├── README.md                Project introduction and entry point: the project, this folder's structure, how to execute it
├── STATUS.md                Project progress: where things stand, next action, milestone table, blockers, log
├── IMPLEMENTATION_PLAN.md   Main plan: architecture, global conventions, each milestone's detailed goal, briefed steps and folder link
├── SKILLS_USAGE.md          Which skills to use for each milestone and each step
├── planning/
│   ├── INTENT.md            The user's intent and success criteria: the judgement yardstick
│   ├── OPTIONS.md           The ranked options the user chose from
│   ├── RESEARCH.md          Sources behind the plan
│   └── DECISIONS.md         Decision log: choices, changes, accepted risks
├── M01-<slug>/
│   ├── README.md            Milestone goal, scope, deliverables, acceptance criteria, step index
│   ├── STATUS.md            Step progress and the milestone's outcome judgement
│   ├── S01-<slug>.md        One file per step, fully detailed
│   └── S02-<slug>.md
└── M02-<slug>/ …
```

**Conventions**
- **IDs:** milestones `M01`, steps `M01-S01` (globally unique), success criteria `SC-1`, acceptance criteria `M01-AC1`, sources `R1`, decisions `D-001`, assumptions `A-1`. Zero-pad numbers so folders sort in order, and use kebab-case slugs.
- **Navigation:** every file opens with a breadcrumb of relative links, such as `[Plan](../README.md) › [M01 Foundation](README.md) › S03`, so an agent dropped into any file can find its way.
- **Links** are relative, so the folder keeps working when it's moved or committed to a repository.
- **Write order:** IMPLEMENTATION_PLAN.md → milestone READMEs → step files → SKILLS_USAGE.md → STATUS files → README.md last, so the introduction describes what was actually produced. Fill any gaps you hit while detailing steps with targeted research, and add those sources to RESEARCH.md.

**How detailed a step must be.** The test: could a capable agent with no access to this conversation complete the step correctly on the first try, using only this file and the files it links to? That takes exact file paths, commands, interfaces and signatures, data shapes, configuration values, edge cases and a way to verify the result. Include short code snippets where they remove ambiguity (an interface, a config block, a schema), but don't write the full implementation into the plan: that's the executing agent's job, and code embedded in plans goes stale.

**Step size.** One coherent unit of work an agent can finish and verify in a single session. If a step spans unrelated concerns or needs more than about ten instructions, split it.

**Skills mapping.** Before writing SKILLS_USAGE.md, find out which skills actually exist in the execution environment (the available-skills list, installed skill folders, plugins) and map only those; a plan that names a skill nobody has fails at its first step. If a step would clearly benefit from a skill that isn't installed, list it under "Recommended to add" with a fallback. Step files repeat their skills for convenience; SKILLS_USAGE.md is the authoritative index, and the two must match.

**Quality gate before presenting.** Check every item (a short script can check links and IDs if you have a shell):
- [ ] Every Must success criterion traces to at least one milestone acceptance criterion with a way to verify it
- [ ] Every step has inputs, instructions, outputs, acceptance criteria, verification and skills
- [ ] Dependencies are acyclic, and executing top to bottom works
- [ ] No placeholders (`TBD`, `…`, `<fill in>`) remain outside deliberate "Open questions"
- [ ] Every relative link resolves and IDs match across files
- [ ] SKILLS_USAGE.md covers every step and names only skills that exist, or flags them as recommended
- [ ] STATUS files are initialized and "Next action" points to `M01-S01`

**Gate 4:** summarize the milestones and their goals, point to the folder, and ask the user to approve or adjust. Also ask whether execution should pause for their confirmation after every milestone (the default) or may continue on a `PASS` verdict when they're unavailable, and record the answer in README.md. Log changes in DECISIONS.md.

## Phase 8 — Execution protocol and milestone outcome judgement

Execution starts only after Gate 4. Copy this protocol into the plan's README.md so every executing agent follows it, whether or not it has this skill.

**Executing a step**
1. Read README.md → planning/INTENT.md → IMPLEMENTATION_PLAN.md → STATUS.md.
2. Open the step named in STATUS.md's "Next action" and confirm its dependencies are `DONE`.
3. Load the skills SKILLS_USAGE.md lists for it.
4. Mark the step `IN_PROGRESS` in the milestone STATUS.md, and update "Now" in the root STATUS.md.
5. Do the work, then run the step's verification and check every acceptance criterion.
6. Mark it `DONE` with evidence (commands run, test output, commit or PR), or `BLOCKED` with the reason and what's needed. Update the root STATUS.md: progress, next action and a log line.
7. **When reality differs from the plan,** update the step file and any dependent steps and log a decision rather than silently improvising; the next agent needs to know. If the change touches intent, scope or a success criterion, stop and ask the user, then update INTENT.md and the affected milestones.
8. After a milestone's last step, run the outcome judgement before starting anything in the next milestone.

**Milestone outcome judgement.** The question isn't only "were the steps done?" but "is this what the user intended?"
1. **Use a fresh reviewer when possible:** a sub-agent that didn't do the work, given only INTENT.md, the milestone README and the deliverables. Agents grading their own work tend to see what they meant to build.
2. **Re-verify with fresh evidence:** run the tests, run the app or a demo, inspect the outputs. Step checkmarks aren't evidence.
3. **Check each acceptance criterion and each mapped success criterion:** Met, Partially met or Not met, with evidence.
4. **Check intent:** does the result serve the users and purpose in INTENT.md? Note any drift, gold-plating or scope creep.
5. **Check quality:** security, performance and maintainability issues, and any technical debt taken on.
6. **Give a verdict:** `PASS`, `PASS_WITH_FOLLOW_UPS` or `FAIL`.
7. **Ask the user:** show the evidence (a demo or screenshots where possible) and ask whether this is what they intended. Their answer decides. If they can't be reached, record the verdict as awaiting confirmation and continue only if README.md allows continuing on `PASS`.
8. **Record** the judgement in the milestone STATUS.md and the root STATUS.md.
9. **Act on the verdict.** On `PASS_WITH_FOLLOW_UPS`, turn each follow-up into a step in an upcoming milestone so it isn't lost. On `FAIL` or rejection, add remediation steps to the milestone (`S07-fix-<slug>.md` onward), execute them and judge again; don't start the next milestone until the user accepts the result or explicitly accepts the risk, logged in DECISIONS.md.
10. **Look ahead:** check whether anything learned changes the upcoming steps, and update them before continuing.

After the last milestone, judge the whole project against every success criterion the same way, record it in the root STATUS.md, and mark the project `COMPLETE`.

## File templates

Use these status values everywhere: `TODO` · `IN_PROGRESS` · `DONE` · `BLOCKED` · `NEEDS_REWORK`. Plain words are easy to grep and update.

### README.md (project introduction)
```markdown
# <Project name> — Implementation Plan
<One paragraph: what is being built, for whom, and the chosen approach.>

**Start here.** This folder is a complete, self-contained plan written to be executed by AI agents and humans. Read this file first, then follow "How to execute this plan".

## At a glance
- **Goal:** … ([intent](planning/INTENT.md))
- **Chosen approach:** … ([why](planning/DECISIONS.md))
- **Stack:** …
- **Milestones:** <n>, see [IMPLEMENTATION_PLAN.md](IMPLEMENTATION_PLAN.md)
- **Progress:** [STATUS.md](STATUS.md)
- **Execution mode:** pause for user confirmation after each milestone | continue on PASS when the user is unavailable

## How this folder is organized
<The folder tree, with a one-line purpose for each file>

## How to execute this plan
<The Phase 8 protocol, copied in full>
```

### STATUS.md (project progress)
```markdown
# Project Status — <Project name>
[Plan](README.md) › Status
Last updated: <YYYY-MM-DD HH:MM> by <agent or person> · Overall: IN_PROGRESS · <x>/<y> milestones · <a>/<b> steps

## Now
- **Current:** M01-S02 — <title> (IN_PROGRESS)
- **Next action:** [M01-S03 — <title>](M01-<slug>/S03-<slug>.md)
- **Blockers:** none

## Milestones
| Milestone | Status | Steps done | Judgement | User accepted |
|---|---|---|---|---|
| [M01 — <name>](M01-<slug>/README.md) | IN_PROGRESS | 2/6 | — | — |

## Open questions and assumptions to confirm
## Log
- <YYYY-MM-DD> — M01-S02 DONE — tests pass, commit <sha>
```

### IMPLEMENTATION_PLAN.md (main plan)
```markdown
# Implementation Plan — <Project name>
[Plan](README.md) › Implementation plan

## Chosen approach
<Summary, with links to its section in planning/OPTIONS.md and to D-001>

## Architecture
<Overview and diagram>

## Global conventions (apply to every step)
- Stack and pinned versions (researched <YYYY-MM-DD>)
- Repository layout, naming, code style, branching and commits
- Security and privacy rules; secrets handling; environments
- Testing standard, and the Definition of Done every step must meet

## Milestones
### M01 — <name> → [folder](M01-<slug>/README.md)
**Goal:** <detailed: what will exist and work at the end, and why it comes at this point>
**Demonstrable outcome:** … · **Satisfies:** SC-1, SC-3 · **Depends on:** —
**Steps:**
1. M01-S01 — <title>: <one-line brief>
2. …

## Traceability
| Success criterion | Milestones | Verified by |
|---|---|---|

## Risks and mitigations
## Open questions
```

### M01-<slug>/README.md (milestone)
```markdown
# M01 — <name>
[Plan](../README.md) › M01

## Goal
<Detailed goal, and how it serves the user's intent>

## Scope
- In: … · Out: …

## Deliverables
## Entry criteria
## Acceptance criteria (used in the outcome judgement)
| ID | Criterion | Satisfies | How to verify |
|---|---|---|---|
| M01-AC1 | … | SC-1 | <command or demo, and the expected result> |

## Steps
| Step | Title | Depends on |
|---|---|---|
| [M01-S01](S01-<slug>.md) | … | — |

## Notes for the judgement
<What the user will look for, and a short demo script>
```

### M01-<slug>/STATUS.md (milestone progress and judgement)
```markdown
# M01 Status — <name>
[Plan](../README.md) › [M01](README.md) › Status
Status: IN_PROGRESS · Last updated: <YYYY-MM-DD> by <agent or person>

## Steps
| Step | Status | Evidence / notes |
|---|---|---|
| M01-S01 | DONE | tests pass; commit <sha> |

## Outcome judgement
- **Date / reviewer:**
- **Acceptance criteria:** M01-AC1 Met — <evidence> …
- **Success criteria:** SC-1 Met — <evidence> …
- **Intent check:**
- **Quality check:**
- **Verdict:** PASS | PASS_WITH_FOLLOW_UPS | FAIL
- **User confirmation:** <date and the user's answer, or "awaiting">
- **Follow-ups:**
```

### M01-<slug>/S03-<slug>.md (step)
```markdown
# M01-S03 — <Title>
[Plan](../README.md) › [M01 — <name>](README.md) › S03
**Skills:** <names> (per [SKILLS_USAGE.md](../SKILLS_USAGE.md)) · **Depends on:** M01-S02 · **Effort:** <range>

## Objective
<What this step achieves, in one or two sentences>

## Why it matters
<How it serves the milestone goal, and which success criteria it supports>

## Context
<What the agent must know that isn't obvious: relevant decisions, existing files, constraints>

## Inputs and prerequisites
- <Artifacts from earlier steps, tools, access, environment variables>

## Instructions
1. <Exact action: command, file path, what to write, configuration values>
2. …

## Expected outputs
- <Files created or changed, services running, artifacts>

## Acceptance criteria
- [ ] <Testable condition>

## Verification
<Commands to run with their expected results; manual checks>

## Pitfalls and best practices
- <From research, cited as [R#]>

## If something goes wrong
<Recovery or rollback, and when to stop and ask>

## References
- [R4] <title> — <URL>
```

### SKILLS_USAGE.md (skills map)
```markdown
# Skills Usage
[Plan](README.md) › Skills usage
Skills confirmed available on <YYYY-MM-DD> in <environment>. Load the listed skills before starting a step; use the fallback if one is missing.

## By milestone
| Milestone | Primary skills | Why |
|---|---|---|

## By step
| Step | Skills | Use them for | Fallback |
|---|---|---|---|
| M01-S01 | … | … | … |

## Recommended to add
| Skill or capability | Would help with | Until then |
|---|---|---|
```

### planning/RESEARCH.md and planning/DECISIONS.md
- **RESEARCH.md:** one table, `| ID | Title | URL | Publisher | Published / updated | Accessed | Supports | Cited in |`
- **DECISIONS.md:** one entry per decision, headed `## D-001 — <YYYY-MM-DD> — <title>`, with *Context*, *Decision*, *Alternatives considered* and *Consequences*.
