---
title: "The Adaptation Engine: Continuous Plan Modification Within Deterministic Constraints"
type: reference
status: draft
version: "0.2"
date: "2026-11-21"
author: "Tooling & Architecture"
audience: "External — no IP disclosure. Readable without NDA."
reading_order: 4
package: runapp
release: public
framework:
  name: "Governed Agentic Engineering (GAE)"
  originator: "Jonas Maandi Wallin"
editor: "Jonas Maandi Wallin"
contact: "https://www.linkedin.com/in/jonasmaandiwallin"
author_llm:
  - model: null
    model_id: null
    roles: [drafting]
    source: not-established
  - model: "Claude Opus 5.5"
    model_id: "claude-opus-5-5"
    roles: [technical-analysis, editing]
    source: platform
ai_disclosure:
  - role: drafting
    status: complete
    scope: "Text and structure of version 0.1 (2026-09-02), written by the Tooling & Architecture agent from Runapp's specification. The model used for that version is not recorded."
    evidence: "The document's own text."
    reason: null
  - role: technical-analysis
    status: complete
    scope: "Version 0.2: every statement about how the adaptation engine is specified read against the governing rules of Runapp's specification. The engine and the iOS app were read as well; the paper makes no claim about what they build."
    evidence: "The release fact-check report of 2026-11-03, one row per statement with the rule it was read against and the verdict."
    reason: null
  - role: editing
    status: complete
    scope: "Version 0.2: statements the specification does not bear corrected to the specification — the adaptation engine's share in structural progression, the timing of the day-of decision, adherence targets adjusted rather than zones, the reason for separate drift thresholds, the decay floor as a floor on expectation, where added plan time goes, and the role of a language model in wording; a week range that pointed at a parameter removed; sections on what is measured, positioning, limitations and a map added to the white-paper template. Inline citations to the research each principle rests on, a limitation on heart-rate variability as a signal, and a sources section added on 2026-11-03, worded from the registered key findings."
    evidence: "The diff against version 0.1."
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
path: "whitepapers/adaptation.md"
---

# The Adaptation Engine: Continuous Plan Modification Within Deterministic Constraints

*How Runapp modifies a training plan in real time — and why the modification never violates the plan's own rules.*

---

## 1 — The Plan Is Not Static

A training plan, as most platforms deliver it, is a fixed artifact. You get your schedule. You follow it. If you miss a session, you figure out what to do. If you feel terrible on Tuesday, you decide whether to push through or skip. If your goal race moves, you rearrange things manually or start over.

Runapp's plan is not fixed. It is a living document that responds to the runner's body and life in real time, modifying itself on the day of execution based on physiological signals. But the modification is not freeform — it operates within strict deterministic constraints. The plan can change what today looks like. It cannot change what kind of plan it is.

This distinction is the central architectural decision of the adaptation engine. The system operates at two clearly separated levels: *structural* decisions (what the plan prescribes across weeks) and *tactical* decisions (what happens on any given day). The adaptation engine makes tactical decisions on its own. It also advances structural progression, but a structural step happens only when the escalation gate — described in the companion white paper — authorizes it. Both share a philosophy: evidence-gated aggression within deterministic constraints. They differ in timescale, authority, and scope.

---

## 2 — Two Kinds of Modification

The adaptation engine performs two fundamentally different operations, and the architecture keeps them strictly separated:

**Session modification** is a day-of-execution decision. Before the runner starts their scheduled session, the system evaluates physiological signals and determines whether the planned session type and volume are appropriate given the runner's current state. If they are not, the system modifies the session — substituting a different session type, reducing the volume, or in extreme cases recommending rest. This happens shortly before the session is due to start, using the most current signal data.

**Structural progression** is a multi-week decision. Over time, as the runner demonstrates tolerance under repeated exposure, the system may increase volume ceilings, introduce harder session categories, or raise intensity limits. The adaptation engine measures, tracks patterns, and flags eligibility; the escalation gate decides whether the step is taken.

The separation matters because the failure modes are different. A bad tactical decision — substituting a session the runner could have completed — wastes one day of training. A bad structural decision — escalating too aggressively — risks non-functional overreaching, which can take weeks to months to recover from (Meeusen et al., 2013), or injury from a load spike (Gabbett, 2016). The system applies correspondingly different evidence standards: tactical modifications require current-state assessment, structural decisions require sustained multi-session stability.

---

## 3 — The Day-Of Decision: Session Modification

