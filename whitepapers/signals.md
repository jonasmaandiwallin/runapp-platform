---
title: "Signals: What Flows into Runapp, and How Much It Is Trusted"
type: reference
status: draft
version: "0.2"
date: "2026-11-21"
author: "Tooling & Architecture"
audience: "External — no IP disclosure. Readable without NDA."
reading_order: 2
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
    roles: [drafting, technical-analysis]
    source: platform
ai_disclosure:
  - role: drafting
    status: complete
    scope: "Text and structure of version 0.2, written by the Tooling & Architecture agent from Runapp's specification, with inline citations worded from the registered key findings of the cited works and, where Research Pipeline has read a work in full, from that reading."
    evidence: "The document's own text."
    reason: null
  - role: technical-analysis
    status: complete
    scope: "The signal rules of Runapp's specification read topic by topic — the tiers between raw data and decisions, ingestion paths and the device integrations they name, source precedence and provenance, confidence classification, anomaly handling, reinforcement, protective handling of uncertainty, heart-rate variability, the runner's own report, environmental, equipment and biological context, signal status and promotion, and starting without baselines. The signal register was counted by status on 2026-11-18. The paper states only what the governing rules agree on; where two rules disagree, it states the principle they share and leaves the detail out."
    evidence: "The fact-check report of 2026-11-03 for this paper, and for the sources section added in version 0.2 the two ingestion contracts read on 2026-11-18: each statement about the specification against the rule it was read from, each citation against its registered key finding, and the open conflicts between rules."
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
path: "whitepapers/signals.md"
---

# Signals: What Flows into Runapp, and How Much It Is Trusted

*How Runapp decides which data it believes, how far, and what that data is allowed to change.*

---

## 1 — The Problem with Trusting the Device

A running app sees its runner through devices — a watch, a chest strap, a phone, a health platform that collects them. The numbers look precise. They are not always.

Consumer wearables that estimate maximal oxygen uptake from exercise are close to correct on average, but the error for an individual can be large (Molina-Garcia et al., 2022). In a meta-analysis of one widely used smartwatch, heart rate agreed with the reference on average, but 95 percent of individual readings are expected to fall within roughly seven beats per minute either side of it (Lambe et al., 2026). In one family of wristband sleep trackers, the sleep-staging models did not differ significantly from polysomnography in total sleep time, but detected wakefulness poorly and give only rough estimates of time in each stage (Haghayegh et al., 2019).

Even a correct reading can be ambiguous: heart-rate variability after exercise and heart-rate recovery can rise both with good adaptation and with overreaching, and on their own cannot tell the two apart (Bellenger et al., 2016). And data arrives from several sources at once, late, twice, or with gaps.

The common answer is to take the device's number, or its readiness score, at face value. That fails in two ways. A wrong reading moves the plan. And a right reading without its context — heat, new shoes, a week at altitude — moves it in the wrong direction.

Runapp's specification takes a different position. Every piece of data has a known origin and a trust class before it is used, it passes through fixed stages before it can change anything, and no single reading decides.

---

## 2 — Four Tiers, One Direction of Authority

The specification separates what is measured from what is decided, in four tiers:

1. **Raw telemetry** — heart-rate variability, resting heart rate, pace, cadence, sleep duration, temperature. Raw telemetry cannot modify the plan's structure.
2. **Derived indicators** — for example, heart rate drifting upward against a steady pace within a session, or cadence falling at easy intensity. Derived indicators feed classification. They cannot escalate or suppress load.
3. **State classifications** — the condition of each physiological domain: systemic, mechanical and central nervous system (see *The Escalation Mechanism*). Only states may inform the rules that make decisions.
4. **Decisions** — the escalation gate, protective rules, intensity-distribution constraints and structural ceilings. Only this tier may change the plan's structure.

No signal may skip a tier, and authority flows in one direction only, upward. No decision rule may refer to raw telemetry directly. The specification names the shortcuts it forbids: a drop in heart-rate variability that cuts the week's volume, a strong session that unlocks high-intensity work, a short night that cancels a phase of the plan.

