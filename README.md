<!--
public-header:
  originator: "Jonas Maandi Wallin"
  editor: "Jonas Maandi Wallin"
  contact: "https://www.linkedin.com/in/jonasmaandiwallin"
  license: "CC-BY-4.0"
  repository: "https://github.com/jonasmaandiwallin/runapp-platform"
  path: "README.md"
-->
# Runapp

*An evidence-based running coach that is deterministic at runtime and governed in evolution.*

## 1 — The question

What would a running coach look like if every decision it made could be traced, reproduced and defended?

Most training products answer a different question. They give a plan from a template, a readiness score that the runner interprets, or advice from a language model that sounds right. Each is useful, and each leaves the same gap: nobody can say exactly why this runner got this session today, or whether the same runner would get the same session tomorrow.

Runapp is built to close that gap. This repository explains how — the logic of the design, at the level of principles. It is not the specification, which is shared under NDA, and it is not a product announcement: Runapp is in development and not yet released.

## 2 — What it is

Runapp is a running coach that builds an individual training plan from a measurement of the runner's fitness and the week they actually have, and adapts the plan as their body and life change.

Underneath, it is a rules engine. Every behavior is written down first, as a specification that says what the system must do, what it must never do and how a violation shows. Every threshold in those rules lives in one registry, and each names where it comes from — a study, coaching consensus or a design decision — and how much confidence it carries. The engine implements the specification; the specification is the truth, and where the two differ, the engine is wrong.

Two properties hold the design together:

- **Deterministic at runtime.** The same inputs give the same plan and the same decision, every time. No randomness and no model inference decide training. A language model may word how a session is presented, in the app or in a conversation, but it never makes or changes a decision.
- **Governed in evolution.** Nothing changes the specification without passing its checks. That holds for what the system learns from its own data — a pattern found across runners is treated as a hypothesis and enters the same research pipeline as a published study — and it holds for the AI agents that build Runapp, which cannot change code or specification without passing gates.

Runapp is one engine with several ways in: an iOS app that reads the runner's health data and training history, an app inside ChatGPT, and more to come. None of them owns coaching logic; each presents what the engine decided.

**What the runner sees.** None of the machinery. A plan built for the week they actually have — a few short sessions or many long ones — that still makes sense when the week changes. A session that is adjusted on a bad morning without a questionnaire to fill in. Progress when the body shows it is ready, not when a calendar says so. No streaks to keep alive, no badges, no readiness score to interpret. And when the day's session changes, the new session is simply the session: no before-and-after, no notice of a downgrade.

**Two kinds of research, one discipline.** A training plan rests on physiology — load and recovery, heart-rate variability, sleep, heat and altitude, injury risk. Whether a runner follows it rests on behaviour — motivation, choice, habit, the feeling of being treated fairly, how uncertainty is told. Runapp draws on both, and holds both to the same chain: every study has one entry in an evidence registry, the entry states only what the study itself shows, and every rule cites the entries it rests on. When a later reading of a source shows that an entry said more than the study did, the entry is corrected and the rules resting on it are read again.

## 3 — The ideas

Each idea has a white paper of its own.

**Start from a measurement, not a template.** A plan is built from a fitness test and the runner's declared week, by rules that select the structure, allocate the phases and distribute intensity. Frequency and time limits change the shape of the plan, not only its size. → [Plan Design](whitepapers/plan-design.md)

**Trust each signal as far as it deserves.** Heart rate, its variability, sleep, running mechanics, perceived effort and pain flow in from different devices with different reliability: through Apple Health, through direct integrations with Garmin, Polar, Whoop, Oura and COROS where a maker keeps data out of Apple Health, and through Strava for devices with no integration of their own. The specification's register holds 23 signals, each with a status that says whether it may yet drive a decision. Each signal is graded by confidence, and a weak signal may inform but never overrule a strong one. → [Signals](whitepapers/signals.md)

**Protect by domain, not by score.** Three physiological domains — systemic, mechanical and the central nervous system — are tracked separately and never averaged into one readiness number. Progression is earned through demonstrated stability, and safety always outranks it. → [The Escalation Mechanism](whitepapers/escalation.md)

**Adapt the day, not the plan's identity.** Sessions are modified on the day from the runner's current state, adherence drift is corrected in capped steps, and disruptions are rescheduled — all inside the plan's structure, which only grows through the escalation gate. → [The Adaptation Engine](whitepapers/adaptation.md)

**Let the engine decide and the words follow.** A decision becomes a structured snapshot before it becomes language, and the language is governed: what may be said, what may never be said, and what a language model is allowed to do with it. → *Decision Delivery* (forthcoming)

**Coach, don't engage.** No streaks, badges, points or recovery scores. Every engagement mechanism is tested against the runner's actual goal, and a mechanism without evidence stays a hypothesis until a controlled test supports it. → [The Motivation Architecture](whitepapers/motivation.md)

