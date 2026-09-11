# Mesh Sentinel

Mesh Sentinel is a coherence review layer for AI-native projects.

It checks whether the system being built still follows the rules the team already wrote: behavior specs, workflow rules, context architecture, approval boundaries, testing expectations, and other source-of-truth artifacts.

Sentinel is not a generic code reviewer. It does not ask, “Is this code good?” It asks a narrower and more useful question:

> Does the implementation evidence still obey the project’s source-of-truth?

## Why Sentinel exists

AI-native teams can generate requirements, specs, docs, code, tests, and architecture notes faster than traditional review processes can keep aligned. That creates review load and drift risk.

Common failure patterns include:

- code that contradicts behavior specs
- workflow rules that are not enforced in implementation
- approval boundaries that disappear during build
- permission or visibility rules that are not reflected in code
- tests that cover happy paths but miss critical behavior boundaries
- handoff docs that say one thing while repo evidence says another
- unresolved decisions being treated as implementation approval

Sentinel reduces review load by turning “please review this project” into a structured coherence check.

## Core model

Sentinel compares two sides of a project packet:

| Side | Meaning | Examples |
| --- | --- | --- |
| Source-of-truth | The authority Sentinel checks against | behavior specs, workflow rules, context architecture, acceptance criteria, approval policies |
| Evidence | What actually exists | repo files, tests, implementation notes, configs, handoff docs, runtime behavior notes |

The output is an approval-readiness report that surfaces contradictions, missing rules, drift risks, unresolved human decisions, recommended tests, and approval blockers.

## Where Sentinel fits

Sentinel can run as:

- an internal review harness
- a Claude Code subagent
- a repo review workflow
- a milestone checkpoint in Spark-to-System
- a hosted demo or prototype surface

The hosted interface is only one surface. The reusable pattern is the review spec, input packet, and approval-readiness harness.

## When to run Sentinel

Run Sentinel at coherence checkpoints:

- after architecture scope is drafted
- before schema design
- after implementation passes
- before security review
- before handoff
- before public release or demo submission
- whenever generated work may have drifted from the project’s declared rules

## What Sentinel outputs

A Sentinel report should include:

- coherence status
- approval readiness
- approval blockers
- contradictions
- missing operating rules
- behavior or architecture drift risks
- unresolved stakeholder decisions
- recommended next tests
- evidence map / reviewed file trace
- caveats about coverage, truncation, or evidence limits

## Human decision boundary

Sentinel may recommend whether something appears approval-ready, not approval-ready, partially coherent, or under-specified.

Sentinel must not authorize implementation, override stakeholders, replace security review, or silently resolve unresolved decisions.

## First-round repo map

```text
mesh-sentinel/
├── README.md
├── docs/
│   └── spark-to-system-integration.md
├── specs/
│   ├── sentinel-agent-spec.md
│   ├── input-packet-spec.md
│   ├── output-report-schema.md
│   └── approval-readiness-harness.md
├── agents/
│   └── claude-code-sentinel-subagent.md
└── examples/
    └── public-service-assistant/
        └── README.md
```

## Status

Draft v0.1. Public-safe working spec. Intended for review, adaptation, and internal use before wider publication.
