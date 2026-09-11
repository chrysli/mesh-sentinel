# Output Report Schema

This spec defines the expected structure of a Sentinel report.

The report may be rendered as Markdown, JSON, YAML, HTML, or another structured format. The fields below define the semantic shape of the output.

## Top-level report fields

```yaml
sentinel_report:
  report_id: "sentinel-review-001"
  generated_at: "2026-09-11T00:00:00Z"
  review_goal: "spec_to_repo"
  review_depth: "compact"
  coherence_status: "incoherent"
  approval_readiness: "Not approval-ready"
  approval_blocker_count: 3
  summary: "Short human-readable result summary."
  packet_summary: {}
  findings: {}
  recommended_tests: []
  unresolved_decisions: []
  evidence_map: []
  caveats: []
```

## Coherence status

Use one of:

| Status | Meaning |
| --- | --- |
| `coherent` | Reviewed evidence appears aligned with source-of-truth. |
| `partially_coherent` | Some parts align, but gaps, caveats, or unresolved decisions remain. |
| `incoherent` | Material contradictions or drift were found. |
| `under_specified` | Source-of-truth does not define enough authority to judge the evidence. |
| `insufficient_evidence` | Evidence is missing or too thin for the review goal. |

## Approval readiness

Use one of:

| Readiness | Meaning |
| --- | --- |
| `Approval-ready` | No blocking findings found in reviewed evidence. Human approval may proceed. |
| `Conditionally approval-ready` | Minor caveats remain, but no material blockers found. |
| `Needs human decision` | A stakeholder/security/product decision is required before readiness can be judged. |
| `Not approval-ready` | One or more blockers exist. |
| `Insufficient evidence` | The packet does not include enough evidence for this review goal. |

Sentinel should not say “approved.” It may say “approval-ready” only in the sense that a human review can proceed.

## Packet summary

```yaml
packet_summary:
  source_file_count: 2
  evidence_file_count: 7
  reviewed_characters: 17148
  coverage_mode: evidence_reserved
  source_files:
    - path: source/ai-design-principles.md
      reviewed: true
      truncated: true
      authority_for:
        - assistant_behavior
        - escalation_boundaries
  evidence_files:
    - path: repo/src/triage.js
      reviewed: true
      truncated: true
      evidence_for:
        - triage_behavior
        - escalation_handling
```

## Finding object

All findings should use a consistent shape.

```yaml
finding:
  id: "CON-001"
  type: "contradiction"
  title: "Urgent cases are auto-closed without human review"
  severity: "high"
  blocking_status: "blocking"
  source_obligation: "Urgent or safety-related cases require escalation or human review."
  evidence_observed: "triage.js marks urgent cases as auto_closed when required fields are present."
  why_it_matters: "This bypasses the review boundary defined by the source-of-truth."
  evidence:
    source_files:
      - "docs/behavior-spec.md"
    evidence_files:
      - "src/triage.js"
  recommended_action: "Require human review/escalation before urgent cases can be resolved or closed."
```

## Finding categories

```yaml
findings:
  contradictions: []
  missing_rules: []
  drift_risks: []
  evidence_gaps: []
  unresolved_decisions: []
  advisory_notes: []
```

### Contradiction

A contradiction means evidence conflicts with source-of-truth.

Examples:

- source says user confirmation is required; implementation submits without confirmation
- source says restricted context cannot be user-facing; implementation exposes restricted details
- source says escalation required; implementation routes normally or auto-closes

### Missing rule

A missing rule means source-of-truth requires a behavior, but evidence lacks an operating rule that enforces it.

Examples:

- no approval gate exists
- no permission filter exists
- no escalation handling path exists
- no conflict handling logic exists

### Drift risk

A drift risk means evidence does not directly contradict source-of-truth yet, but creates a plausible path to misalignment.

Examples:

- keyword routing without confidence thresholds
- summaries generated without source traceability
- tests only covering happy paths
- fallback behavior that bypasses context rules

### Evidence gap

An evidence gap means the packet lacks files needed to judge a source obligation.

Examples:

- routing spec exists but no routing implementation included
- behavior spec requires tests but no tests were included
- security-sensitive flow exists but no permission code was reviewed

### Unresolved decision

An unresolved decision means Sentinel found a decision that should be made by a human owner before implementation or approval proceeds.

Fields:

```yaml
unresolved_decision:
  decision: "Should urgent cases ever be auto-closed?"
  needed_by: "before_security_review"
  likely_owner: "product / operations / governance"
  default_recommendation: "Require human review for urgent cases."
  risk_if_unresolved: "Implementation may encode a policy decision without approval."
```

## Recommended tests

Recommended tests should be specific and tied to findings.

```yaml
recommended_tests:
  - title: "Urgent case escalation test"
    purpose: "Verify urgent cases cannot be auto-closed."
    covers_findings:
      - "CON-001"
    expected_result: "Urgent cases enter escalation or human review state."
```

## Evidence map

The evidence map should help humans inspect what was reviewed.

```yaml
evidence_map:
  - file: "src/triage.js"
    role: "evidence"
    used_for:
      - "urgent auto-close finding"
      - "routing behavior review"
    reviewed_status: "truncated"
  - file: "docs/behavior-spec.md"
    role: "source_of_truth"
    used_for:
      - "approval boundary obligation"
    reviewed_status: "partial"
```

## Caveats

Caveats should be explicit when the review is compact, partial, budgeted, or otherwise limited.

Examples:

- “This was a compact live review; files were truncated.”
- “Only selected repo files were reviewed.”
- “No security implementation files were included.”
- “The report should not be treated as a full audit.”

## Report quality criteria

A good Sentinel report should be:

- structured
- evidence-linked
- decisive where source/evidence supports a conclusion
- cautious where evidence is partial
- clear about blockers
- clear about human decisions
- useful for the next engineering or product review
