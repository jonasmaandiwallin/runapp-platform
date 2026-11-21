---
title: "The Escalation Mechanism: Adaptive Protection Through Independent Domain Composition"
type: reference
status: draft
version: "0.2"
date: "2026-11-21"
author: "Tooling & Architecture"
audience: "External — no IP disclosure. Readable without NDA."
reading_order: 3
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
    scope: "Version 0.2: every statement about how the mechanism is specified read against the governing rules of Runapp's specification. The engine and the iOS app were read as well; the paper makes no claim about what they build."
    evidence: "The release fact-check report of 2026-11-03, one row per statement with the rule it was read against and the verdict."
    reason: null
  - role: editing
    status: complete
    scope: "Version 0.2: statements the specification does not bear corrected to the specification — the per-domain states replace a five-colour model, BandAid described as a runner-set flag rather than a state, tiers no longer said to be completed in strict sequence, the precedence order summarised rather than restated, good conditions scoped to execution intensity, the domain separation stated as a rule rather than as a property of the code; the claim of uniqueness and an unsupported figure removed; sections on what is measured and a map added to the white-paper template. Inline citations to the research each principle rests on and a sources section added on 2026-11-03, worded from the registered key findings; two sentences the literature does not bear (that holding back is waste, that the hardest sessions carry the highest cost) reworded to what the cited works state."
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
path: "whitepapers/escalation.md"
---

# The Escalation Mechanism: Adaptive Protection Through Independent Domain Composition

*How Runapp decides when a runner is ready to train harder — and why a single readiness number is the wrong tool for that decision.*

---

## 1 — The Problem with Readiness Scores

A common design reduces the question "how is this runner doing?" to a single number. A recovery score. A readiness percentage. A colored ring. The score aggregates heart rate variability, sleep, training load, and sometimes perceived effort into one value, and that value drives one decision: go or rest.

This is intuitive. It is also architecturally wrong.

A single readiness score conflates fundamentally different physiological systems. Different heart-rate measures may each reflect a different aspect of adaptation, so no single one is enough on its own (Buchheit, 2014), and heart-rate variability after exercise and heart-rate recovery can rise both with good adaptation and with overreaching, so they cannot be read alone (Bellenger et al., 2016). A runner with a strained Achilles tendon and perfect cardiovascular markers gets the same "yellow" as a runner with excellent tissue tolerance but several nights of poor sleep in a row. Both are told to ease off. Neither is told *why*, because the aggregation erased the reason.

Worse, a single score invites compensation. If the cardiovascular signal is strong enough, it can offset a musculoskeletal warning — the composite score stays green while the knee deteriorates. The system cannot distinguish between "everything is fine" and "one thing is very good and another thing is very bad," because it threw away the distinction before making the decision.

Runapp takes a different approach. It shows the runner no readiness score, and no single number can authorize progression. It maintains three independent domain states — one per physiological system — and resolves decisions across them without collapsing them into one. The result is a protection system that knows *where* the problem is, not just *that* there is one.

---

## 2 — Three Domains, Not One Score

Runapp's escalation mechanism operates across three independent domains, each monitoring a distinct physiological system:

**Systemic** — cardiovascular and metabolic health. Heart rate variability trends, resting heart rate, aerobic drift, cumulative training load markers. This domain answers the question: is the runner's cardiovascular system absorbing the training stimulus? A disturbance can show in either direction: overreaching has been observed with *elevated* heart-rate variability (Le Meur et al., 2013).

**Mechanical** — musculoskeletal tolerance. Impact exposure, cadence patterns, movement quality, and the runner's own report of localized discomfort. This domain answers: are the runner's bones, tendons, and muscles handling the mechanical stress, or are they approaching their limit? Cadence is part of it because it changes joint loading: in a small pilot study, a higher step rate acutely lowered the peak load at the knee (Hafer et al., 2015).

**Central nervous system** — neurological and cognitive load. Accumulated sleep debt, sympathetic load markers, and sustained negative subjective state. This domain answers: is the runner mentally and neurologically capable of absorbing high-quality training, or are they running on fumes? Sleep restriction accumulates: nights cut short, night after night, impaired attention and working memory as much as a night or two without sleep, while the person's own sense of sleepiness levelled off after the first days (Van Dongen et al., 2003). And subjective measures can be more sensitive to changes in training load than heart-rate-based ones (Nuuttila et al., 2024).

