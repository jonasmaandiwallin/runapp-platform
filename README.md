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

Runapp is an evidence-based running coach. It builds an individual training plan from a measurement of the runner's fitness, and it adapts the plan as the runner's body and life change. Every rule it follows, and every threshold in those rules, traces back to the research that supports it.

Runapp is not public yet. This repository is where its documentation will be published, starting soon.

## What Runapp is

- **Evidence-governed.** The thresholds that steer a plan — recovery spacing, intensity ceilings, how fast volume may grow, when to protect a runner from injury — live in one registry. Each value names the evidence behind it and how strong that evidence is. When the research changes, the value changes in one place, and the change is recorded.
- **Specified before it is built.** Every behaviour is written down first, as a contract that says what the system must do, what it must never do, and how a violation shows. The code implements the contracts, and the contracts are checked against the code.
- **Adaptive.** Signals from the runner — heart rate and its variability, sleep, training load, missed sessions, perceived effort, pain — flow in continuously. The plan pushes harder when they are strong and holds back when they deteriorate, within limits the contracts set.
- **Measured.** Plan quality is not asserted. It is measured with a plan-quality model and a simulator that runs the plans against modelled runners before a real runner ever follows them.

## The platform

Runapp is one coaching engine with several ways in: an iOS app that reads the runner's health data and training history, an app inside ChatGPT, and more to come.

## What will be published here

- **Runapp's architecture:** how the engine designs, protects and adapts a plan, and how the runner is kept motivated.
- **The evidence registers** behind each part.

How plan quality is measured — the plan-quality model and the simulator — will be published in its own repository, `runapp-validation`.

## How Runapp was built

Runapp was built with Governed Agentic Engineering (GAE), a set of standards for building with AI agents where the agents that write the specification are governed as strictly as the agents that write the code. Runapp is GAE's reference implementation. The standards and a starter kit are published at [governed-agentic-engineering](https://github.com/jonasmaandiwallin/governed-agentic-engineering).

## License and contact

Text in this repository is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/), attributed to Jonas Maandi Wallin. The full text is in `LICENSE`.

Contact: [Jonas Maandi Wallin on LinkedIn](https://www.linkedin.com/in/jonasmaandiwallin).