When a scheduled session approaches, the system evaluates whether the runner's body is ready for what the plan prescribes. Two questions are decided: whether the planned type of session is one the body can take today, and how much of it.

**Readiness — the session type.** The system checks whether the planned session type is physiologically accessible. The primary signal is heart rate variability. The approach follows HRV-guided training: trials that prescribed hard sessions unless HRV had dropped meaningfully below the runner's own reference, with easy training or rest otherwise, improved performance as much as or more than fixed plans while using fewer hard sessions (Kiviniemi et al., 2007; Vesterinen et al., 2016). When HRV is suppressed below the runner's individual baseline, the system therefore substitutes a lower-intensity session type: typically an easy aerobic run.

When HRV is severely suppressed, rest is considered, as HRV-guided protocols do when HRV falls further (Kiviniemi et al., 2007).

**Volume — how much of it.** When the planned session type is accessible, the system checks whether the runner should complete the full prescribed volume. Sub-optimal readiness indicators (trending autonomic signals, adaptive capacity state) may indicate that the runner would benefit from a shorter session at the same intensity. The principle comes from research on reduced training: when training is cut back, intensity is the main determinant of whether adaptations are kept, and volume can fall substantially if intensity is held (Hickson et al., 1985; Spiering et al., 2021). Holding intensity while cutting duration keeps maximal oxygen uptake and short-term endurance, though not always long-duration endurance (Hickson et al., 1982).

Shortening has a floor. A hard session that is cut too short stops being the session it was: an interval session, for example, is thought to need at least several minutes near maximal oxygen uptake to give an optimal stimulus (Buchheit & Laursen, 2013). Below that, the runner gets a different session type rather than a truncated one.

A shorter version is never a way around a session type the body cannot take today. That is a structural requirement, not a suggested workflow.

---

## 4 — What the Runner Sees

When the system modifies a session, the runner sees the modified session as their session for the day. The original planned session is not displayed. There is no "we changed your workout" notification, no before-and-after comparison, no opt-in prompt.

This is a deliberate design decision, and its reasoning is the design's own: showing the original invites the runner to compare, and to read the modification as a downgrade — something they lost rather than something the system did for them. The cognitive load of evaluating "should I override the system?" defeats the purpose of having a system make the decision.

The runner can always override. The mechanism for user control exists — the runner can choose to do something different. But the system does not invite the override. It decides, presents, and moves on. If the signals support the modification, the modification is the right session.

When the modified session is completed, it counts as a full completion in all tracking and progress metrics. There is no distinction between a modified session and an unmodified one in the runner's history. A completed modified session maintains the behavioral chain — the runner trained today, the system adapted to their state, and training continues tomorrow. No gap, no failure, no missed day.

---

## 5 — Tracking How the Runner Actually Trains

The adaptation engine does not only modify what is prescribed — it measures how well the runner executes what was prescribed. After each session, the system measures zone adherence: what fraction of the session time was spent in the prescribed training zone.

This measurement is objective. Heart rate data from the runner's wearable provides the primary signal, with pace as a secondary input; in recreational runners, prescribing by heart rate kept them in the intended zone more of the time than prescribing by race pace (Ranieri et al., 2026). The system converts the raw measurement to an adherence score per zone and tracks it across sessions.

Before any adherence measurement is used for adaptive decisions, a confidence gate evaluates the underlying data quality. Sessions where heart rate data is absent, incomplete, or affected by sensor artifacts are flagged as low-confidence and excluded from adaptive calculations; wrist-worn heart-rate sensors are markedly less accurate during activity than at rest (Bent et al., 2020). The system does not make decisions on bad data.

**Drift Detection.** When a runner consistently fails to hit the prescribed zone across multiple consecutive sessions of the same type, the system recognizes a pattern — zone drift. The drift thresholds are different for easy sessions and quality sessions, because time in the prescribed zone is lower in high-intensity sessions than in easy ones, whichever way the intensity is prescribed (Ranieri et al., 2026). Drift across different session types is tracked independently: a runner who consistently runs easy sessions too hard is a different pattern from a runner who consistently falls short on quality sessions.

**Adaptive Target Adjustment.** Confirmed drift adjusts the session's adherence target in capped steps. The zone itself stays the same; what moves is how much of the session the plan expects the runner to spend in it, so that the expectation comes closer to what the runner actually executes.

These adjustments lean one way by design: the system is more willing to ease the demand than to raise it. That direction is consistent with research on injury in novice runners, where higher running intensity was associated with higher injury risk (Kluitenberg et al., 2016).

