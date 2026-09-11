# Input Packet Spec

This spec defines how files should be prepared for a Sentinel review.

A Sentinel input packet separates authority from evidence.

## Core distinction

| Packet section | Meaning |
| --- | --- |
| Source-of-truth | Files that define what the system is supposed to do. |
| Evidence | Files that show what has actually been written, built, tested, configured, or handed off. |

Sentinel should compare evidence against source-of-truth.

## Source-of-truth files

Source-of-truth files may include:

- project brief
- system overview
- behavior spec
- context architecture
- workflow rules
- approval boundaries
- acceptance criteria
- routing or escalation rules
- permission and visibility rules
- testing and diagnostics spec
- stakeholder decisions
- approved milestone snapshot

A source file should be included only when it is authoritative for the review goal.

## Evidence files

Evidence files may include:

- implementation files
- tests
- package/config files
- repo manifests
- API route files
- service modules
- generated docs
- handoff summaries
- test output
- implementation notes
- current-state files

Evidence should be relevant to the review goal. For example, a spec-to-repo review should prioritize implementation files, tests, routes, services, package files, and config.

## Review goal

Every packet should include a review goal.

Examples:

```yaml
review_goal: spec_to_repo
```

```yaml
review_goal: handoff_coherence
```

The review goal determines how Sentinel should prioritize source and evidence.

## Decision stage

When possible, include the stage where decisions matter.

```yaml
decision_stage: before_schema_design
```

Recommended decision stages:

- `now`
- `before_schema_design`
- `before_security_review`
- `before_implementation`
- `before_handoff`
- `before_release`
- `later`

## Packet manifest

A Sentinel packet should include or derive a manifest.

```yaml
packet_manifest:
  review_goal: spec_to_repo
  decision_stage: before_handoff
  source_files:
    - path: docs/behavior-spec.md
      authority_for:
        - assistant_behavior
        - approval_boundaries
  evidence_files:
    - path: src/triage.js
      evidence_for:
        - routing_behavior
        - escalation_handling
    - path: tests/triage.test.js
      evidence_for:
        - behavior_boundary_coverage
```

## Authority dimensions

A source file may be authoritative for specific dimensions rather than everything.

Example:

```yaml
authority_dimensions:
  docs/behavior-spec.md:
    - assistant_behavior
    - prohibited_actions
    - confirmation_rules
  docs/context-architecture.md:
    - context_types
    - retrieval_boundaries
    - permission_labels
  docs/testing-diagnostics.md:
    - expected_tests
    - diagnostic_categories
```

Sentinel should avoid using a source file as authority outside its relevant dimension unless the packet explicitly allows it.

## Recommended folder pattern

```text
sentinel-packet/
├── README.md
├── packet-manifest.yaml
├── source-of-truth/
│   ├── behavior-spec.md
│   ├── context-architecture.md
│   └── approval-criteria.md
└── evidence/
    ├── repo-manifest.md
    ├── src/
    ├── tests/
    └── implementation-notes.md
```

## Minimal packet

A minimal `spec_to_repo` packet should include:

- one behavior or requirements source file
- one implementation evidence file
- one test or config evidence file when available
- a review goal

## Coverage and truncation metadata

If files are too large to review fully, Sentinel should include coverage metadata.

```yaml
coverage:
  mode: evidence_reserved
  represented_files: 9
  truncated_files: 4
  omitted_files: 2
  reviewed_characters: 18000
  original_characters: 82000
```

This matters because a compact review may surface material drift without being a full audit.

## Missing evidence handling

If the review goal depends on evidence and evidence is missing, Sentinel should not approve.

Examples:

| Situation | Sentinel behavior |
| --- | --- |
| Spec-to-repo review with no repo files | Mark evidence gap; not approval-ready. |
| Handoff review with no implementation notes | Mark caveat; may be partially coherent or insufficient evidence. |
| Security-sensitive source rules but no relevant evidence | Mark blocker or needs security review. |

## Packet quality checklist

Before running Sentinel, check:

- Are source files clearly separated from evidence files?
- Is the review goal defined?
- Are authority dimensions clear enough?
- Are evidence files relevant to the review goal?
- Are oversized files represented honestly?
- Are unresolved decisions included rather than hidden?
- Are secrets and private data excluded?

## Principle

A good packet does not need to include everything.

A good packet includes the right authority and enough evidence to answer the review question without pretending the review is broader than it is.