The specification creates no readiness score, fatigue index, injury-risk score or performance rating, and the runner never sees the signals. Signals used only for advice, such as fuel timing, may not influence the escalation gate, trigger protection or rewrite the plan's structure. In the specification's words: data does not govern Runapp; structure governs data.

---

## 3 — Where Data Comes From, and Which Source Wins

Runapp is specified to work with the devices runners already own, not with one brand. That meant going through each maker's interface: what it exposes, what it holds back, and how far each field can be trusted.

**The baseline is the phone's health platform.** On iPhone that is Apple Health, which collects data from the Apple Watch and from compatible devices and apps — Garmin, Polar, Wahoo, COROS, Strava and others. It must remain a fully functional path on its own: a runner who connects nothing else still gets a working coach.

**Where the platform falls short, Runapp goes direct.** Some makers do not pass everything to Apple Health. Running dynamics such as ground contact time, vertical oscillation and running power, heart-rate variability in the format the maker measures, and the maker's own training load often stay in the maker's system. The specification calls this the HealthKit wall, and answers it with direct integrations: Garmin, Polar, Whoop and Oura through their own interfaces; COROS, which has no public interface, through a service that normalizes its data; and Strava as the catch-all for devices without a direct integration. A shoe-mounted power meter that writes its own running dynamics to Apple Health opens the same metrics to a runner with any watch.

Whatever the path, data follows one sequence — ingestion, an authority and provenance stage, confidence classification, signal interpretation, state classification, decision rules — and nothing may bypass it. Every source is normalized into one record of a session before it enters that sequence.

When two sources report the same thing, precedence is deterministic: the same conflict always resolves the same way, and the order favours the more direct record — a direct integration over a catch-all, a measurement over a value derived from it, a recorded activity over a manually logged one, a continuous stream over a summary.

Between platforms, precedence is set per category of data, so one run can take its running dynamics from one source and the night's sleep from another. Conflicting values are never merged or averaged, and a run that arrives twice is counted once. A conflict the rules cannot resolve is downgraded in trust, not thrown away or picked at random; a lower-ranked record is kept for provenance, not deleted. When a preferred source is unavailable, the next is used; running-dynamics fields only that source could supply are treated as missing, never estimated or interpolated.

Some metrics look alike and are not. Device makers report heart-rate variability in two formats; the specification treats them as separate metrics, never mixes them in one baseline, and rebuilds the baseline when a change of device changes the format. Metrics whose measurement depends on where the sensor sits are graded by placement and validation: in a small treadmill study, wearable sensors tracked vertical oscillation reliably but differed systematically in absolute values, some over- and some under-estimating it against video (Smith et al., 2022). A validated chest- or waist-mounted sensor therefore counts fully, a wrist-based estimate without published validation is accepted but marked, and a foot pod is used only relative to the runner's own baseline. No absolute value is compared across devices, and a change of device restarts the baseline. A wearable's own fitness estimate becomes an anchor only through the specification's ranked calibration, and only from sources accurate enough (see *Plan Design*).

The runner sees none of this. Which source a number came from, and whether an integration is connected, is never shown.

---

## 4 — Trust Before Use

Before any signal may influence a decision, it is placed in one of four confidence classes:

- **High confidence** — reliable sensor origin and stable context.
- **Context modified** — affected by environment or partial uncertainty, such as a heart-rate recording with gaps.
- **Low confidence** — incomplete or unreliable, such as an interrupted recording.
- **Disqualified** — fails integrity, such as a duplicated session or a physiologically implausible stream.

A signal without a class goes nowhere. When a signal fits more than one class, the most restrictive applies. The class is assigned once, at the authority stage, and no later stage may upgrade it. Each class caps what the signal may do: disqualified data influences nothing; low-confidence data may serve as context but never triggers an escalation; context-modified data is checked against high-confidence data before it counts toward the runner's assessed capacity.

