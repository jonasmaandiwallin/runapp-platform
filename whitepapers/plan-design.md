---
title: "Plan Design: How a Measurement Becomes a Training Plan"
type: reference
status: draft
version: "0.1"
date: "2026-11-21"
author: "Tooling & Architecture"
audience: "External — no IP disclosure. Readable without NDA."
reading_order: 1
package: runapp
release: public
framework:
  name: "Governed Agentic Engineering (GAE)"
  originator: "Jonas Maandi Wallin"
editor: "Jonas Maandi Wallin"
contact: "https://www.linkedin.com/in/jonasmaandiwallin"
author_llm:
  - model: "Claude Opus 5.5"
    model_id: "claude-opus-5-5"
    roles: [drafting, technical-analysis, editing]
    source: platform
ai_disclosure:
  - role: drafting
    status: complete
    scope: "Text and structure of version 0.1, written by the Tooling & Architecture agent from Runapp's specification."
    evidence: "The document's own text."
    reason: null
  - role: technical-analysis
    status: complete
    scope: "The plan-design rules of Runapp's specification read topic by topic — fitness measurement, the generation sequence, structure, phases, frequency, intensity distribution, time caps, progression, personalization and starting without history. The paper states only what the governing rules agree on; where two rules disagree, it states the principle they share and leaves the detail out."
    evidence: "The release fact-check report of 2026-11-03, section on plan design: the rules read per topic, and the open conflicts between them."
    reason: null
  - role: editing
    status: complete
    scope: "Inline citations to the research each principle rests on and a sources section added on 2026-11-03, worded from the registered key findings. Section 9 corrected the same day: the protection against overload is the per-session ceiling the specification sets, not a ceiling on weekly growth, which the first wording implied."
    evidence: "The diff against the commit that first added the paper, and the citation map of 2026-11-03."
    reason: null
  - role: fact-check
    status: pending
    scope: null
    evidence: null
    reason: null
  - role: review
    status: complete
    scope: "Read in full by the editor in the form it is released."
    evidence: "Editorial approval, 2026-11-21."
    reason: null
license: "CC-BY-4.0"
repository: "https://github.com/jonasmaandiwallin/runapp-platform"
path: "whitepapers/plan-design.md"
---

# Plan Design: How a Measurement Becomes a Training Plan

*How Runapp turns one fitness measurement and a runner's week into a complete, deterministic training plan.*

---

## 1 — The Problem with Templates

A common way to give a runner a plan is to pick a template by experience level and race distance, then scale it. Beginner, intermediate, advanced; 5K, 10K, marathon. The template was written for an imagined runner, and the scaling is a guess about how far the real runner is from that imagined one.

Two things go missing. The first is the measurement: the template starts from what the runner says about themselves, not from what their body can do. The second is the week: the template assumes a schedule, and the runner's actual days, hours and constraints are fitted to it afterwards.

Runapp starts from the other end. It measures the runner's fitness, takes their week as declared, and builds the plan from rules: which structure fits this distance, this plan length and this many sessions, which phases the time allows, how intensity is distributed, and how the plan progresses. The same inputs always give the same plan. Nothing in the plan is a guess that a rule could replace.

---

## 2 — Start with a Measurement

Every intensity in a Runapp plan comes from an explicit fitness anchor, and the anchor comes from a measurement.

The specification ranks the measurements it accepts. A maximal field test — the Cooper test, twelve minutes as far as possible — sits at the top: the distance covered in twelve minutes tracks laboratory-measured maximal oxygen uptake closely (Cooper, 1968), and among walk and run field tests, the 12-minute and 1.5-mile runs correlate best with measured maximal oxygen uptake (Mayorga-Vega et al., 2016). Submaximal tests come next, and a fitness estimate imported from a wearable may be accepted under conditions — conditions, because consumer wearables that estimate maximal oxygen uptake from exercise data are accurate on average but can miss an individual's value by a wide margin, and estimates from resting data are worse (Molina-Garcia et al., 2022). When nothing better exists, a conservative default applies. A higher-ranked measurement always overrides a lower one.

Some inputs are never accepted as an anchor. Self-reported race times and training paces are not used. Every estimate, whatever its source, is checked against the same plausibility bounds before it is used.