Each domain maintains its own state. Each evaluates its own signals. Each has its own thresholds, its own stability criteria, and its own expiration logic. They do not vote. They do not negotiate. They do not average.

This separation is a rule of the specification, not a design nicety. The three domains may not be collapsed into a composite readiness score, a weighted average, or a unified metric, and the runner is never shown one. Stability in one domain does not offset instability in another. A runner with excellent cardiovascular signals cannot compensate for mechanical fragility. Perfect sleep cannot override systemic suppression.

---

## 3 — States Within Each Domain

Each domain grades its own condition on a short scale of its own, from stable, through caution, to restriction:

**Systemic** — stable, fragile, or suppressed.

**Mechanical** — unrestricted, monitoring, or restricted. The mechanical state also sets the envelope a session may use: under restriction, the hardest intensities are removed from the session.

**Central nervous system** — baseline, volatile, or depleted.

The states are internal. The runner never sees them as labels; they see the session the states allowed.

State changes are deterministic and rest on patterns. Heart-rate variability, for one, is read as an average over several days, not as a single morning; in trained triathletes, a few recordings spread across the period reproduced the average of the daily recordings (Plews et al., 2014). A single strong session does not restore stability — that requires repeated demonstration, not a momentary spike. A single bad night does not confirm instability — concern grows from a pattern, not from an isolated event.

The asymmetry between entering and leaving protection is deliberate. Protection is entered quickly: the escalation gate closes as soon as a protective rule applies. It is released slowly: authorization to progress returns only after stability has been shown across consecutive sessions. Quick to protect, slow to release.

BandAid — the runner's own signal that something mechanical is wrong — is not one of these states. It is a flag the runner sets, and it feeds the mechanical domain (section 7).

---

## 4 — The Offensive Default

Most training platforms use physiological data defensively. Recovery scores suggest rest. Readiness metrics recommend lighter sessions. The default posture is conservative: if in doubt, hold back.

Runapp inverts this.

The system's default posture is offensive. Absence of negative signals is permission to advance. When all three domains are stable and the runner has demonstrated stability across consecutive sessions, the system pushes forward: volume ceilings increase, intensity sharpens, session difficulty rises. The plan gets harder — not because the runner asked for it, but because the data shows they can tolerate it.

This is not recklessness. The offensive default is paired with the architecture's protection mechanisms. The same mechanism that pushes when signals are strong brakes hard when signals deteriorate. The relationship between offense and defense is not a tradeoff — it is a design: the protection system exists precisely to enable the offensive posture. Without the independent domain monitoring, the graduated escalation tiers, and the deterministic state transitions, the offensive default would be dangerous. With them, it is the architecturally correct behavior.

The philosophical claim is simple: passivity requires justification. A runner who is sleeping well, whose cardiovascular system is absorbing load, whose musculoskeletal system shows no stress markers, and who has demonstrated tolerance under repeated exposure — that runner should be challenged. Holding them back has its own cost: non-response to endurance training falls as the training dose rises (Montero & Lundby, 2017), and in team sports, a high chronic load reached through graded progression has been associated with fewer injuries, with rapid spikes rather than high load itself as the main problem (Gabbett, 2016).

Good conditions — favorable weather, strong physiological signals, high sleep quality — do not merely permit the plan as written. They raise the intensity at which a session may be executed, within the structure the plan already has. The system does not say "things look good, so proceed as planned." It says "things look good, so execute this session at the upper end of what it allows." Enlarging the structure itself is a separate decision, taken only by the escalation gate (section 5).

---

## 5 — Earning Escalation

Escalation — the structural authorization to increase training load — is not a reward. It is not triggered by a single excellent workout. It is not triggered by the runner's enthusiasm, motivation, or expressed desire for more. It is earned through repeated, demonstrated tolerance. In a cohort of 5,205 runners, a single run much longer than the runner's recent longest was associated with a higher injury rate, rising with the size of the jump, while weekly load ratios were not (Frandsen et al., 2025).

The system distinguishes three tiers of escalation, each requiring progressively stronger stability evidence:

**Tier 1 — Micro escalation.** Minor volume increase within the current intensity category. No change in session type or intensity ceiling. This is the smallest structural step: a slightly longer long run, a marginally increased weekly total. It requires confirmed stability across all three domains, but the stability window is short.

**Tier 2 — Structural escalation.** Intensity ceiling increase. Introduction of harder session variants. Longer duration at higher effort. This changes the character of the training, not just the volume. It requires a longer stability window and stronger cross-domain evidence.