Each signal also carries a continuous internal confidence weight, built from its history, completeness, agreement with other signals and plausibility. It changes sensitivity, never rules. When confidence falls, safety buffers widen, progression needs a longer run of stability, and the day's session may be simplified. When confidence is high across agreeing signals, buffers narrow and progression may come earlier. In the specification's words: when confidence shifts, thresholds adapt; authority does not.

Low confidence may simplify a session, but it may not expand the plan's envelope, lower a ceiling permanently, rewrite the intensity distribution, or by itself cause structural suppression. High confidence never bypasses protection, safety limits or the escalation rules. Confidence is never shown to the runner. And the largest structural steps require full confidence in every domain: a runner whose devices cannot supply it does not get them — by design.

---

## 5 — Anomalies Are Expected

An anomaly — an extreme deviation from the runner's own baseline, a sudden change without history, disagreement between signals, a known noisy context, a gap — is expected, not a system failure. The rules follow:

- A single measurement may not cancel a session.
- Escalation is never granted from isolated data or from one session's anomaly.
- Structural change requires a consistent pattern, confirmed across sessions and across signals; so does tightening a restriction.
- Noise alone may not reset the counters toward escalation, freeze the escalation window, or downgrade the plan's trajectory. Low confidence increases reliance on continuity, not regression.

Uncertainty must also stay inside its own domain. Noise in heart-rate variability may not become an assumed mechanical injury. Variable sleep may not become a classification of systemic overtraining. One hard session may not become a suppressed nervous-system state.

The filter has a counterweight. Its purpose is to prevent false positives, not to hide real risk. When instability is confirmed across domains, protection activates, and confidence handling may not delay it.

All external sources — health platforms, devices, weather, calendars — are treated as optional, fallible and non-authoritative. External data improves resolution; it never defines authority, and no structural decision rests on a single source. Calendar access is opt-in and may shape how a session is expressed, never eligibility to progress. When data is missing, the runner sees no technical error and no shift of responsibility onto them.

---

## 6 — Patterns, Not Readings

Negative patterns trigger protection; positive patterns are expected to be used. A reinforcing signal is a pattern confirmed across several sessions and several signals that training is being absorbed: the relationship between heart rate and pace stays stable, cadence holds, drift stays controlled, the nervous-system domain stays neutral, and the runner's own experience is positive without mechanical regression. A single strong session does not qualify.

Reinforcement is not escalation. It informs when the escalation gate reviews the runner, not what the gate may authorize. But when reinforcement is sustained and confidence is high, the specification requires the system to act — to advance progression within the safety limits. Leaving a runner where they are despite a sustained positive pattern is named a failure, not caution.

The brakes are explicit. Instability pauses the accumulation of positive evidence. Mechanical instability suspends it entirely, and positive systemic or nervous-system signals cannot override that mechanical veto. A single bad session does not erase long-term confidence; confidence declines gradually, not reactively. Reinforcement never becomes a performance score or a reward mechanic.

---

## 7 — Heart-Rate Variability: Useful and Ambiguous

Different heart-rate measures may each reflect a different aspect of adaptation, so no single one is enough on its own (Buchheit, 2014). Measures taken after exercise can rise with good adaptation and with overreaching alike (Bellenger et al., 2016), and in trained triathletes driven into functional overreaching, weekly-averaged variability rose while performance fell (Le Meur et al., 2013). In elite athletes, both increases and decreases have been associated with negative adaptation, and single-day values are noisy enough that rolling averages, judged against the individual's own smallest worthwhile change, are recommended instead (Plews et al., 2013).

The specification uses heart-rate variability in two ways it forbids conflating: a day-level readiness determination, which decides whether the planned session type stands, and a longer-term suppression signal that feeds the systemic domain.