The measurement is converted to a performance value (VDOT). In a small study, the threshold pace derived from it matched recreational runners' lactate-threshold pace, although the value underestimated their measured maximal oxygen uptake and its interval pace may be too slow for them (Scudamore et al., 2018). The value drives pace targets, the runner's level, and the projection of how much the plan can achieve. It is an anchor, not a target: it does not drift from session to session. As the runner trains, the plan schedules the efforts that let the system recalibrate — and, once enough maximal efforts exist, a critical-speed estimate takes over the placement of the threshold zone. Critical speed estimated from a recreational runner's ordinary training data predicts their marathon performance (Smyth & Muniz-Pumares, 2020).

A measurement also expires. When a runner has been completely inactive for longer than their fitness allows, the old measurement no longer describes them, and a new plan waits for a new one. Fitness gained recently is the first to go: in recently trained people, longer complete detraining reverses the gains in maximal oxygen uptake (Mujika & Padilla, 2000).

---

## 3 — A Deterministic Sequence

A plan is generated in a fixed sequence of steps. None may be skipped or reordered.

1. **Close the old plan.** One runner has one active plan. A replaced plan's future sessions are archived, never deleted.
2. **Check the fitness input.** No current measurement, no plan.
3. **Check feasibility.** The goal — distance, date, and target if the runner has one — is tested against what the measured fitness and the available time can support. If the goal is not feasible, the runner is offered two alternatives, a longer timeline or an adjusted goal, and confirms one.
4. **Select the phase sequence** for the distance and plan type, compressed if the time is short.
5. **Build the weekly template.** The session layout is resolved from the runner's constraints, the time budget is computed, sessions are assigned to the runner's declared days and slots, the intensity distribution is resolved, and the taper volume is computed.
6. **Output and activate** the full schedule.

Every step is deterministic. Day assignment is deterministic; so is the rotation of workouts within a session type. Session placement is not offered to the runner as a choice — the plan is the system's decision, made from the runner's constraints.

When the runner changes their weekly template, only the last three steps run again, from the current week forward, and they keep the phase sequence chosen when the plan was created. Past weeks are never modified, and the measurement and the feasibility decision stand.

---

## 4 — Structure by Lookup

Runapp does not store a plan per runner. It stores the rules that produce plans, and resolves each plan's structure by lookup:

- **The phase sequence** comes from a registry of plans per distance and plan type: which phases, in which proportion of the plan's length, and with which intensity-distribution profile in each phase.
- **The session layout** comes from the runner's constraints: distance, sessions per week, time cap, plan length. Distances are grouped by what dominates them — speed for the shortest races, threshold for the middle distances, marathon-specific endurance for the half and full marathon — and a layout is matched on the exact constraints first, then on the distance group.
- **The weekly frame** comes from the runner's frequency band (section 6).

Within a session type, the specific workout is chosen by the runner's level and the phase, and rotated within a group of equivalent workouts with bounded repetition. The variation is deterministic: two runners with the same inputs get the same workouts in the same order.

Six goal distances are supported: 3K, 5K, 10K, 15K, half marathon and marathon. Besides race-specific plans, there is a maintenance plan that keeps a quality stimulus without a race.

---

## 5 — Phases with Fixed Purposes

A race-specific plan has four phases, and each has a purpose that nothing in the system may change:

- **Base** builds the aerobic foundation.
- **Build** expands threshold work and introduces controlled high-intensity work.
- **Specific** delivers the stimulus the race itself demands, closing with a short sharpening period.
- **Taper** reduces volume while keeping intensity.

When the time to the race is shorter than the plan would like, the plan is compressed. Compression removes weeks, never session content, and it cuts in a fixed order: Base first, then Build, Specific last. How far Base may be cut depends on the runner's training status, read from their recorded training history: only a runner who is already training at a build level may skip Base entirely. Every phase keeps a minimum, and the taper never falls below the floor for its distance.

Phase transitions are time-based. The adaptation engine may bring a transition forward when the runner is ready, but it can never block one, and a transition is not an escalation.

The taper is computed, not cut. Its volume comes from a fitness-fatigue model solved for the race day — the approach of model-based taper studies (Thomas & Busso, 2005) — within a hard weekly floor. Quality sessions keep their number and their intensity, and the workouts stay the ones the runner already knows, shortened. Reducing volume while keeping intensity is what the meta-analyses of tapering support (Bosquet et al., 2007; Wang et al., 2023). Escalation is suspended during the taper; the plan's envelope is frozen.