**Tier 3 — High-stress exposure.** Introduction of the most demanding session types: VO₂max intervals, race simulations, peak-specific loading. In a trial that compared high-intensity intervals with long slow distance and threshold running, the intervals gave the largest gain in maximal oxygen uptake (Helgerud et al., 2007). They require the strongest stability evidence: extended multi-domain stability with no protective contract active.

The graduated structure means a runner cannot jump from a first easy run to race-pace intervals, regardless of how strong a single session feels: each tier asks for stronger and longer stability evidence than the one below it.

If a protective rule activates in any domain, any pending authorization is invalidated, and the evidence for the next tier has to be shown again. The runner does not lose fitness or undo training. They lose *authorization* for the next tier. The system re-evaluates from the demonstrated state, not from the desired state.

---

## 6 — De-escalation: The Symmetric Counterpart

De-escalation is the structural reduction of training load: lower volume ceilings, reduced intensity limits, simpler session categories. It is the mirror image of escalation, governed by the same authority and the same logic.

De-escalation is not punishment. It is not a penalty for missing workouts or reporting discomfort. It is the system's structural response to demonstrated intolerance — the evidence shows the runner cannot sustain the current load level, so the load level adjusts downward.

De-escalation is triggered by protective contract activation: when a safety mechanism engages in a domain, any pending escalation authorization is immediately invalidated. It is also triggered by patterned regression — not a single bad session, but a sustained deterioration across the stability window. In the consensus definition, overreaching is a short-term drop in performance that recovers within days to weeks; a drop that lasts weeks to months marks non-functional overreaching or the overtraining syndrome (Meeusen et al., 2013). And it is triggered by escalation-induced oscillation: if a recent tier upgrade caused load to bounce between sessions rather than stabilize, the upgrade is treated as premature and de-escalated.

The key architectural property is that de-escalation follows the same precedence hierarchy as escalation. Protective contracts — the highest-priority layer in the system — take precedence over progression, and the escalation gate cannot override them. This asymmetry is intentional: entering protection is faster than leaving it, and protection always wins when it conflicts with progression.

---

## 7 — BandAid: The Runner-Controlled State

BandAid is not a physiological state. It is a flag that only the runner can set and only the runner can clear; the system cannot raise or remove it on its own.

BandAid represents a declared mechanical vulnerability — a twinge in the Achilles, a sore knee, a tight hamstring. It is not a diagnosis. The system does not interpret the nature or severity of the issue. It takes the runner's declaration at face value and applies structural constraints.

When BandAid is active, the system restricts to a protected envelope: only marginal volume extension is permitted, intensity level upgrades are blocked, and impact amplification is prohibited. The system continues to prescribe training — BandAid is not a stop button — but it constrains the training to stay within the mechanical limits the runner has signaled.

When the runner deactivates BandAid, the system does not immediately return to full escalation authority. Instead, a graduated re-entry sequence applies: the runner must demonstrate mechanical stability across consecutive sessions before each escalation tier is re-authorized. Tier 1 first, then Tier 2, then Tier 3. The principle that governs normal escalation governs the return from BandAid: structural authorization requires demonstrated tolerance.

The design decision to make BandAid entirely user-controlled reflects a deliberate boundary. The system can detect many things — cadence changes, movement pattern shifts, impact exposure accumulation — but it cannot detect localized tissue pain. The runner knows something the system does not. Rather than pretending otherwise, the architecture gives the runner a first-class mechanism to express that knowledge, and the system responds with appropriate structural constraints.

---

## 8 — The Arbitration Hierarchy

When multiple signals, contracts, and domains produce conflicting guidance, the system resolves them through a precedence order. This is not a weighting scheme or a voting mechanism. It is a strict ordering: higher layers always win.

Safety contracts — injury protection, load limits, mechanical guards — sit at the top. Below them come the rules for returning after a break and for keeping the plan's load and intensity distribution intact. The escalation gate sits in the middle. Adjustments to the plan's trajectory, session-level modulation and environmental adjustments sit at the bottom. The order is defined once in the specification and nowhere else, and a shorter order applies during a taper.

A lower-priority layer may never override a higher-priority restriction. If a safety contract is active, the escalation gate cannot open — regardless of how strong the escalation evidence is. If a distribution integrity rule is violated, adaptation weighting cannot compensate — the violation must be resolved first.