The readiness determination compares a recent average with a band around the runner's own baseline, and it is binary — sufficient or insufficient, with no graded score. Insufficient readiness may exchange a planned session for an easy one; it never adds volume. This is the shape of the decision in trials of HRV-guided training, where hard sessions were done unless the runner's daily or rolling value had dropped meaningfully below their own reference, with easy training or rest otherwise (Kiviniemi et al., 2007; Vesterinen et al., 2016). In recreational runners, the HRV-guided group improved 3000 m performance with fewer hard sessions, though the between-group difference was small and not significant (Vesterinen et al., 2016). A meta-analysis of randomized trials found a small advantage of HRV-guided training for maximal oxygen uptake, larger in amateur athletes (Granero-Gallegos et al., 2020).

Further rules keep the signal honest. Windows count valid measurements, not calendar days. Without enough data for a baseline there is no determination, and the session proceeds as planned. Variability above the runner's normal range is not automatically good: it opens an opportunity only when other signals agree and the pattern has been told apart from early overreaching — and even then only to keep the planned quality session within the existing volume. Recovery signals never raise load or unlock escalation. Where a device offers a standardized resting measurement as well as passive overnight recording, the standardized one is preferred.

---

## 8 — The Runner's Own Report

Self-reported wellness responds to changes in training load more sensitively and consistently than commonly used objective measures (Saw, Main & Gastin, 2016). In recreational runners during a period of intensified training, subjective measures were more sensitive to the change in load than heart-rate-based ones (Nuuttila et al., 2024). In a small randomized study of experienced male cyclists with heavy dropout, training guided by heart-rate variability together with wellness scores improved some short-duration power outcomes more than heart-rate-variability-guided training alone (Alfonso et al., 2025).

Runapp assembles the runner's subjective state without a questionnaire, from a feeling slider before and after a session, passive wearable data, and movement patterns from recent sessions. Self-reported values such as perceived exertion and pain are treated as valid but subjective. The specification constrains this input:

- **Absence is not normal.** Missing input is never read as wellness, and no default is substituted.
- **Never required, never an authority.** It improves classification when present; its absence blocks nothing. It does not by itself trigger escalation, a volume increase or a gate decision.
- **Patterns, not days.** A deviation must persist before it counts.
- **No nagging.** Sporadic input is the expected state; the runner is not prompted or penalized for skipping it. In interviews with athletes, coaches and support staff at a national sporting institute, the use of self-report depended both on the measure itself, such as how accessible it was and when it was completed, and on the social environment, including athlete buy-in and staff reinforcement (Saw et al., 2015); the specification's answer is to show runners how their input shaped the response, not to chase them for it.

When a runner reports a low starting state but their physiology during the session stays within their own normal range, the specification reads probable mental rather than physical fatigue. The inference needs both kinds of data, is never shown to the runner, and only shapes how the next session is communicated.

---

## 9 — Context Modifies; It Does Not Decide

The specification treats environment, equipment and biology as modifiers of how signals are read — never as authorities over the plan.

**Environment.** Where evidence supports a correction formula, a quantitative correction is applied; where it supports recognizing a condition but not correcting for it, a flag is set and no number changes; where evidence is missing, nothing is implemented. During prolonged exercise in the heat, a rising body temperature raises heart rate and lowers stroke volume, even without dehydration (González-Alonso et al., 2008), so while heat stress is flagged, heart-rate-based escalation is held back; steady heart-rate drift on a treadmill at constant speed and cadence counts as thermoregulation, not fatigue. In the cold, optical wrist sensors may report cadence as heart rate; while that risk is flagged, no escalation may rest on heart rate alone. At altitude, the reading is adjusted rather than the runner's fitness, and the dip in heart-rate variability after arrival is not read as overreaching. No arithmetic correction of heart rate for air temperature is permitted, because no validated formula exists. A flag does not outlive its session without fresh evidence. And no environmental signal may change a structural ceiling, the escalation gate or a progression threshold.