---

## 6 — Frequency Shapes the Week

How many days a runner trains changes the plan's structure, not just its size. The specification divides weekly frequency into bands, and each band has its own template, its own ceiling on quality sessions and its own spacing.

The principle behind the bands is that each session's share of the training effect changes with frequency. At low frequency there is no redundancy: each session carries a large share of the effect, so which session type it is matters most, and high-intensity work is never suppressed on frequency alone. As frequency rises, added sessions are easy first, and more quality becomes possible. The highest frequencies, with double days, are reserved for advanced runners, with the cumulative fatigue modeled.

Moving up a band requires an established training load. And a runner whose weeks alternate between more and fewer sessions gets a cycle evaluated as a whole, in which the lighter week still contains a quality session.

---

## 7 — Intensity Distribution as Weighted Exposure

The specification treats intensity distribution as an exposure model, not an ideology. Each distance and each phase has a profile — how much time goes to easy running, threshold, marathon pace, high-intensity intervals and speed — and the plan's time in each zone follows it. A strict 80/20 polarization is never enforced for its own sake. Elite endurance athletes do train mostly at low intensity (Seiler, 2010), but recreational marathon runners train with less of it, most often in a pyramidal pattern (Muniz-Pumares et al., 2025), and in a network meta-analysis no distribution model was shown to be better than the others, though the data for some models were sparse (Rosenblat et al., 2025).

The profile moves through the plan: pyramidal in Base, with more high-intensity work toward Specific. That sequence gave the largest improvement in a trial of well-trained runners, by a margin the authors place within the smallest detectable change (Filipas et al., 2022). The specification calls the progression a design choice, not a proven optimum.

Who the runner is decides which profiles are available. Pyramidal is the default at the frequencies most runners train, and a strict polarized distribution is not used there. At higher frequencies, a polarized distribution becomes available — but only to well-trained runners. In the same meta-analysis, gains in maximal oxygen uptake favoured polarized training in competitive athletes and pyramidal training in recreational ones (Rosenblat et al., 2025). A recreational runner is never given the targets of a well-trained one: for them, structure and consistency matter more than distribution precision, and a runner whose status is unknown is treated as recreational.

Two further rules protect the distribution. Each zone has a minimum effective exposure, so a zone is either trained or left out, not touched symbolically. And time accumulated in the gray zone between easy and threshold — running too hard to be easy and too easy to be quality — counts as a compliance failure, not as training.

---

## 8 — Time Caps

Many runners have a hard limit on how long a session can be. A cap in Runapp binds the whole session, warm-up included. The warm-up is kept short; the cool-down is advised, not deducted.

When a cap bites, the plan does not shorten every session by the same amount. Easy volume yields before quality: quality sessions are kept as close to their full form as the cap allows, because they carry the adaptation. The cost of a cap rises with the distance — a marathon plan loses more to the same limit than a 5K plan. For the fittest runners at the longer distances, a capped plan gets a composition of its own rather than a shortened version of the uncapped one. A time slot too short for any quality workout gets a recovery-type session instead.

---

## 9 — Progression, Deloads, and What a Plan Never Does

Within Base and Build, session durations grow week by week at a steady rate — a choice for progression, not the protection against injury, which sits on the single session (below). Specific holds its volume steady; the taper is computed (section 5).

Phases long enough get deload weeks, placed by rule. A deload keeps its quality sessions at reduced duration, and progression resumes from where it stood before the deload — the deload is not a step back.

Some limits apply to every plan: a ceiling on the long run, a cap on high-intensity sessions per week, and, for runners who train only a few days, volume grows before intensity density does. The protection against overload is set per session, not per week: no session may grow by more than a set step over the longest comparable session the runner has recently done. That is where the evidence points. In a cohort of 5,205 runners, a single session much longer than the runner's recent longest run was associated with a higher injury rate, while week-to-week change in volume showed no relationship with injury (Frandsen et al., 2025), and in a randomized trial a graded program built on the 10% rule gave novice runners no protection (Buist et al., 2008). A runner with a history of injury gets a lower per-session ceiling, because recreational runners with an injury history are about twice as likely to be injured again (Desai et al., 2021); that history is managed by the system, not declared by the runner. And missed training is never caught up by stacking it onto the following days.