This strict ordering is designed to rule out a failure that composite scores invite: the "override cascade," where a sufficiently strong positive signal in one dimension overwhelms a negative signal in another. Safety does not negotiate with progression.

---

## 9 — Execution Sharpening vs. Structural Escalation

The system draws a sharp line between two kinds of "making training harder":

**Execution sharpening** is session-level optimization. When signals indicate available capacity, the system may push the runner to the upper end of the prescribed intensity band, favor a faster target pace within the zone boundary, or reinforce interval quality. This happens within the current structural envelope — the session type does not change, the weekly volume does not increase, the intensity category does not shift.

**Structural escalation** is the expansion of the envelope itself. Higher volume ceiling. New intensity categories. More demanding session types. Structural escalation changes what the plan can prescribe — not just how aggressively it prescribes within existing boundaries.

The core engine — the component responsible for day-to-day session execution — operates under an offensive bias. It sharpens execution whenever the data supports it. But it cannot escalate structure. That authority belongs exclusively to the escalation gate, which applies the graduated tier logic described in section 5.

This distinction matters because a single excellent workout creates the subjective sense that the runner is ready for more. And often they are — ready for more *within the current structure*. Execution sharpening captures that. What they are not necessarily ready for is a fundamentally different training structure — and that requires the slower, more rigorous proof of graduated escalation.

A runner who executes a tempo session brilliantly gets a sharper tempo session next time. They do not get a VO₂max session. The difference is architectural.

---

## 10 — What Is Measured

What this paper describes is the mechanism as Runapp's specification defines it. Its outcomes have not been measured.

- **No field data.** Runapp is not yet released. No runner has trained under the mechanism, so there is no measurement of how often it protects, how often it holds a runner back unnecessarily, or how it changes injury rates.
- **Not in the simulator.** The simulator that measures Runapp's plans evaluates plans as prescribed. It does not model the domain states or the tiers (section 12).
- **The evidence behind the design** — the works cited here are in section 13 and in the companion evidence register, which says where each is cited. The citations show the principle each part of the design rests on; neither the paper nor the register says which value a study sets.

---

## 11 — Why This Architecture Is Unusual

Two common designs flatten the protection problem.

The first is the recovery score: aggregate the physiological signals into a single number, present it to the user, and let them decide. In that design the score is advisory: the user may follow it or not, and the system can warn but not protect.

The second is the algorithm-driven adjustment: the platform reduces or increases workout difficulty based on recent performance data, without clearly separating the reasons or the authority levels. The adjustment happens, but the logic is opaque and the domains are mixed. A runner who had poor sleep gets an easier run, but so does a runner with a biomechanical issue, and the system cannot tell them why, because the same mechanism produced both adjustments.

Runapp's architecture differs in three specific ways:

**Independent domain composition.** The three domains are structurally separate. Each maintains its own state, its own evaluation logic, and its own transitions. They inform a shared arbitration layer but never merge. This means the system always knows *which* domain is driving a decision, even though the internal state labels are never exposed to the runner.

**Offensive default with earned progression.** The system does not hold runners back by default. It pushes forward whenever the evidence supports it — and it defines "evidence" rigorously: multi-domain stability across multiple sessions, with graduated tiers requiring progressively stronger proof. This is neither reckless nor conservative. It is evidence-gated aggression.

**Structural authority, not advisory suggestion.** The escalation mechanism does not produce suggestions. It produces structural constraints that the plan generation engine must obey. When a safety contract activates, the escalation gate closes. When a domain enters a restrictive state, certain session categories become unavailable. The protection is not a recommendation the system might override — it is a hard boundary the system cannot cross.

Recovery scores are common, and so are algorithmic adjustments. Independent domain states with structural authority over the plan and an offensive default are the combination this architecture is built on. We have not surveyed the market systematically, so we make no claim that the combination is unique.

---

## 12 — What This Architecture Does Not Model

The escalation mechanism is a real-time adaptive system — it responds to the runner's changing physiological state during the execution of a training plan. This is distinct from the plan architecture itself, which determines what sessions are prescribed in what order.

