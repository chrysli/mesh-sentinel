# Approval-Readiness Harness

The approval-readiness harness turns Sentinel findings into a readiness judgment.

It exists to prevent false approval, especially when a review finds contradictions, missing boundaries, evidence gaps, or unresolved human decisions.

## Principle

Sentinel should be conservative about approval.

A project may be promising, useful, or partially coherent and still be not approval-ready.

## Readiness levels

| Level | Meaning |
| --- | --- |
| `Approval-ready` | Reviewed evidence shows no blockers. Human approval can proceed. |
| `Conditionally approval-ready` | Minor issues remain, but no material blockers were found. |
| `Needs human decision` | Readiness depends on a stakeholder, security, product, or operations decision. |
| `Not approval-ready` | Blocking contradictions, missing rules, or drift risks exist. |
| `Insufficient evidence` | Sentinel cannot judge readiness because evidence is missing or too thin. |

## Coherence statuses

| Status | Meaning |
| --- | --- |
| `coherent` | Evidence appears aligned with source-of-truth. |
| `partially_coherent` | Some alignment exists, but review limits or unresolved gaps remain. |
| `incoherent` | Material contradictions or drift found. |
| `under_specified` | Source-of-truth is not specific enough. |
| `insufficient_evidence` | Evidence is not enough for the review goal. |

## Blocking rules

A finding should block approval when it involves:

- contradiction with source-of-truth
- approval or human-review boundary bypass
- permission, visibility, or restricted-context leakage
- security-sensitive behavior without review
- irreversible or externally visible action without confirmation
- routing or escalation authority not supported by rules
- material missing rule required by the source-of-truth
- missing evidence for an evidence-dependent review goal
- unresolved stakeholder decision required before the current stage

## Default mapping

| Condition | Coherence status | Approval readiness |
| --- | --- | --- |
| Any critical blocker | `incoherent` | `Not approval-ready` |
| High contradiction | `incoherent` | `Not approval-ready` |
| Missing approval/security/permission rule | `partially_coherent` or `incoherent` | `Not approval-ready` |
| Evidence missing for spec-to-repo review | `insufficient_evidence` | `Insufficient evidence` or `Not approval-ready` |
| Under-specified source-of-truth | `under_specified` | `Needs human decision` |
| Only low-severity advisory issues | `coherent` or `partially_coherent` | `Conditionally approval-ready` |
| No material findings and sufficient evidence | `coherent` | `Approval-ready` |

## Human decision timing

Sentinel should mark when a decision is needed.

| Timing | Meaning |
| --- | --- |
| `now` | Needed before any further implementation or approval. |
| `before_schema_design` | Needed before data structures or persistence encode the decision. |
| `before_security_review` | Needed before security or permission review can be meaningful. |
| `before_implementation` | Needed before developers proceed. |
| `before_handoff` | Needed before the work can be handed to another team. |
| `before_release` | Needed before public/demo/production release. |
| `later` | Can be deferred without blocking the current step. |

## Harness logic

Pseudo-logic:

```text
if source_of_truth_missing:
  coherence_status = under_specified
  approval_readiness = Needs human decision

else if evidence_required and evidence_missing:
  coherence_status = insufficient_evidence
  approval_readiness = Insufficient evidence

else if critical_or_high_blocking_contradiction_exists:
  coherence_status = incoherent
  approval_readiness = Not approval-ready

else if missing_required_boundary_exists:
  coherence_status = partially_coherent or incoherent
  approval_readiness = Not approval-ready

else if unresolved_decision_blocks_current_stage:
  coherence_status = partially_coherent
  approval_readiness = Needs human decision

else if only_advisory_or_low_findings_exist:
  coherence_status = coherent or partially_coherent
  approval_readiness = Conditionally approval-ready

else:
  coherence_status = coherent
  approval_readiness = Approval-ready
```

## Contradictions always matter

A contradiction between source-of-truth and evidence should not be downgraded just because the implementation appears intentional.

Intentional implementation can still be incoherent if it violates the declared rules.

## Evidence gaps

Evidence gaps should block approval when the review goal depends on the missing evidence.

Example:

- In a `spec_to_repo` review, missing repo files should block readiness.
- In a `handoff_coherence` review, missing implementation detail may produce a caveat or blocker depending on the handoff claim.
- In an `approval_readiness` review, missing test evidence should usually block readiness if tests are part of approval criteria.

## Compact review caveats

A compact review may identify real blockers, but it should not overclaim completeness.

If files are truncated or omitted, the report should say so.

A compact review can say:

> “Blocking drift was found in reviewed evidence.”

It should avoid saying:

> “No other drift exists.”

unless a full review was actually performed.

## Approval wording

Allowed wording:

- “Appears approval-ready based on reviewed evidence.”
- “Not approval-ready.”
- “Needs human decision before approval.”
- “Insufficient evidence to determine approval readiness.”

Avoid wording:

- “Approved.”
- “Safe to ship.”
- “Security complete.”
- “All issues resolved.”

## Harness goal

The harness should make the next human review easier by answering:

1. Can this move forward?
2. What blocks it?
3. Who needs to decide?
4. What evidence supports the finding?
5. What test should be run next?