**Equipment.** A runner who switches to carbon-plated shoes may see a lower heart rate at the same pace, which a system without shoe context would read as improved fitness. In mostly treadmill-based studies, carbon-plated shoes were associated with a lower energy cost of running than non-plated shoes, though other features of the shoe likely contribute (Kobayashi et al., 2026), so the specification attaches shoe context to every session before its signals are interpreted. The economy gain from such a shoe is attributed to the shoe, not to the runner. Sessions in shoes still being broken in are read with reduced confidence and left out of the assessment of the runner's capacity; sessions in shoes of unknown maturity are read with reduced confidence.

**Biology.** Measured physiology takes precedence over profile-based modifiers; measured fitness is not corrected for age or menopausal status. Biological sex is not a prescription variable: running economy does not differ between the sexes (Besson et al., 2022). Menstrual cycle phase is opt-in context, never inferred from physiological signals, never used to prescribe a different training load, and never turned into a performance prediction: the effect of cycle phase on performance is, on average, trivial and the evidence low in quality and heterogeneous, so population averages are a weak guide for any individual (McNulty et al., 2020). Age lengthens recovery after the most demanding sessions, an adjustment the specification marks as low-confidence. No biological modifier may override a hard stop or an escalation decision.

---

## 10 — Signals That Wait

The specification keeps a register of every signal — its definition, data source, action and governing rule — and gives each a status:

- **Active** signals may drive decisions.
- **Candidate** signals may be ingested and stored, but may not drive anything until research coverage justifies promotion.
- **Computed** signals are derived internally from active ones and are never imported.
- **Pre-registered** signals are declared only to record what must be true before they could be used. Data for them is discarded.

The register holds 23 signals: 14 active, five candidates, two computed, one pre-registered and one retired. They span the heart (variability, resting rate, recovery after effort, drift against pace or heat), running mechanics (cadence, ground contact, stride, vertical oscillation, power, stiffness), the night (sleep, breathing rate, skin temperature), the runner's own report (how a session felt, mood, wellness), and menstrual context.

Promotion requires evidence. Overnight wrist skin temperature, for example, is a candidate: skin temperature measured by a wearable varies with the menstrual cycle and rises after ovulation, in a pattern better described as a gradual oscillation than a step (Gombert-Labedens et al., 2024), so without cycle data, a hormonal rise cannot be told from the onset of illness. Heart-rate recovery after exercise is pre-registered only, because its value depends heavily on how hard the effort was. Sleep is used by its total duration; the breakdown into sleep stages is held at lower confidence and may not drive decisions that depend on stage-level precision.

A metric that is not individual-relative, pattern-based and resistant to volatility does not qualify as a signal at all.

---

## 11 — Starting Without Baselines

Most signals are relative to the runner's own baseline, and a new runner has none. Population norms are no substitute: resting heart rate varies with age, sex, sleep, body composition and season (Quer et al., 2020).

If the runner has recorded history in their health platform, baselines are built from it, and the signals are active from the first day. If not, each signal builds its baseline separately, and its day-to-day influence on the plan switches on when its own baseline is established; until then, the plan runs as a static progressive plan (see *Plan Design*). A disconnected wearable pauses accumulation; it does not reset it. A runner who never pairs a wearable stays on the static plan, and that is intended. The runner is never told they are in a limited mode.

When external data later degrades or is withdrawn, the system falls back on its internal baselines and historical patterns. The plan's structure does not change because a connection failed.

---

## 12 — What Is Measured

What this paper describes is signal handling as Runapp's specification defines it.

- **No measurement of signal handling.** The rules for classifying, weighting and interpreting signals have not been tested against outcomes in runners. Where this paper cites research, the research supports the principle a rule rests on, not Runapp's version of it.
- **The plans are measured in simulation**, under a physiological model (see *Runapp Validation*). That measures plans, not how real device data is handled.
- **No field data.** Runapp is not yet released. No runner's device data has passed through these rules in use.

---

## 13 — Why This Design Is Unusual

A common design trusts the device and acts on its numbers. Runapp's specification separates the reading from the decision and puts rules between them.

