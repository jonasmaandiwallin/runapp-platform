---
title: "The Motivation Architecture: Behavioral Science as System Design"
type: reference
status: draft
version: "0.3"
date: "2026-11-21"
author: "Tooling & Architecture"
audience: "External — no IP disclosure. Readable without NDA."
reading_order: 6
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
    scope: "Text and structure of versions 0.1 and 0.2 (2026-09-02 and 2026-09-03), written by the Tooling & Architecture agent from Runapp's specification. The model used for those versions is not recorded."
    evidence: "The document's own text."
    reason: null
  - role: technical-analysis
    status: complete
    scope: "Version 0.3: every statement about how the behavioral architecture is specified read against the governing rules of Runapp's specification. The engine and the iOS app were read as well; the paper makes no claim about what they build."
    evidence: "The release fact-check report of 2026-11-03, one row per statement with the rule it was read against and the verdict."
    reason: null
  - role: editing
    status: complete
    scope: "Version 0.3: statements the specification does not bear corrected to the specification — the three criteria of the alignment test, telling the runner that the plan adapted held as a hypothesis rather than a rationale with every prescription, communication that declines in frequency rather than varying, escalation denial without justification or promise, notification frequency after a gap, event day as the day the system does not modify, and how mechanisms are classified; sections on what is measured, limitations and a map added to the white-paper template. Inline citations to the research each principle rests on (loss aversion behind the absence of streaks, among others) and a sources section added on 2026-11-03, worded from the registered key findings."
    evidence: "The diff against version 0.2."
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
path: "whitepapers/motivation.md"
---

# The Motivation Architecture: Behavioral Science as System Design

*Why Runapp has no streaks, no badges, no recovery scores — and what it does instead.*

---

## 1 — The Problem with Engagement Mechanics

Most fitness applications treat behavioral science as a layer of decoration applied on top of the training logic. The core system generates a plan. Then a separate team adds streaks, badges, weekly challenges, leaderboards, celebration screens, and push notifications designed to keep the user opening the app. The engagement layer and the training layer are architecturally separate — and often in tension.

This creates a specific failure mode. The engagement mechanics optimize for interaction frequency — how often the user opens the app, how many sessions they complete, how long they maintain a streak. The training mechanics optimize for physiological outcomes — how much the runner improves, how safely they progress, how well the plan adapts to their body. When these objectives diverge — and they do — the engagement layer wins, because it is louder.

A streak system tells the runner to train every day. The training plan says to rest. The runner trains, because the streak is at 47 days and losing it feels like failure. The physiological cost is invisible. The engagement metric looks excellent.

Runapp treats behavioral science differently. It is not a layer applied after the training logic is designed. It is woven into the architecture from the beginning — shaping how decisions are presented, how authority is experienced, how communication is framed, and how the system handles the psychologically sensitive moments when it tells a runner to do less.

---

## 2 — The Alignment Test

Before any engagement mechanism is implemented in Runapp, it must pass a three-criteria diagnostic. The test is simple, and the bar is absolute — all three criteria must be satisfied:

**Does the mechanism serve the runner's actual goal?** A mechanism that helps the runner progress, perform, and stay healthy passes. A mechanism that increases app opens, time in the app, or session count without improving training fails.

**Is the system's adaptation transparent without exposing signals?** The runner should know *that* the system adapts to them, and never need to know *why* in terms of signals. The system's competence is visible; its data is not. A mechanism that surfaces the machinery — scores, states, the signal behind a decision — fails.

**Does the mechanism optimize for the runner's own goals, not for engagement metrics?** The same mechanism can be coaching or manipulation depending on what it is tuned for. A coach who times feedback to help the runner perform is coaching; an application that times feedback to maximize opens is not.

Mechanisms that pass all three and have direct empirical support are permitted. Mechanisms that fail any one are prohibited — not deferred, not softened, prohibited. Mechanisms with theoretical support but insufficient empirical validation are classified as hypotheses: they may be tested in controlled experiments, and are deployed only after a test supports them and the decision is recorded.

The test is intentionally conservative. It is easier to add an engagement mechanism that harms training than to detect the harm after the mechanism is in place. The bias is toward omission.

---

## 3 — What Runapp Does Not Have