---

## 10 — Personalization Within Limits

Personalization in Runapp works through the rules, never around them:

- **Level.** The measured fitness maps once, at generation, to a level that filters which workouts are eligible. The runner cannot set it. Well-trained runners above the highest level get distribution profiles and compositions of their own.
- **Age.** Above a certain age, the spacing between quality sessions is extended. Endurance performance peaks in early adulthood, declines modestly into middle age and more steeply after that, mainly through a fall in maximal oxygen uptake (Tanaka & Seals, 2008). When the week cannot hold both the spacing and the number of quality sessions, the number goes down; the spacing is never violated. The runner is not told this happened — the plan is simply theirs.
- **Training history.** A runner's recorded training status decides how far a short plan may be compressed (section 5).
- **Injury history** lowers the progression ceiling (section 9).

What personalization never does is grant authority. A plan's structure only grows through the escalation gate, on demonstrated tolerance (see *The Escalation Mechanism*). Day-level rules may change how a session is expressed; they may not rewrite the plan's structure.

---

## 11 — Starting Without History

Account and plan are separate steps. A runner can create an account, connect their health data and wearable, and stop there; the plan comes when they ask for it.

What happens next depends on whether there is history to read:

- **With enough recorded training history**, the plan is fully adaptive from the first day. There is no ramp-in.
- **Without history**, the plan starts as a static progressive plan, and the system's signals switch on one by one as their baselines accumulate — resting heart rate, for one, varies widely between people but is stable within a person, so an individual baseline says more than a population norm (Quer et al., 2020). A lower volume ceiling and a longer gate apply until they have. The runner is not told they are in a limited mode — they get a plan, and it becomes more responsive as the system learns their body. Leaving this phase never counts as an escalation.

---

## 12 — What Is Measured

What this paper describes is plan design as Runapp's specification defines it.

- **The plans are measured in simulation.** Runapp's plans are run through a simulator against populations of synthetic runners and scored with a plan-quality metric of its own, alongside plans from books, apps and published intervention studies. Those measurements, their method and their caveats are published separately, in *Runapp Validation*. They measure plans as prescribed, under a physiological model — not outcomes in runners.
- **No field data.** Runapp is not yet released. No runner has followed a Runapp plan to a race, so there is no measurement of how its plans perform in the field.

---

## 13 — Why This Design Is Unusual

A template scaled by experience starts from what the runner says and fits the runner's week to it afterwards. Runapp starts from a measurement and from the runner's actual week, and derives everything else by rule.

Three properties follow. The plan is **reproducible**: the same measurement, goal and week give the same plan, every time, and a change to a rule changes every plan the same way. The plan is **structural**: frequency, time caps, age and fitness change the shape of the plan, not only its size. And the plan is **bounded**: personalization works inside rules that protect the runner — the gray zone counts as a failure, missed training is never stacked, recreational runners never get elite targets — and structure grows only on demonstrated tolerance.

We have not surveyed the market systematically, and make no claim that the design is unique.

---

## 14 — Limitations

- **The specification is the subject.** Runapp is in development. This paper describes plan design as the specification defines it. The engine implements the specification in stages; where it does not yet match, the specification governs and the engine is changed.
- **The specification is itself under review.** Where two of its rules are found to disagree, the conflict is resolved in the specification — never by what the code happens to do. This paper states only what the rules agree on and leaves contested details out.
- **The distribution progression is a design choice.** Moving from pyramidal toward more high-intensity work across the plan is chosen, not proven optimal.
- **Six distances.** Plans exist for 3K through marathon. Ultra distances and track events below 3K are not specified.
- **Simulation is not validation.** The simulator measures plans under a physiological model that Runapp's own plans were optimized against; the companion validation repository states that asymmetry in full.

---

## 15 — Sources

The works cited in this paper (bibliographic details checked against the publisher):

