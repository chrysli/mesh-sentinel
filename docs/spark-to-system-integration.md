# Spark-to-System Integration

Sentinel is the coherence checkpoint inside Spark-to-System.

Spark-to-System turns an initial idea, need, workflow, or prototype direction into an implementable AI-native system. Sentinel checks whether the system remains coherent as it moves from intent to architecture to implementation.

## Placement

```text
Spark
→ Scope
→ System intent
→ Behavior and context architecture
→ Implementation
→ Sentinel review
→ Human decision / approval
→ Next build cycle
```

Sentinel does not replace the Spark-to-System process. It reviews whether the outputs of that process still agree with each other.

## What Spark-to-System should set up

A Spark-to-System project should define enough structure for Sentinel to run later.

At minimum, project setup should include:

- source-of-truth folder or file list
- evidence folder or repo path
- review goals
- approval checkpoints
- stakeholder decision log
- test expectations
- Sentinel agent/subagent instructions

The project should make clear which files are authoritative for which dimensions. For example, a behavior spec may be authoritative for assistant behavior, while a security review may be authoritative for data-access constraints.

## Sentinel as a milestone gate

Run Sentinel when a project reaches a coherence checkpoint.

Recommended checkpoints:

| Checkpoint | Why run Sentinel |
| --- | --- |
| After scope | Check whether the scope, assumptions, and intended behavior agree. |
| Before schema design | Identify unresolved human decisions before they become data structures. |
| After implementation pass | Compare code/tests/config against source-of-truth obligations. |
| Before security review | Surface permission, boundary, and sensitive-flow issues before formal review. |
| Before handoff | Check whether handoff docs, repo state, and implementation evidence agree. |
| Before release/demo | Identify approval blockers and caveats before public exposure. |

## Review goals

Sentinel can run with different review goals depending on the checkpoint.

| Review goal | Source-of-truth | Evidence | Main question |
| --- | --- | --- | --- |
| `scope_coherence` | project brief, assumptions, architecture notes | related planning docs | Does the project direction hold together? |
| `handoff_coherence` | handoff brief, milestone docs, source docs | implementation notes, repo summaries, review notes | Is the handoff internally coherent? |
| `spec_to_repo` | behavior specs, workflow rules, acceptance criteria | repo files, tests, config | Does implementation follow the spec? |
| `drift_check` | approved baseline / prior milestone | current docs, code, tests | What changed, and did it drift from authority? |
| `approval_readiness` | approval criteria, test expectations, review policies | final packet evidence | Is this ready for human approval review? |

## Agent creation pattern

A Spark-to-System setup can include instructions to create a Sentinel agent or subagent.

The agent should be configured to:

1. identify source-of-truth files
2. identify evidence files
3. read only the files needed for the review goal
4. extract obligations from source-of-truth
5. compare evidence against obligations
6. surface contradictions and drift risks
7. classify blockers
8. recommend next tests
9. identify unresolved human decisions
10. avoid authorizing implementation

## Example Claude Code usage

After an implementation pass, the project lead can ask:

```text
Run Sentinel as a spec-to-repo review.
Use docs/behavior-spec.md and docs/context-architecture.md as source-of-truth.
Use src/, tests/, and package/config files as evidence.
Do not modify files.
Produce an approval-readiness report and mark any decisions that require human review.
```

After a milestone handoff, the project lead can ask:

```text
Run Sentinel as a handoff coherence review.
Use the milestone handoff, current-state file, and approved architecture scope as source-of-truth.
Use implementation notes, repo status, tests, and open issues as evidence.
Identify contradictions, missing rules, drift risks, and decisions needed before the next milestone.
Do not authorize implementation.
```

## Human decision boundary

Sentinel can reduce review load, but it should not remove human judgment.

Sentinel may say:

- “This appears coherent based on reviewed evidence.”
- “This is not approval-ready because blockers remain.”
- “This requires stakeholder decision before schema design.”
- “This needs security review before implementation proceeds.”

Sentinel must not say:

- “Implementation is approved.”
- “Security review is complete.”
- “Stakeholder decision is resolved.”
- “The system is safe to ship.”

## Integration principle

Spark-to-System creates the project’s declared operating truth.

Sentinel checks whether the rest of the project still obeys that truth.