**Recovery.** When adherence normalizes — the runner hits the prescribed zones consistently again — the system gradually recovers the target toward the plan's nominal values. A runner who demonstrates normalized adherence across several consecutive sessions has proven the point — holding the corrected target longer than necessary degrades the training experience without additional safety benefit.

---

## 6 — Long Plans and Adherence Decay

Training plans that span many weeks face a predictable challenge: intensity adherence degrades over time. Runners who hit their zones precisely in the early weeks begin to drift late in a long plan. This is expected behavior over a long plan, not a compliance failure.

The adaptation engine models this explicitly. After a defined onset point in the plan, the system gradually widens the acceptable tolerance margin for zone adherence. The baseline targets do not change — the system still prescribes the same zones — but it expects and accommodates a natural widening of execution variance as the plan progresses.

This decay model has a hard floor. Tolerance cannot widen indefinitely: the expected adherence is never allowed to fall below the floor. The floor ensures that the decay model is a realistic accommodation, not a permission to drift without limit.

When adherence decay is active and the runner simultaneously shows signs of systemic instability — autonomic markers trending negative — the system initiates a proactive session downgrade. Decay alone is tolerated. Decay combined with physiological stress is not.

---

## 7 — When Life Disrupts the Schedule

Runners miss sessions. They reschedule workouts. Their Tuesday run becomes a Thursday run because of a meeting. The adaptation engine handles these disruptions through local reoptimization — a dynamic rescheduling mechanism that operates within a rolling time window.

When a session is moved or missed, the system does not simply leave a gap and continue. It reoptimizes the remaining sessions within a defined window to maximize the weekly training stimulus given the new reality. The core principle: the runner always sees a complete, executable schedule. No gaps, no blocking warnings, no "you should have done this yesterday" messages.

The reoptimization respects physiological spacing constraints — minimum recovery time between quality sessions, minimum gap between threshold and VO₂max sessions — but treats them as optimization inputs, not hard blocks. If a quality session cannot be placed within the constraints, it converts to an easy session rather than being dropped. The runner stays active, the training week retains as much of its intended stimulus as the new schedule allows, and the following week returns to the original plan structure.

When quality sessions must be converted, the system keeps the high-intensity session and converts the threshold session. In work-matched trials, high-intensity intervals improved maximal oxygen uptake where training at moderate intensity did not (Helgerud et al., 2007).

---

## 8 — When the Goal Moves

Goal-date changes are a common disruption — the runner's target race moves forward or back, or they choose a new goal date. The adaptation engine decides by rule whether the existing plan can absorb the change or must be regenerated from scratch.

**The taper and the phases are protected.** A new date that would leave the taper, or any phase, too short to serve its purpose regenerates the plan. Taper length matters for race performance: in a meta-analysis of tapering, and in a large observational study of recreational marathon runners, how long and how strict the taper was went with how well the race went (Bosquet et al., 2007; Smyth & Lawlor, 2021). No recalculation can fix an insufficient taper, and each phase has a purpose that a phase cut too short no longer serves.

**Small shifts are absorbed, large ones regenerate.** Within those limits, a small shift is absorbed by recalculation — added time goes to the base phase — and a large one regenerates the plan. How large a shift is depends on how much of the plan remains, not on its original length.

Lengthening is tolerated more broadly than shortening. Adding time to a plan is structurally safe — extra days are absorbed by the base phase, the most elastic part of the plan. Removing time compresses against hard phase-floor constraints and is correspondingly more likely to require full regeneration.

---

## 9 — The Offensive Bias in Adaptation

The adaptation engine shares the escalation mechanism's offensive posture: when signals are strong, the system pushes harder. But it expresses the offense differently.

In the escalation mechanism, offense means structural expansion — higher ceilings, harder session categories, more demanding training. In the adaptation engine, offense means execution sharpening — pushing to the upper end of prescribed intensity bands, reinforcing interval quality, favoring the harder variant within the current structural envelope.

The adaptation engine's offensive bias is bounded by the same constraints that bind the escalation mechanism. When signals are negative, the system brakes — it does not ride out bad signals hoping they will improve. When a protective contract activates, the adaptation engine respects the protection unconditionally. The offensive posture is always gated by the evidence. Aggression without evidence is recklessness.

---

## 10 — Controlled Aggression Within Constraints

The phrase that best describes the adaptation engine's operating philosophy is *controlled aggression within deterministic constraints*. Every word matters:

**Controlled** — every modification is decided by deterministic rules. No randomness, no neural network inference, no black-box decisions. The same signals produce the same modification every time. A language model may word how a session is presented to the runner, but it never makes or changes the decision.

**Aggression** — the system's default is to push forward. It does not hold runners back when the data supports harder training. Rest is never the automatic choice. It substitutes, adjusts, and sharpens — always in the direction of maintaining the highest possible training stimulus compatible with the runner's current state.

**Within** — the adaptation engine does not escape its constraints. It cannot escalate structure. It cannot override protective contracts. It cannot modify the plan's architectural properties. It operates inside a box defined by the escalation mechanism, the arbitration hierarchy, and the safety contracts.

**Deterministic** — the rules are auditable, traceable, and reproducible. Every modification traces to a signal evaluation, a gate decision, and a rule in the contract system. A skeptical reader can follow the logic from "your session was modified" back to the specific physiological signal that triggered the modification and the specific rule that governed the response.

**Constraints** — the system's boundaries are not arbitrary limits. They are the structural conditions that make the offensive posture safe. Without the escalation gate, the adaptation engine could not push — it would risk escalating into injury. Without the protective contracts, it could not substitute — it would have no authority structure to fall back on. The constraints enable the aggression. Remove the constraints and you do not get a more aggressive system — you get a dangerous one.

---

## 11 — The Full Adaptive Loop

The escalation mechanism and the adaptation engine together form a complete adaptive loop:

The adaptation engine operates within the current structural envelope — modifying sessions, tracking adherence, adjusting targets, handling disruptions. It measures how the runner responds to training and feeds that evidence to the escalation mechanism.

The escalation mechanism evaluates whether the structural envelope should change — expanding when the evidence shows sustained tolerance, contracting when the evidence shows intolerance. It sets the boundaries within which the adaptation engine operates.

Neither mechanism is complete without the other. An escalation mechanism without tactical adaptation produces a rigid plan that cannot respond to daily variation. An adaptation engine without structural escalation produces a plan that never gets harder, regardless of how well the runner is doing.

The combination is what makes the system genuinely adaptive: responsive in the short term and progressive in the long term, tactical on any given day and strategic across weeks. And at every timescale, the same principle applies: the system does what the evidence supports, nothing more, nothing less.

---

## 12 — What Is Measured

What this paper describes is the adaptation engine as Runapp's specification defines it. Its outcomes have not been measured.

- **No field data.** Runapp is not yet released. There is no measurement of how often sessions are modified, whether modified sessions preserve the training effect the design expects, or how drift correction changes adherence over a plan.
- **Not in the simulator.** The simulator that measures Runapp's plans models how faithfully a population of runners follows a plan, sampled per runner. It does not model the day-of gates, drift correction, rescheduling or goal-date changes: it evaluates plans as prescribed.
- **The evidence behind the design** — the works cited here are in section 15 and in the companion evidence register, which says where each is cited. The citations show the principle each part of the design rests on; neither the paper nor the register says which value a study sets.

---

## 13 — Why This Design Is Unusual

A fixed plan leaves every adaptation to the runner: a missed session, a bad night or a moved race is theirs to resolve. A plan that rescales sessions without separating the day from the season mixes two decisions with different costs, and applies one evidence standard to both.

Runapp keeps the two apart. Day-level changes are made on the runner's current state and stay inside the plan's structure; structural changes need sustained evidence and the escalation gate. Every modification traces to a signal, a gate and a rule, and none changes what kind of plan it is. As with the escalation mechanism, we have not surveyed the market systematically and make no claim that the design is unique.

---

## 14 — Limitations

- **Heart rate is the basis of adherence.** Sessions without reliable heart-rate data are excluded from adaptive decisions rather than estimated. A runner who trains without a heart-rate sensor gets fewer adaptive decisions, not worse ones.
- **Heart-rate variability is an imperfect signal.** Overreaching can show as *elevated* rather than suppressed variability (Le Meur et al., 2013), subjective measures can be more sensitive to changes in load (Nuuttila et al., 2024), and a meta-analysis found no pooled advantage of HRV-guided training for maximal oxygen uptake or endurance performance (Manresa-Rocamora et al., 2021). The day-of gate reads variability alongside the runner's other signals, not alone; how much it adds is not measured.
- **The decay model is set for the population.** When adherence decay begins and how fast it widens are the same for every runner; the model does not learn an individual's pattern.
- **Specified, not yet shipped.** Runapp is in development. This paper describes the adaptation engine as the specification defines it. The engine implements the specification in stages; where it does not yet match, the specification governs and the engine is changed. The specification is itself under review, and where two of its rules are found to disagree, the conflict is resolved in the specification — never by what the code happens to do.