The most distinctive feature of Runapp's behavioral architecture is what it excludes. Several mechanisms common across fitness applications are explicitly prohibited:

**No streaks.** A streak turns training into something the runner can lose. People judge outcomes against a reference point, and losses weigh more heavily than equivalent gains (Kahneman & Tversky, 1979). A streak makes the unbroken run the reference point, so any break — a planned rest day, an illness, a life event — registers as a loss. In negative social-media posts about popular commercial fitness apps, users described losing a streak as a source of failure and demotivation, and some described giving up the app and the behaviour itself (Sheen et al., 2025). A rest day the plan prescribes should never feel like a failure. The system that is supposed to help the runner train should not become the thing they are training for.

**No badges or achievements.** Badges for completing sessions, hitting milestones, or maintaining consistency are constructed rewards — they do not correspond to actual physiological progress. A badge for "10 sessions completed" treats the tenth session of a poorly designed plan identically to the tenth session of an excellent one. The reward is for interaction, not improvement. In controlled experiments, rewards made contingent on doing or completing a task undermine the motivation to do it for its own sake (Deci, Koestner & Ryan, 1999). A badge is exactly such a reward. The design keeps the runner training to become a better runner, not to collect.

**No recovery scores or readiness percentages.** This is a design decision with deep architectural consequences. A recovery score is a single number derived from multiple physiological signals. Exposing it creates two problems. First, it gives the runner a number to argue with: "My recovery score is 85, I feel fine, I'm going to ignore the easy session and do intervals." Second, it requires interpretation — the runner must decide what 85 means, whether it is high enough, how it compares to yesterday — and that is processing the task itself does not need; cognitive-load research shows that unnecessary problem-solving search competes with learning for limited working memory (Sweller, 1988). Runapp's three domain states (described in the Escalation Mechanism white paper) maintain the richness of the underlying signals without exposing them. The system makes the decision. The runner sees the decision, not the data.

**No raw data dashboards.** Heart rate variability trends, training load charts, sleep quality graphs — these are powerful tools for coaches and sports scientists. For a recreational runner on a plan, they are mostly a burden. Measuring an activity can reduce how much it is enjoyed (Etkin, 2016), and tracker users report a dependency on the device for staying active, weaker among those intrinsically motivated to exercise (Attig & Franke, 2019). Runners do say they value their data. Runapp's answer is not to hide it from them forever but to keep it out of the moment of execution. Runapp processes all this data internally. The runner sees the outcome — today's session, appropriately adapted — not the analysis.

**No points systems or leaderboards.** This is a design choice, not a settled finding. In a trial of gamification with different social incentives, a competition design produced the largest increase in activity; after the intervention ended activity fell in all arms, though the competition effect was still partly present (Patel et al., 2019). Runapp's plan is built for the long term, and a mechanism whose effect falls once it ends, and whose success is measured against other runners rather than against the runner's own goal, fails the alignment test's first and third criteria.

---

## 4 — What Runapp Does Have

The behavioral architecture is not just a list of prohibitions. It actively implements mechanisms grounded in behavioral science research:

**A single primary action.** When the runner opens the app on a training day, they see one thing: today's session. Not a dashboard. Not a menu. Not a choice between three session options with difficulty ratings. One session, ready to execute. This is grounded in choice architecture research: in field and laboratory experiments, people offered a large array of options were less likely to choose one, and less satisfied with what they chose, than people offered a small one (Iyengar & Lepper, 2000), and in a large meta-analysis, changing the structure of choices, such as defaults, was among the more effective choice-architecture approaches, although the overall evidence is contested once publication bias is accounted for (Mertens et al., 2022). The runner does not have the domain expertise to evaluate whether today should be threshold intervals or tempo — that is what the system is for. The single action is the default.

**Delegated authority with explicit consent.** Runapp operates a delegated authority model. During onboarding, the runner explicitly accepts that the system may deny escalation, reduce volume, suppress intensity, and impose recovery constraints. This is not fine print — it is the core agreement. The psychological foundation is Self-Determination Theory (Deci & Ryan, 1985): what sustains exercise is autonomous motivation: exercising because it is personally important predicts starting, and enjoying the activity itself predicts keeping it up (Teixeira et al., 2012). Runapp's design reads autonomy as the voluntary choice of a system whose constraints one endorses, an idea with a long philosophical history, in Kant's (1785) formulation the capacity to give oneself a law. The design's hypothesis is that a runner who chose Runapp and accepted its authority model experiences protection as a feature of the system they selected, not as a loss of control. In a survey of university students required to use a campus running app, a perceived threat to their freedom went with psychological reactance, and reactance with less favourable attitudes and intentions toward the app (Guo, 2024); explicit consent is meant to avoid that.