The Runapp simulator evaluates plan architecture: session composition, volume progression, intensity distribution. It does not model the escalation mechanism — the domain states, the tiers, BandAid — because the simulator evaluates *prescribed* plans, not *adapted* plans. What it does carry is a narrower proxy for one downstream effect: a load-ratio rule that replaces a scheduled quality session with an easier one when a synthetic runner's recent load runs ahead of their chronic load. The proxy applies to Runapp's plans only and does not share a trigger with the specified mechanism, which reads readiness from heart-rate variability rather than from load. Its effect on measured plan quality is not measured at the current instrument version. The simulator answers "what happens if a population of runners follows this plan as prescribed?" The escalation mechanism answers "what happens when this particular runner's body changes during execution?"

Modeling the full adaptive loop — plan architecture plus real-time escalation plus signal-driven modification — is a future research direction. The current measurement campaigns evaluate the quality of plan design. Evaluating the quality of plan adaptation requires a different kind of simulation, one that models the feedback between execution and response.

This is a known limitation. It is also a feature of the architecture's modularity: the plan design system and the plan adaptation system are separable components with clearly defined interfaces. The plan can be evaluated without the adaptation, and the adaptation can be evaluated without the plan — though the full picture requires both.

**Specified, not yet shipped.** Runapp is in development. This paper describes the mechanism as the specification defines it. The engine implements the specification in stages; where it does not yet match, the specification governs and the engine is changed. The specification is itself under review, and where two of its rules are found to disagree, the conflict is resolved in the specification — never by what the code happens to do.

---

## 13 — Sources

The works cited in this paper (bibliographic details checked against the publisher), also listed in the companion evidence register:

- Bellenger, C.R. et al. (2016). Monitoring athletic training status through autonomic heart rate regulation: A systematic review and meta-analysis. *Sports Medicine*. https://doi.org/10.1007/s40279-016-0484-2
- Buchheit, M. (2014). Monitoring training status with HR measures: Do all roads lead to Rome? *Frontiers in Physiology*. https://doi.org/10.3389/fphys.2014.00073
- Frandsen, J. et al. (2025). How much running is too much? Identifying high-risk running sessions in a 5200-person cohort study. *British Journal of Sports Medicine*. https://doi.org/10.1136/bjsports-2024-109380
- Gabbett, T.J. (2016). The training-injury prevention paradox: should athletes be training smarter and harder? *British Journal of Sports Medicine*. https://doi.org/10.1136/bjsports-2015-095788
- Hafer, J.F. et al. (2015). The effect of a cadence retraining protocol on running biomechanics and efficiency: a pilot study. *Journal of Sports Sciences*. https://doi.org/10.1080/02640414.2014.962573
- Helgerud, J. et al. (2007). Aerobic high-intensity intervals improve VO2max more than moderate training. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/mss.0b013e3180304570
- Le Meur, Y. et al. (2013). Evidence of parasympathetic hyperactivity in functionally overreached athletes. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0b013e3182980125
- Meeusen, R. et al. (2013). Prevention, diagnosis, and treatment of the overtraining syndrome: Joint consensus statement of the European College of Sport Science and the American College of Sports Medicine. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0b013e318279a10a
- Montero, D. & Lundby, C. (2017). Refuting the myth of non-response to exercise training: 'non-responders' do respond to higher dose of training. *The Journal of Physiology*. https://doi.org/10.1113/JP273480
- Nuuttila, O.-P. et al. (2024). Monitoring fatigue state with heart rate-based and subjective methods during intensified training in recreational runners. *European Journal of Sport Science*. https://doi.org/10.1002/ejsc.12115
- Plews, D.J. et al. (2014). Monitoring training with heart-rate variability: How much compliance is needed for valid assessment? *International Journal of Sports Physiology and Performance*. https://doi.org/10.1123/IJSPP.2013-0455
- Van Dongen, H.P.A. et al. (2003). The cumulative cost of additional wakefulness: Dose-response effects on neurobehavioral functions and sleep physiology from chronic sleep restriction and total sleep deprivation. *Sleep*. https://doi.org/10.1093/sleep/26.2.117

---

## 14 — Map

- [Runapp](../README.md) — what Runapp is and how its parts fit together.
- [The Adaptation Engine](adaptation.md) — how the plan is modified day to day, inside the envelope this mechanism sets.
- [The Motivation Architecture](motivation.md) — how protection and denial are communicated to the runner.
- [Evidence Register: The Escalation Mechanism](../reference/escalation-evidence-register.md) — the literature behind the design.
- [Runapp Validation](https://github.com/jonasmaandiwallin/runapp-validation) — the simulator and the plan-quality metric that measure Runapp's plans.