**Learn without losing determinism.** Runapp is designed to learn — from the individual runner, from cohorts, and eventually from the population — but no learned output ever acts on a decision directly. It is data, or a hypothesis for the research pipeline, never a rule. → *Learning Without Losing Determinism* (forthcoming)

**Make every decision traceable.** One decision, followed end to end: from a signal, through the rule that read it, to the parameter the rule used and the study behind the parameter. → *Traceable by Design* (forthcoming)

**Keep what is needed, and no more.** What the system stores, what it does not, and who can see what. → *Data & Privacy* (forthcoming)

## 4 — What is measured

- **Plan quality, in simulation.** Runapp's plans are run through a simulator against populations of synthetic runners and compared with plans from books, apps and published intervention studies, using a plan-quality metric of its own. The method, the results and the strongest case against them — the plans were optimized against the same simulator — are published in [Runapp Validation](https://github.com/jonasmaandiwallin/runapp-validation).
- **Not yet in the field.** Runapp is not released. No runner has followed a Runapp plan to a race, so nothing here claims an outcome in real runners.
- **The evidence behind the design.** Each white paper released here has an evidence register listing the works it cites and where. The registers name the studies; they do not say which value a study sets.

## 5 — Why this is unusual

Determinism, evidence and adaptivity are each common claims. What is less common is holding all three at once and making them checkable: every rule specified before it is built, every number traced to its source, every adaptation bounded by rules that protect the runner — and a system that improves over time only through the same governed path a new study would take.

The same discipline applies to how Runapp is built. The specification and the code are written by AI agents working in governed roles, and no agent can change either without passing checks that another agent runs. That method is published separately as Governed Agentic Engineering, with Runapp as its reference implementation.

We have not surveyed the market systematically and make no claim that the combination is unique.

## 6 — Limitations

- **Specified, not yet shipped.** These papers describe the design as the specification defines it. The engine implements the specification in stages; where it does not yet match, the specification governs and the engine is changed.
- **The specification is itself under review.** Where two of its rules are found to disagree, the conflict is resolved in the specification — never by what the code happens to do. The papers state only what the rules agree on.
- **Simulation is not validation.** Plan quality is measured under a physiological model, not in runners.
- **The learning tiers are not active.** Learning from cohorts and the population is specified with activation thresholds, and none has been reached.

## 7 — Map

**White papers** — in [whitepapers/](whitepapers/), in the order the engine works
1. [Plan Design](whitepapers/plan-design.md) — how a measurement becomes a training plan.
2. [Signals](whitepapers/signals.md) — what flows in, and how much it is trusted.
3. [The Escalation Mechanism](whitepapers/escalation.md) — when the runner may train harder, and how they are protected.
4. [The Adaptation Engine](whitepapers/adaptation.md) — how the plan changes day to day.
5. *Decision Delivery* (forthcoming) — how a decision becomes words, and what the language model may do.
6. [The Motivation Architecture](whitepapers/motivation.md) — why there are no streaks or scores, and what there is instead.
7. *Learning Without Losing Determinism* (forthcoming) — how the system learns from its own data without changing at runtime.
8. *Traceable by Design* (forthcoming) — one decision followed from signal to rule, parameter and study.
9. *Data & Privacy* (forthcoming) — what is stored, what it is used for, and how it leaves the system.

**Reference** — in [reference/](reference/)
- Evidence registers, one per white paper: [Plan Design](reference/plan-design-evidence-register.md), [Signals](reference/signals-evidence-register.md), [The Escalation Mechanism](reference/escalation-evidence-register.md), [The Adaptation Engine](reference/adaptation-evidence-register.md) and [The Motivation Architecture](reference/motivation-evidence-register.md).
- *The Nine Domains of the Specification* (forthcoming) — how the specification is organised, and which document prevails when two disagree.
- *Glossary* (forthcoming) — the terms the papers use.
- *Runapp as a GAE Installation* (forthcoming) — how the Governed Agentic Engineering standards are bound in Runapp: what each standard's placeholders are here, where Runapp departs from a standard, and what it adds.

**Under NDA** (forthcoming) — shown as title and abstract only
- The specification, the parameter registry and the signal registry.

**The other parts**
- How plan quality is measured — the simulator and the plan-quality metric: [runapp-validation](https://github.com/jonasmaandiwallin/runapp-validation).
- How Runapp was built — Governed Agentic Engineering: [governed-agentic-engineering](https://github.com/jonasmaandiwallin/governed-agentic-engineering).

## License and contact

Text in this repository is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/), attributed to Jonas Maandi Wallin. The full text is in `LICENSE`.

Each document in `whitepapers/` and `reference/` carries its own frontmatter with an `ai_disclosure` block: AI agents drafted the text and checked it against the specification under the editor's direction, and the block says which roles were done and how each was checked. The editor takes full editorial responsibility for what is published.

Contact: [Jonas Maandi Wallin on LinkedIn](https://www.linkedin.com/in/jonasmaandiwallin).