---

## 15 — Sources

The works cited in this paper (bibliographic details checked against the publisher), also listed in the companion evidence register:

- Bent, B. et al. (2020). Investigating sources of inaccuracy in wearable optical heart rate sensors. *npj Digital Medicine*. https://doi.org/10.1038/s41746-020-0226-6
- Bosquet, L. et al. (2007). Effects of tapering on performance: A meta-analysis. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/mss.0b013e31806010e0
- Buchheit, M. & Laursen, P.B. (2013). High-intensity interval training, solutions to the programming puzzle: Part I: Cardiopulmonary emphasis. *Sports Medicine*. https://doi.org/10.1007/s40279-013-0029-x
- Gabbett, T.J. (2016). The training-injury prevention paradox: should athletes be training smarter and harder? *British Journal of Sports Medicine*. https://doi.org/10.1136/bjsports-2015-095788
- Helgerud, J. et al. (2007). Aerobic high-intensity intervals improve VO2max more than moderate training. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/mss.0b013e3180304570
- Hickson, R.C. et al. (1982). Reduced training duration effects on aerobic power, endurance, and cardiac growth. *Journal of Applied Physiology*. https://doi.org/10.1152/jappl.1982.53.1.225
- Hickson, R.C. et al. (1985). Reduced training intensities and loss of aerobic power, endurance, and cardiac growth. *Journal of Applied Physiology*. https://doi.org/10.1152/jappl.1985.58.2.492
- Kiviniemi, A.M. et al. (2007). Endurance training guided individually by daily heart rate variability measurements. *European Journal of Applied Physiology*. https://doi.org/10.1007/s00421-007-0552-2
- Kluitenberg, B. et al. (2016). The NLstart2run study: Training-related factors associated with running-related injuries in novice runners. *Journal of Science and Medicine in Sport*. https://doi.org/10.1016/j.jsams.2015.09.006
- Le Meur, Y. et al. (2013). Evidence of parasympathetic hyperactivity in functionally overreached athletes. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0b013e3182980125
- Manresa-Rocamora, A. et al. (2021). Heart rate variability-guided training for enhancing cardiac-vagal modulation, aerobic fitness, and endurance performance: A methodological systematic review with meta-analysis. *International Journal of Environmental Research and Public Health*. https://doi.org/10.3390/ijerph181910299
- Meeusen, R. et al. (2013). Prevention, diagnosis, and treatment of the overtraining syndrome: Joint consensus statement of the European College of Sport Science and the American College of Sports Medicine. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0b013e318279a10a
- Nuuttila, O.-P. et al. (2024). Monitoring fatigue state with heart rate-based and subjective methods during intensified training in recreational runners. *European Journal of Sport Science*. https://doi.org/10.1002/ejsc.12115
- Ranieri, L.E. et al. (2026). Comparison of heart rate- and race pace-based training prescriptions on training execution accuracy in recreational distance runners. *International Journal of Sports Physiology and Performance*. https://doi.org/10.1123/ijspp.2025-0578
- Smyth, B. & Lawlor, A. (2021). Longer disciplined tapers improve marathon performance for recreational runners. *Frontiers in Sports and Active Living*. https://doi.org/10.3389/fspor.2021.735220
- Spiering, B.A. et al. (2021). Maintaining physical performance: the minimal dose of exercise needed to preserve endurance and strength over time. *Journal of Strength and Conditioning Research*. https://doi.org/10.1519/JSC.0000000000003964
- Vesterinen, V. et al. (2016). Individual endurance training prescription with heart rate variability. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0000000000000910

---

## 16 — Map

- [Runapp](../README.md) — what Runapp is and how its parts fit together.
- [The Escalation Mechanism](escalation.md) — the protection system and the escalation gate that set the envelope this engine works inside.
- [The Motivation Architecture](motivation.md) — how modified sessions and disruptions are communicated to the runner.
- [Evidence Register: The Adaptation Engine](../reference/adaptation-evidence-register.md) — the literature behind the design.
- [Runapp Validation](https://github.com/jonasmaandiwallin/runapp-validation) — the simulator and the plan-quality metric that measure Runapp's plans.