**That, never why.** The runner may be told that the plan has adapted to how their body responded — never the signal behind it. "Adapted based on how your body responded" is the permitted form. "Adapted because your heart-rate variability was low" is not: it exposes the signals the system keeps internal and invites the runner to fixate on them. Even the permitted form is classified as a hypothesis. Research on procedural justice — decisions are accepted more readily when the process is seen as fair (Lind & Tyler, 1988) — supports it, but it is not deployed until a controlled test shows that it builds long-term trust.

This is a trade-off, and the evidence does not all point one way. In health interventions informed by self-determination theory, giving a meaningful rationale was among the techniques associated with larger gains in autonomy, though only in exploratory analyses (Ntoumanis et al., 2021). Runapp's rule gives up the signal-level reason to keep the signals out of the runner's head. Whether the rationale can be meaningful without them is exactly what the hypothesis tests.

**Micro-choice within prescription.** The system does not offer the runner a choice of what to do today — that would defeat the purpose of the delegated authority model. But within the prescribed session, the runner has genuine agency: when to start, how to modify timing, how to report what they experienced. These micro-choices draw on an online experiment on a dietary intervention, in which giving people a real choice improved how they rated the intervention, whereas autonomy-supportive wording alone made no detectable difference (Smit et al., 2019).

**Declining notification frequency.** Prompt and message interventions lose their effect over weeks. In one micro-randomized trial, the short-term effect of activity prompts on walking was largest at the start and largely disappeared over the following weeks (Klasnja et al., 2019), and in another, in cardiac rehabilitation, tailored activity texts raised short-term step counts early on, but the effect weakened over time and differed by device (Golbus et al., 2024). Across text-message trials, an individualized or decreasing frequency was associated with larger effects than a fixed one (Head et al., 2013). Runapp's notifications follow a declining schedule: frequent while the habit forms, less frequent once it is established. Wording never varies at random: the same state gives the same message, and a change of wording is a new, recorded version.

---

## 5 — The Coaching Voice

Runapp communicates with runners through what the architecture calls the Playbook — a governed communication layer that defines tone and content boundaries; when messages are delivered is governed separately. The Playbook is not a marketing style guide. It is a behavioral contract with explicit invariants.

**Non-celebratory completion tone.** When a runner completes a session, the system acknowledges the completion. It does not celebrate it. This is a deliberate choice. Outcomes are judged against a reference point (Kahneman & Tversky, 1979), and celebrating every session risks moving it, so that an ordinary acknowledgement starts to read as less than usual. The strongest affirmation is kept for real progress, when it actually occurs.

**Calm escalation denial.** The most psychologically sensitive moment in the system is when it tells a runner they are not ready to progress. The runner feels strong. The runner wants to push. The system says no. How this is communicated determines whether the runner trusts the system or fights it.

Runapp handles escalation denial without moral language ("you shouldn't"), without reward framing ("when you earn it"), and without punitive tone ("you failed to meet the threshold"). It also gives no justification, no promise of when progression will come, and no condition to meet: today's session is stated calmly, as the plan for today. The system absorbs the runner's frustration. The runner retains dignity.

**No urgency escalation.** When a runner's engagement declines — fewer app opens, more missed sessions — the system does not respond with more notifications, stronger language, or loss-framed reminders. Freedom-threatening, controlling language — imperatives, pressure — provokes anger and reactance, more so when the recommended behaviour is one people repeat, such as exercise (Li & Shi, 2025). Runapp never escalates notification frequency over time. Frequency declines as the habit forms; after a long gap without training, it returns to the level of the first weeks rather than climbing above it.

**Event-day sensitivity.** On race day or goal-event day, the system's communication shifts to an event-appropriate register. If physiological signals suggest the runner is not in optimal condition, the system provides an advisory — not a substitution. The runner has trained for this day. The system informs; the runner decides. On every other day the system may modify the session and the runner may still choose differently; on event day the system does not modify the session at all, and the communication reflects that shift.

