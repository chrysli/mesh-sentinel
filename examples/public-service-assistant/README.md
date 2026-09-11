# Public Service Assistant Example

This example shows how Sentinel can review a small AI-native workflow against source-of-truth behavior rules.

The example is fictional, simplified, and public-safe.

## Scenario

A Public Service Request & Feedback Triage Assistant helps users submit questions, complaints, service requests, issue reports, feedback, and follow-ups.

The assistant should preserve user intent, ask only necessary questions, protect restricted case history, support human review, avoid weak routing, and escalate when needed.

## Source packet

The curated source-of-truth packet uses two obligation-rich files from the Public Service Assistant sample:

```text
source-packet/
├── ai-design-principles.md
└── public-service-request-feedback-triage-assistant-behavior-spec.md
```

These files define expectations such as:

- preserve user intent
- use context before asking redundant questions
- distinguish known, inferred, retrieved, missing, uncertain, and restricted context
- protect restricted case history
- adapt to workflow state
- escalate urgent or sensitive cases
- require user review before submission or handoff
- preserve reviewability for human service teams

## Evidence repo

The sample evidence repo is intentionally drifted.

Suggested repo name:

```text
mesh-sentinel-public-service-sample
```

Suggested evidence files:

```text
README.md
package.json
src/triage.js
src/caseHistory.js
src/routing.js
src/summary.js
tests/triage.test.js
tests/caseHistory.test.js
tests/routing.test.js
```

## Intended drift areas

The sample implementation should intentionally include these drift areas so Sentinel has concrete issues to catch:

| Drift area | Expected Sentinel finding |
| --- | --- |
| Urgent cases auto-close | Approval boundary / escalation violation |
| Restricted related-case details exposed | Permission or visibility contradiction |
| Weak keyword routing | Unsupported inference / missing clarification |
| AI summaries marked ready to submit | Missing user confirmation boundary |
| Happy-path-only tests | Missing behavior-boundary test coverage |

## Expected Sentinel result

A compact `spec_to_repo` review should produce something like:

```yaml
coherence_status: incoherent
approval_readiness: Not approval-ready
finding_areas:
  - approval boundary / urgent auto-close
  - restricted case-history leakage
  - weak inference routing
  - missing confirmation before submission
  - missing behavior-boundary tests
```

Exact wording may vary by model and packet coverage.

## How to run the example

1. Use the two source packet files as source-of-truth.
2. Use the sample public repo as evidence.
3. Run Sentinel with review goal `spec_to_repo`.
4. Review the output for blockers, contradictions, drift risks, and recommended tests.

Example instruction:

```text
Run Sentinel as a spec-to-repo review.
Use the Public Service Assistant source packet as source-of-truth.
Use the sample public repo as implementation evidence.
Do not modify files.
Return an approval-readiness report.
```

## Why this example matters

The example demonstrates that Sentinel is not only checking syntax or code quality.

It checks whether implementation choices still obey the behavioral, contextual, and approval rules that the system was supposed to follow.

That makes it useful for reducing review load in AI-native projects where docs, specs, code, and tests are generated or changed quickly.