- Bosquet, L. et al. (2007). Effects of tapering on performance: A meta-analysis. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/mss.0b013e31806010e0
- Buist, I. et al. (2008). No effect of a graded training program on the number of running-related injuries in novice runners: A randomized controlled trial. *American Journal of Sports Medicine*. https://doi.org/10.1177/0363546507307505
- Cooper, K.H. (1968). A means of assessing maximal oxygen intake: Correlation between field and treadmill testing. *JAMA*. https://doi.org/10.1001/jama.1968.03140030033008
- Desai, P. et al. (2021). Recreational runners with a history of injury are twice as likely to sustain a running-related injury as runners with no history of injury: A 1-year prospective cohort study. *Journal of Orthopaedic & Sports Physical Therapy*. https://doi.org/10.2519/jospt.2021.9673
- Filipas, L. et al. (2022). Effects of 16 weeks of pyramidal and polarized training intensity distributions in well-trained endurance runners. *Scandinavian Journal of Medicine & Science in Sports*. https://doi.org/10.1111/sms.14101
- Frandsen, J. et al. (2025). How much running is too much? Identifying high-risk running sessions in a 5200-person cohort study. *British Journal of Sports Medicine*. https://doi.org/10.1136/bjsports-2024-109380
- Mayorga-Vega, D. et al. (2016). Criterion-related validity of the distance- and time-based walk/run field tests for estimating cardiorespiratory fitness: A systematic review and meta-analysis. *PLoS ONE*. https://doi.org/10.1371/journal.pone.0151671
- Molina-Garcia, P. et al. (2022). Validity of estimating the maximal oxygen consumption by consumer wearables: A systematic review with meta-analysis and expert statement of the INTERLIVE Network. *Sports Medicine*. https://doi.org/10.1007/s40279-021-01639-y
- Mujika, I. & Padilla, S. (2000). Detraining: Loss of training-induced physiological and performance adaptations. Part II: Long term insufficient training stimulus. *Sports Medicine*. https://doi.org/10.2165/00007256-200030030-00001
- Muniz-Pumares, D. et al. (2025). The training intensity distribution of marathon runners across performance levels. *Sports Medicine*. https://doi.org/10.1007/s40279-024-02137-7
- Quer, G. et al. (2020). Inter- and intraindividual variability in daily resting heart rate and its associations with age, sex, sleep, BMI, and time of year: Retrospective, longitudinal cohort study of 92,457 adults. *PLoS ONE*. https://doi.org/10.1371/journal.pone.0227709
- Rosenblat, M.A. et al. (2025). Which training intensity distribution intervention will produce the greatest improvements in maximal oxygen uptake and time-trial performance in endurance athletes? A systematic review and network meta-analysis of individual participant data. *Sports Medicine*. https://doi.org/10.1007/s40279-024-02149-3
- Scudamore, E.M., Barry, V.W. & Coons, J.M. (2018). An evaluation of time-trial–based predictions of V̇O₂max and recommended training paces for collegiate and recreational runners. *Journal of Strength and Conditioning Research*. https://doi.org/10.1519/JSC.0000000000001942
- Seiler, S. (2010). What is best practice for training intensity and duration distribution in endurance athletes? *International Journal of Sports Physiology and Performance*. https://doi.org/10.1123/ijspp.5.3.276
- Smyth, B. & Muniz-Pumares, D. (2020). Calculation of critical speed from raw training data in recreational marathon runners. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0000000000002412
- Tanaka, H. & Seals, D.R. (2008). Endurance exercise performance in masters athletes: Age-associated changes and underlying physiological mechanisms. *Journal of Physiology*. https://doi.org/10.1113/jphysiol.2007.141879
- Thomas, L. & Busso, T. (2005). A theoretical study of taper characteristics to optimize performance. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/01.mss.0000177461.94156.4b
- Wang, Z. et al. (2023). Effects of tapering on performance in endurance athletes: A systematic review and meta-analysis. *PLoS ONE*. https://doi.org/10.1371/journal.pone.0282838

---

## 16 — Map

- [Runapp](../README.md) — what Runapp is and how its parts fit together.
- [The Escalation Mechanism](escalation.md) — how the plan's structure grows, and how it is protected.
- [The Adaptation Engine](adaptation.md) — how the plan is modified day to day once it runs.
- [The Motivation Architecture](motivation.md) — how the plan is presented to the runner.
- [Evidence Register: Plan Design](../reference/plan-design-evidence-register.md) — the works this paper cites, and where.
- [Runapp Validation](https://github.com/jonasmaandiwallin/runapp-validation) — the simulator and the plan-quality metric that measure Runapp's plans.