---

## 6 — Modified Sessions and Behavioral Continuity

When the adaptation engine modifies a scheduled session (described in the companion Adaptation Engine white paper), the behavioral architecture governs how the modification is experienced.

The core principle is the behavioral chain: the runner trained today. A modified session — an easier session substituted for a harder one, or a shorter session at the same intensity — is still a completed session. It counts as a full completion in all tracking. The runner's record shows unbroken training, because it was unbroken training.

This is not a cosmetic choice. The abstinence violation effect, a framework from relapse research, proposes that a lapse read as an irreparable failure is more likely to progress to a full relapse (Marlatt & Witkiewitz, 2005). It has not been tested directly in exercise, and habit research finds that missing a single opportunity does not materially change how a habit forms (Lally et al., 2010). Runapp's design takes the cautious reading: whatever the size of the effect, there is no reason to show a runner a failure that did not happen. The modification preserved the stimulus, protected the runner's body, and maintained the behavioral chain. Presenting it as anything other than a completed session would undermine all three.

The system follows a priority hierarchy for every session evaluation: modify is preferred over skip, and skip is preferred over forcing a session that exceeds the runner's readiness capacity. The runner always sees a complete, executable schedule. No gaps. No "you missed this" messages. No loss framing.

---

## 7 — The Onboarding Decision

Runapp's onboarding is designed as a behavioral commitment mechanism — specifically, a meta-implementation intention. Implementation intentions — if-then plans that link a situation to a response — have a medium-to-large effect on goal attainment and reduce the deliberation needed to act (Gollwitzer & Sheeran, 2006), though for a repeated behaviour such as gym attendance, a large field experiment found that simple planning prompts did not increase attendance (Carrera et al., 2018). Runapp applies the idea once, at enrollment, as a plan that covers every training day.

The onboarding resolution, structurally, is: "If it is a training day, I open the app and execute what it says." This plan is formed once. Every subsequent training day, the app's Today View is designed to work as the situational cue that activates the pre-formed response — an application of the research, not a finding of it. The runner does not decide whether to train — they decided that during onboarding. They simply execute.

This is why the app shows a single primary action. A menu of options would require deliberation. A single action activates the existing plan. The behavioral architecture and the interface design are not separate disciplines — they are the same decision expressed at different levels.

---

## 8 — The Boundary Between Coaching and Manipulation

Runapp draws an explicit architectural boundary between coaching design and manipulative design:

**Coaching design** optimizes for the runner's training progression, performance, and health outcomes. Every mechanism in the system is evaluated against this criterion. If a feature improves the runner's training — even at the cost of reduced app engagement — it passes.

**Manipulative design** optimizes for application engagement metrics: session count, open frequency, time in app. If a feature increases engagement without improving (or while harming) training outcomes, it fails the alignment test and is prohibited.

This boundary is not rhetorical. It is written into the specification. Every engagement mechanism the specification names carries a classification — permitted, prohibited, or hypothesis — and the evidence behind it, and a new mechanism is evaluated against the alignment test before it is released. A hypothesis cannot be deployed without a controlled test and a recorded decision.

The boundary also clarifies what Runapp considers legitimate engagement. A runner who opens the app daily because their training is working, because the system communicates clearly, and because the adaptive coaching is visibly responsive to their body — that is legitimate engagement. A runner who opens the app daily because a streak counter will reset if they do not — that is manufactured engagement. Runapp pursues the first and prohibits the second.

---

## 9 — Why This Matters

The behavioral architecture is not separate from the training architecture. It is the same architecture expressed through a different lens.

The escalation mechanism is a physiological system with behavioral consequences: when it denies progression, the runner experiences frustration. How the system handles that frustration — the communication tone, the rationale delivery, the dignity preservation — determines whether the runner trusts the system or abandons it.

The adaptation engine is a physiological system with behavioral consequences: when it modifies a session, the runner could perceive the modification as a failure. How the system presents the modification — as the session for the day, not a downgrade from the "real" session — determines whether the runner maintains their behavioral chain or breaks it.

The single-action interface is a behavioral decision with physiological consequences. Prolonged cognitive effort raises perceived exertion in later exercise (Marcora et al., 2009); removing decisions before a session is consistent with that finding, though not shown by it.