Three properties follow. Trust is **explicit**: every signal has a known origin and a confidence class before it is used, and the class bounds what it may do. Influence is **pattern-based**: no single reading cancels a session, grants progression or changes the plan's structure, while confirmed instability still triggers protection without delay. And context is **subordinate**: heat, altitude, shoes and biology change how a signal is read, never what the plan is allowed to become.

The breadth is deliberate too. The specification goes device by device rather than taking one platform's summary, so that a runner with a Garmin, a Polar, a COROS, a Whoop, an Oura or only an iPhone gets the same rules, each signal graded by what that device can actually deliver.

We have not surveyed the market systematically, and make no claim that the design is unique.

---

## 14 — Limitations

- **The specification is the subject.** Runapp is in development. The engine implements the specification in stages; where it does not yet match, the specification governs and the engine is changed. No device integration named in this paper is claimed to be live.
- **The specification is itself under review.** Where two of its rules are found to disagree, the conflict is resolved in the specification — never by what the code happens to do. This paper states only what the rules agree on and leaves contested details out, among them the full order of precedence between platforms for each category of data.
- **Sensors set the ceiling.** No rule makes a reading more accurate than its sensor. Several signals rest on device validations made in small samples or on other devices, and some wrist-based measurements have no published validation at all.
- **Makers decide what they share.** What a direct integration can deliver depends on what each maker exposes, and that can change.
- **Heart-rate variability remains ambiguous.** Disambiguating adaptation from overreaching relies on combining signals and on the runner's own report, which is sporadic by design.
- **Some modifiers rest on mechanism, not trials.** Several biological and environmental adjustments are marked low-confidence in the specification, pending better evidence.
- **Evidence gaps are left open.** Where research is missing — temperature-based heart-rate correction, running in snow and ice, biomechanics in the cold — the specification implements nothing rather than guessing.
- **Full trust needs full data.** A runner without high-confidence data in every domain is, by design, never eligible for the largest structural steps.

---

## 15 — Sources

The works cited in this paper (bibliographic details as registered):

