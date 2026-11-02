# Appendix: Worked Example of Evidence Recovery

This appendix expands the Chapter 5 example by following one source observation through Workspace retrieval. The execution is selected because it exposes a clear admission boundary, an explicit recovery action, and a final snippet that can be checked directly against the repository snapshot. It illustrates how the system handles evidence rather than measuring how frequently recovery succeeds.

## Execution and Source

| Item | Recorded value |
|---|---|
| Testcase | TypeScript 35468 |
| Request context | Investigate why a code change does not produce the expected compiler error under watch mode with project references and wildcard re-exports. |
| Configuration | Full Workspace |
| Repetition | 4 |
| Run ID | `run-20260916T034916Z` |
| Pre-resolution commit | `f7860b048037bd74021ec0557a62688ec57e33c1` |
| Source | `src/compiler/builder.ts`, lines 664–680 |
| Function | `getBinderAndCheckerDiagnosticsOfFile` |
| Observation ID | `obs_492dfe725960df80` |

## Complete Retrieved Snippet

The source below makes the retrieval record inspectable. Understanding every compiler-specific detail is unnecessary for following the evidence lifecycle.

```typescript
function getBinderAndCheckerDiagnosticsOfFile(state: BuilderProgramState, sourceFile: SourceFile, cancellationToken?: CancellationToken): readonly Diagnostic[] {
    const path = sourceFile.path;
    if (state.semanticDiagnosticsPerFile) {
        const cachedDiagnostics = state.semanticDiagnosticsPerFile.get(path);
        // Report the bind and check diagnostics from the cache if we already have those diagnostics present
        if (cachedDiagnostics) {
            return cachedDiagnostics;
        }
    }

    // Diagnostics werent cached, get them from program, and cache the result
    const diagnostics = Debug.assertDefined(state.program).getBindAndCheckDiagnostics(sourceFile, cancellationToken);
    if (state.semanticDiagnosticsPerFile) {
        state.semanticDiagnosticsPerFile.set(path, diagnostics);
    }
    return diagnostics;
}
```

The source retains its original wording. Indentation is reduced for presentation; long lines wrap in the typeset listing.

## Detailed Trace

Event references identify line numbers in the run's `retrieval-trace.jsonl`.

| Stage | Recorded decision or result | Event reference |
|---|---|---|
| Initial repository search | Range 660–681 appears in sparse results for two obligations, at channel ranks 7 and 6. It also appears in one hybrid result list at rank 8. Matched terms include “cache,” “cached,” “program,” “state,” “affected,” and “diagnostic.” | 35, 45 |
| Prepare canonical snippets | CodeGraph resolves the range to the function at 664–680. Canonicalisation records it as observation `obs_492dfe725960df80`. | 51–52 |
| Admission to owner comparison | The pool contains 406 canonical snippets. Admission includes 76 snippets. The target occupies position 77 and receives `excluded_after_budget_crossing`. | 54 |
| Owner comparison and initial selection | The target is outside the comparison request and does not enter round-zero qualification. Another owner from the same file proceeds. | 59–60 |
| Controller scheduling | Round 1 schedules action `action_d923049e53dc1041` in the deferred-file-rescue pool, using the preserved observation as its seed. | 464 |
| Controller execution | A search within `src/compiler/builder.ts` returns the target observation and two others. The action succeeds without a recorded materialisation loss. | 485 |
| Source disclosure and qualification | The round-1 qualification request includes the target. Qualification promotes it as direct evidence for the state-change obligation and records a further retrieval need concerning cache invalidation. | 510, 513 |
| Candidate-pool construction | The same structural candidate enters the pool supplied to final selection. | 1649 |
| Final evidence selection | Selection accepts the function. The final artifact, `evidence-items.json`, contains its complete source at lines 664–680 as evidence item 7. This is a snippet position, not a file-ranking measure. | 1657–1658, 1670 |

The initial search positions and the later admission position describe different rankings. A snippet can appear relatively early in an individual query's results yet fall outside the globally assembled comparison request.

## Why the Snippet Remains Deferred

Admission operates against a preferred 60,000-character input target and a 100,000-character maximum. It includes the snippet that crosses the preferred target, then stops.

| Admission position | Snippet | Request size after admission | Outcome |
|---|---|---|---|
| 76 | `ReusableBuilderProgramState`, lines 21–60 in the same file | 61,002 characters | Admitted; crosses the preferred target |
| 77 | `getBinderAndCheckerDiagnosticsOfFile`, lines 664–680 | No further admission | Preserved as deferred |

These two snippets have identical principal ranking signals: neither has an exact-anchor match, both recur across two query views, and both have the same best grouped rank and score. Their earlier priority components and file path also match. Source position breaks the tie, placing the declaration at line 21 first.

The record therefore supports a specific explanation: the target falls immediately after the admission boundary. It does not show that the preceding snippet is semantically better, that the target lacks relevant keywords, or that an LLM judges it irrelevant.

## Why the Controller Revisits It

The controller still has an unresolved information need concerning changes to checking state. Its recovery action searches for:

> Which state-update function clears or repopulates semanticDiagnosticsPerFile and affected files after a shape change?

This query uses the preserved observation to guide a bounded search within its file. The action returns the same observation, making it available for source disclosure and qualification.

Recovery alone does not grant evidential support. Qualification subsequently retains the snippet as direct evidence about diagnostic-cache behaviour and records what remains missing. Final selection then accepts it within the evidence assembled for the request.

## What the Trace Demonstrates

The same observation can be followed from initial retrieval to deferred admission, controller recovery, qualification, and final selection. Its initial omission is reversible because the pipeline preserves it for possible later use. Its eventual acceptance is a separate decision supported by source inspection.

The run remains partial and insufficient overall. This example demonstrates recovery of one useful snippet, not complete recovery of the testcase's required evidence or a complete diagnosis of the reported issue.