Every design decision in the system exists at the intersection of physiology and behavior. The behavioral architecture ensures that the intersection is navigated deliberately, with evidence, and with the runner's long-term interest — not short-term engagement — as the governing objective.

---

## 10 — What Is Measured

What this paper describes is the behavioral architecture as Runapp's specification defines it. Its effect on runners has not been measured.

- **No field data.** Runapp is not yet released. There is no measurement of how its design changes adherence, motivation or dropout compared with a design that has streaks, badges or scores.
- **In the simulator, only as an assumption.** The simulator that measures Runapp's plans samples how faithfully each synthetic runner follows a plan, and assumes that app-delivered plans are followed more reliably than book-based ones. The direction of that difference has some support: in one resistance-training trial, people coached through an app followed the programme more closely than people given a written plan alone (Gavanda et al., 2025). Its size is not measured. The simulator does not model communication.
- **The evidence behind the design** — the works cited here are in section 12 and in the companion evidence register, which says where each is cited. The citations show the principle each part of the design rests on; neither the paper nor the register says which value a study sets.

---

## 11 — Limitations

- **Evidence from other settings.** Much of the research behind the notification design was done with sedentary adults, cardiac patients and university students, not with runners following a structured plan. The specification applies it in its most conservative reading, and whether it carries over is part of what field data will show.
- **Hypotheses held back.** Mechanisms the evidence does not yet support — telling the runner that the plan has adapted is one — are not deployed until a controlled test supports them. Until then the system says less than it could.
- **Specified, not yet shipped.** Runapp is in development. This paper describes the behavioral architecture as the specification defines it. The engine and the app implement the specification in stages; where they do not yet match, the specification governs and the code is changed. The specification is itself under review, and where two of its rules are found to disagree, the conflict is resolved in the specification — never by what the code happens to do.

---

## 12 — Sources

The works cited in this paper, also listed in the companion evidence register:

- Attig, C. & Franke, T. (2019). I track, therefore I walk — Exploring the motivational costs of wearing activity trackers in actual users. *International Journal of Human-Computer Studies*. https://doi.org/10.1016/j.ijhcs.2018.04.007
- Carrera, M., Royer, H., Stehr, M., Sydnor, J. & Taubinsky, D. (2018). The limits of simple implementation intentions: Evidence from a field experiment on making plans to exercise. *Journal of Health Economics*. https://doi.org/10.1016/j.jhealeco.2018.09.002
- Deci, E.L. & Ryan, R.M. (1985). *Intrinsic Motivation and Self-Determination in Human Behavior*. Plenum. https://doi.org/10.1007/978-1-4899-2271-7
- Deci, E.L., Koestner, R. & Ryan, R.M. (1999). A meta-analytic review of experiments examining the effects of extrinsic rewards on intrinsic motivation. *Psychological Bulletin*. https://doi.org/10.1037/0033-2909.125.6.627
- Etkin, J. (2016). The hidden cost of personal quantification. *Journal of Consumer Research*. https://doi.org/10.1093/jcr/ucv095
- Gavanda, S. et al. (2025). Optimizing resistance training outcomes: Comparing in-person supervision, online coaching, and self-guided approaches: A randomized controlled trial. *Journal of Strength and Conditioning Research*. https://doi.org/10.1519/JSC.0000000000005216
- Golbus, J.R. et al. (2024). Text messages to promote physical activity in patients with cardiovascular disease: A micro-randomized trial of a just-in-time adaptive intervention. *Circulation: Cardiovascular Quality and Outcomes*. https://doi.org/10.1161/CIRCOUTCOMES.123.010731
- Gollwitzer, P.M. & Sheeran, P. (2006). Implementation intentions and goal achievement: A meta-analysis of effects and processes. *Advances in Experimental Social Psychology*. https://doi.org/10.1016/S0065-2601(06)38002-1
- Guo, J. (2024). Exploring college students' resistance to mandatory use of sports apps: A psychological reactance theory perspective. *Frontiers in Psychology*. https://doi.org/10.3389/fpsyg.2024.1366164
- Head, K.J. et al. (2013). Efficacy of text messaging-based interventions for health promotion: A meta-analysis. *Social Science & Medicine*. https://doi.org/10.1016/j.socscimed.2013.08.003
- Iyengar, S.S. & Lepper, M.R. (2000). When choice is demotivating: Can one desire too much of a good thing? *Journal of Personality and Social Psychology*. https://doi.org/10.1037/0022-3514.79.6.995
- Kahneman, D. & Tversky, A. (1979). Prospect theory: An analysis of decision under risk. *Econometrica*. https://doi.org/10.2307/1914185
- Kant, I. (1785). *Groundwork of the Metaphysics of Morals*. Read in the English version by Jonathan Bennett, Early Modern Texts. https://www.earlymoderntexts.com/
- Klasnja, P. et al. (2019). Efficacy of contextually tailored suggestions for physical activity: A micro-randomized optimization trial of HeartSteps. *Annals of Behavioral Medicine*. https://doi.org/10.1093/abm/kay067
- Lally, P. et al. (2010). How are habits formed: Modelling habit formation in the real world. *European Journal of Social Psychology*. https://doi.org/10.1002/ejsp.674
- Li, Z. & Shi, J. (2025). Message effects on psychological reactance: Meta-analyses. *Human Communication Research*. https://doi.org/10.1093/hcr/hqaf016
- Lind, E.A. & Tyler, T.R. (1988). *The Social Psychology of Procedural Justice*. Plenum. https://doi.org/10.1007/978-1-4899-2115-4
- Marcora, S.M. et al. (2009). Mental fatigue impairs physical performance in humans. *Journal of Applied Physiology*. https://doi.org/10.1152/japplphysiol.91324.2008
- Marlatt, G.A. & Witkiewitz, K. (2005). Relapse prevention for alcohol and drug problems. In G.A. Marlatt & D.M. Donovan (Eds.), *Relapse Prevention: Maintenance Strategies in the Treatment of Addictive Behaviors* (2nd ed., pp. 1–44). Guilford.
- Mertens, S. et al. (2022). The effectiveness of nudging: A meta-analysis of choice architecture interventions across behavioral domains. *Proceedings of the National Academy of Sciences*. https://doi.org/10.1073/pnas.2107346118
- Ntoumanis, N. et al. (2021). A meta-analysis of self-determination theory-informed intervention studies in the health domain: Effects on motivation, health behavior, physical, and psychological health. *Health Psychology Review*. https://doi.org/10.1080/17437199.2020.1718529
- Patel, M.S. et al. (2019). Effectiveness of behaviorally designed gamification interventions with social incentives for increasing physical activity among overweight and obese adults across the United States: The STEP UP randomized clinical trial. *JAMA Internal Medicine*. https://doi.org/10.1001/jamainternmed.2019.3505
- Sheen, F., Porter, L., Papakonstantinou, T., Ceka, M. & Bondaronek, P. (2025). Living well? The unintended consequences of highly popular commercial fitness apps through social listening using Machine-Assisted Topic Analysis: Evidence from X. *British Journal of Health Psychology*. https://doi.org/10.1111/bjhp.70026
- Smit, E.S., Zeidler, C., Resnicow, K. & de Vries, H. (2019). Identifying the most autonomy-supportive message frame in digital health communication: A 2×2 between-subjects experiment. *Journal of Medical Internet Research*. https://doi.org/10.2196/14074
- Sweller, J. (1988). Cognitive load during problem solving: Effects on learning. *Cognitive Science*. https://doi.org/10.1207/s15516709cog1202_4
- Teixeira, P.J. et al. (2012). Exercise, physical activity, and self-determination theory: A systematic review. *International Journal of Behavioral Nutrition and Physical Activity*. https://doi.org/10.1186/1479-5868-9-78

---

## 13 — Map

- [Runapp](../README.md) — what Runapp is and how its parts fit together.
- [The Escalation Mechanism](escalation.md) — the protection system whose decisions this architecture communicates.
- [The Adaptation Engine](adaptation.md) — how sessions are modified, and why a modified session counts as a completed one.
- [Evidence Register: The Motivation Architecture](../reference/motivation-evidence-register.md) — the literature behind the design.
- [Runapp Validation](https://github.com/jonasmaandiwallin/runapp-validation) — the simulator and the plan-quality metric that measure Runapp's plans.