- Alfonso, C., Clarke, D.C. & Capdevila, L. (2025). Individual training prescribed by heart rate variability, heart rate and well-being scores in experienced cyclists. *Scientific Reports*. https://doi.org/10.1038/s41598-025-13540-z
- Bellenger, C.R. et al. (2016). Monitoring athletic training status through autonomic heart rate regulation: A systematic review and meta-analysis. *Sports Medicine*. https://doi.org/10.1007/s40279-016-0484-2
- Besson, T. et al. (2022). Sex differences in endurance running. *Sports Medicine*. https://doi.org/10.1007/s40279-022-01651-w
- Buchheit, M. (2014). Monitoring training status with HR measures: Do all roads lead to Rome? *Frontiers in Physiology*. https://doi.org/10.3389/fphys.2014.00073
- Gombert-Labedens, M. et al. (2024). Using wearable skin temperature data to advance tracking and characterization of the menstrual cycle in a real-world setting. *Journal of Biological Rhythms*. https://doi.org/10.1177/07487304241247893
- González-Alonso, J., Crandall, C.G. & Johnson, J.M. (2008). The cardiovascular challenge of exercising in the heat. *Journal of Physiology*. https://doi.org/10.1113/jphysiol.2007.142158
- Granero-Gallegos, A., González-Quílez, A., Plews, D. & Carrasco-Poyatos, M. (2020). HRV-based training for improving VO2max in endurance athletes. A systematic review with meta-analysis. *International Journal of Environmental Research and Public Health*. https://doi.org/10.3390/ijerph17217999
- Haghayegh, S., Khoshnevis, S., Smolensky, M.H., Diller, K.R. & Castriotta, R.J. (2019). Accuracy of wristband Fitbit models in assessing sleep: Systematic review and meta-analysis. *Journal of Medical Internet Research*. https://doi.org/10.2196/16273
- Kiviniemi, A.M. et al. (2007). Endurance training guided individually by daily heart rate variability measurements. *European Journal of Applied Physiology*. https://doi.org/10.1007/s00421-007-0552-2
- Kobayashi, E.N., de Toledo, R.R.F., de Almeida, M.O., Sprey, J.W.C. & Jorge, P.B. (2026). Metabolic effects of carbon-plated running shoes: A systematic review and meta-analysis. *Frontiers in Sports and Active Living*. https://doi.org/10.3389/fspor.2025.1710224
- Lambe, R., Baldwin, M., O'Grady, B., Schumann, M., Caulfield, B. & Doherty, C. (2026). The accuracy of Apple Watch measurements: A living systematic review and meta-analysis. *npj Digital Medicine*. https://doi.org/10.1038/s41746-025-02238-1
- Le Meur, Y. et al. (2013). Evidence of parasympathetic hyperactivity in functionally overreached athletes. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0b013e3182980125
- McNulty, K.L. et al. (2020). The effects of menstrual cycle phase on exercise performance in eumenorrheic women: A systematic review and meta-analysis. *Sports Medicine*. https://doi.org/10.1007/s40279-020-01319-3
- Molina-Garcia, P. et al. (2022). Validity of estimating the maximal oxygen consumption by consumer wearables: A systematic review with meta-analysis and expert statement of the INTERLIVE Network. *Sports Medicine*. https://doi.org/10.1007/s40279-021-01639-y
- Nuuttila, O.-P. et al. (2024). Monitoring fatigue state with heart rate-based and subjective methods during intensified training in recreational runners. *European Journal of Sport Science*. https://doi.org/10.1002/ejsc.12115
- Plews, D.J., Laursen, P.B., Stanley, J., Kilding, A.E. & Buchheit, M. (2013). Training adaptation and heart rate variability in elite endurance athletes: Opening the door to effective monitoring. *Sports Medicine*. https://doi.org/10.1007/s40279-013-0071-8
- Quer, G. et al. (2020). Inter- and intraindividual variability in daily resting heart rate and its associations with age, sex, sleep, BMI, and time of year: Retrospective, longitudinal cohort study of 92,457 adults. *PLoS ONE*. https://doi.org/10.1371/journal.pone.0227709
- Saw, A.E. et al. (2015). Monitoring athletes through self-report: Factors influencing implementation. *Journal of Sports Science and Medicine*.
- Saw, A.E., Main, L.C. & Gastin, P.B. (2016). Monitoring the athlete training response: Subjective self-reported measures trump commonly used objective measures: A systematic review. *British Journal of Sports Medicine*. https://doi.org/10.1136/bjsports-2015-094758
- Smith, C.P. et al. (2022). The validity and reliability of wearable devices for the measurement of vertical oscillation for running. *PLoS ONE*. https://doi.org/10.1371/journal.pone.0277810
- Vesterinen, V., Nummela, A., Heikura, I., Laine, T., Hynynen, E., Botella, J. & Häkkinen, K. (2016). Individual endurance training prescription with heart rate variability. *Medicine & Science in Sports & Exercise*. https://doi.org/10.1249/MSS.0000000000000910

---

## 16 — Map

- [Runapp](../README.md) — what Runapp is and how its parts fit together.
- [Plan Design](plan-design.md) — how a fitness measurement and a runner's week become a training plan.
- [The Escalation Mechanism](escalation.md) — how the domain states built from these signals decide when the plan may grow.
- [The Adaptation Engine](adaptation.md) — how the plan is modified day to day once signals arrive.
- [Evidence Register: Signals](../reference/signals-evidence-register.md) — the works this paper cites, and where.
- *Decision Delivery* (forthcoming) — how a decision reaches the runner without exposing the signals behind it.
- *Traceable by Design* (forthcoming) — how every decision can be traced back to the data and rules that produced it.
- *Data and Privacy* (forthcoming) — what data Runapp collects, and what the runner is never shown.
