# Evaluation

This chapter reports the evaluation described in Chapter 4. It comprises 35 CodeRepoQA-derived cases, five retrieval conditions, and four valid repetitions of every case-condition pair, giving 700 accepted runs. All conditions use `gpt-5.6-luna`, receive the same issue and pre-resolution repository, skip response generation, and retain final evidence selection. The chapter first reports file-level results, then compares the four Workspace configurations and Full Workspace with Codex. It examines cost, stability, and selected cases separately.

## Overall Retrieval Performance

Table 7.1 presents the principal ranking results across all 35 cases. Codex produces the strongest aggregate results: its R@5 is 0.677, compared with 0.508 for Full Workspace, and its NDCG@5 is 0.511, compared with 0.401. Among the native conditions, Workspace without CodeGraph places an implementation Oracle first most often, reaching P@1 of 0.493. Full Workspace and the two controller-disabled conditions reach 0.329, 0.321, and 0.464 respectively. This early-rank difference does not extend to recall at five files: Full Workspace and Workspace without CodeGraph obtain nearly identical R@5 values of 0.508 and 0.505.

| Condition | P@1 | P@5 | R@5 | R@10 | NDCG@5 | NDCG@10 |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Full Workspace | 0.329 | 0.191 | 0.508 | 0.517 | 0.401 | 0.373 |
| Codex | 0.386 | 0.260 | 0.677 | 0.709 | 0.511 | 0.502 |
| Without CodeGraph | 0.493 | 0.200 | 0.505 | 0.508 | 0.463 | 0.427 |
| Without adaptive controller | 0.321 | 0.186 | 0.465 | 0.473 | 0.389 | 0.360 |
| Without either capability | 0.464 | 0.186 | 0.479 | 0.481 | 0.433 | 0.401 |

<!-- POST-REPAIR STATISTICS NOTE: Replace Table 7.1 and its interpretation with the completed repaired-evaluation aggregates. Do not insert the current ten-case values as final statistics. The interim matched comparison—Full Workspace P@1 0.275→0.450, NDCG@5 0.387→0.472, and R@5 0.473→0.453—shows the likely change in interpretation but is not the final table. -->

The development and held-out partitions follow the same broad ordering. The seven held-out cases provide an independent check across all issue categories, although their smaller number makes the exact aggregate values more sensitive to individual cases. Codex has the highest R@5 and NDCG@5 in both partitions. R@5 is higher on the held-out set for all four native conditions, including the combined ablation, whose value rises from 0.398 to 0.804. This does not indicate that held-out cases are generally easier; it reflects the particular seven selected cases. Its useful role is to show that the principal aggregate patterns are not confined to cases used during formative development.

| Partition | Condition | P@5 | R@5 | NDCG@5 | Any implementation hit | Full implementation recall |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| Development | Full Workspace | 0.188 | 0.474 | 0.392 | 0.670 | 0.366 |
|  | Codex | 0.246 | 0.596 | 0.464 | 0.866 | 0.473 |
|  | Without CodeGraph | 0.195 | 0.453 | 0.439 | 0.661 | 0.312 |
|  | Without adaptive controller | 0.184 | 0.434 | 0.391 | 0.616 | 0.295 |
|  | Without either capability | 0.173 | 0.398 | 0.385 | 0.607 | 0.268 |
| Held-out | Full Workspace | 0.207 | 0.643 | 0.437 | 0.714 | 0.571 |
|  | Codex | 0.314 | 1.000 | 0.700 | 1.000 | 1.000 |
|  | Without CodeGraph | 0.221 | 0.714 | 0.560 | 0.786 | 0.643 |
|  | Without adaptive controller | 0.193 | 0.589 | 0.382 | 0.679 | 0.571 |
|  | Without either capability | 0.236 | 0.804 | 0.626 | 0.857 | 0.750 |

Repository and issue-category results vary considerably. Codex has the highest R@5 in TypeScript, pandas, and Vue. The native differences are less uniform: removing CodeGraph slightly increases R@5 for TypeScript and Vue but reduces it for pandas, while controller removal reduces aggregate native recall but not every category. These small groups therefore locate heterogeneous behaviour rather than rank repositories or establish that a component is uniformly beneficial for a particular language.

## Contributions of CodeGraph and Adaptive Exploration

The four native conditions form the two-factor comparison defined in Chapter 4. Table 7.3 reports each capability's observed difference while the other capability is either present or absent. A positive value means that enabling the named capability increased the measure.

| Capability contrast | P@1 | R@5 | NDCG@5 | Any hit | Full recall | Mean flow-token difference |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Adaptive controller, with CodeGraph | +0.007 | +0.042 | +0.012 | +0.050 | +0.057 | +47,749 |
| Adaptive controller, without CodeGraph | +0.029 | +0.026 | +0.030 | +0.029 | +0.014 | +36,002 |
| CodeGraph, with adaptive controller | -0.164 | +0.002 | -0.062 | -0.007 | +0.029 | +13,137 |
| CodeGraph, without adaptive controller | -0.143 | -0.014 | -0.044 | -0.029 | -0.014 | +1,390 |

<!-- POST-REPAIR STATISTICS NOTE: Replace this factorial table with contrasts calculated from the completed repaired evaluation. On the current ten-case subset, CodeGraph with adaptive exploration changes P@1 by 0.000, R@5 by +0.054, and NDCG@5 by +0.023; without the controller it changes P@1 by -0.100, R@5 by +0.009, and NDCG@5 only negligibly. Use these only to guide the eventual interpretation and recalculate every row from the final artifacts. -->

Adaptive exploration produces modest positive aggregate differences under both structural settings. With CodeGraph enabled, it increases R@5 from 0.465 to 0.508 and full implementation recall from 0.350 to 0.407. Without CodeGraph, the corresponding increases are smaller: R@5 rises from 0.479 to 0.505 and full recall from 0.364 to 0.379. The direction is consistent with the controller recovering some relevant evidence after round zero, but the scale is limited relative to its additional model use. It also does not transform the native sufficiency outcome discussed below.

CodeGraph shows a different pattern. With the controller enabled, it changes R@5 by only +0.002 and full recall by +0.029, while P@1 falls by 0.164 and NDCG@5 by 0.062. Without adaptive exploration, enabling CodeGraph reduces all four reported quality measures. The early-rank difference therefore cannot be attributed only to graph-driven controller expansion: it remains when both conditions stop after round zero. At the same time, Full Workspace recovers all implementation Oracles slightly more often than the graphless condition, and individual positive cases are present. For pandas 10068, for example, Full Workspace achieves full recall in four of four repetitions, compared with one of four without CodeGraph. The results show that structural processing can recover useful evidence in individual cases, but can also introduce or prioritise additional structural candidates that reduce early file-ranking quality. Its contribution is therefore beneficial in some cases without producing a consistent aggregate improvement.

<!-- POST-REPAIR INTERPRETATION NOTE: Replace this interpretation after the repaired evaluation completes. The current rerun indicates that the earlier controller-enabled CodeGraph penalty was caused at least partly by defective source-range representation: Full Workspace tied graphless at P@1 and exceeded it at R@5 and NDCG@5. The new interpretation must still preserve the controller-disabled P@1 penalty and the pandas 10068 regression rather than claiming universal benefit. -->

The interaction between the capabilities is small and measure-dependent. Adaptive exploration improves R@5 more with CodeGraph than without it, whereas it improves NDCG@5 more in the graphless pipeline. CodeGraph's slight full-recall benefit appears only when the controller is active. These descriptive differences suggest that the controller sometimes uses structural evidence productively, but they do not show a general dependency between the components.

## Evidence Survival and Mechanism Completeness

File ranking describes whether known fixing files reach the returned evidence, but it does not establish whether the selected source explains the requested mechanism. The final selector labels 135 Full Workspace runs `partial`, four `missing`, and one `strong`; it marks only one of the 140 runs sufficient. It labels every run in each native ablation partial and insufficient, while Codex labels all 140 runs strong and sufficient. As Chapter 4 establishes, these are the systems' own judgements rather than independent completeness labels. The contrast therefore shows a substantial difference in output policy or confidence, but it cannot by itself establish that all Codex mechanisms are complete or that almost every native result is objectively incomplete.

<!-- POST-REPAIR STATISTICS NOTE: Replace these native coverage totals with the completed repaired-evaluation outputs. The current 160 accepted native runs are all `partial/false`, which indicates that better prioritisation has not yet produced mechanism completeness. Update the Codex comparison only if Codex is included in the final replacement evaluation. -->

The Full Workspace controller usually exhausts its ordinary exploration allowance. It stops after the normal three-round budget in 113 runs and reaches the conditional round limit in another 16. Nine runs stop after a round produces no evidence gain, one stops when every required obligation is covered, and one stops when no executable action remains. Across 423 executed rounds, the trace records evidence and navigation gains in 347 rounds and coverage improvement in 137. Later exploration is therefore frequently productive at the candidate level, but new candidates translate into only modest aggregate recall gains and almost no sufficient outcomes.

Saved traces also show that an evidence identity can survive while the source visible to a later LLM becomes less complete. Across 563 recorded coverage calls, 239 require source truncation. The traces contain 1,461 instances in which a candidate's visible source becomes shorter than in an earlier round; in 164 of the 195 affected calls, the previous views collectively fit within the existing total character budget. This diagnostic does not change the evaluation metrics, but it identifies a pipeline boundary that file-level recall cannot reveal: preserving a candidate ID is not equivalent to preserving all source needed to assess its contribution.

The evaluation does not include an independent owner-level or causal-transition Oracle for every case. Aggregate claims about mechanism completeness must therefore remain limited to final file recovery, selector-produced coverage states, and auditable examples. The traces can identify whether particular evidence is absent, transformed, rejected, or restored, but those case findings should not be generalised into a corpus-wide mechanism-completeness rate.

## Full Workspace versus Codex

Codex exceeds Full Workspace on every headline ranking measure. It returns at least one implementation Oracle in 89.3% of runs, compared with 67.9% for Full Workspace, and recovers the complete implementation Oracle in 57.9%, compared with 40.7%. Its advantage is especially visible by rank five: R@5 is higher by 0.169 and NDCG@5 by 0.110. The held-out comparison is stronger still, with Codex reaching complete recall in all 28 held-out repetitions and Full Workspace doing so in 16.

The comparison also reflects different output behaviour. Codex returns 6.49 unique files on average, whereas Full Workspace returns 2.96. Some of its recall advantage therefore comes with a broader evidence set rather than only better prioritisation. Standard P@5 still favours Codex, however, so the additional files do not merely dilute its result: implementation evidence remains more concentrated within the first five positions.

This remains a complete-system comparison. Codex uses its own iterative repository inspection and decides how to navigate, stop, and assemble evidence, whereas Workspace uses the staged evidence lifecycle described in Chapter 5. The numerical difference cannot be assigned to agentic navigation alone, nor does the universal Codex `strong` and `sufficient` output constitute external validation of its explanations. The defensible result is narrower: under the evaluated configurations, Codex recovers and ranks the file Oracle more effectively and returns broader evidence, while the native pipeline exposes more conservative coverage judgements and substantially lower model-token use.

## Efficiency, Stability, and Failures

Table 7.4 places the quality results beside operational cost and repeated-run stability. Mean pairwise Jaccard similarity measures agreement among the four returned file sets for each case, then averages those case values. A higher value indicates more repeatable file selection. The mean R@10 standard deviation measures variation in recall, with lower values indicating greater stability.

| Condition | Mean files | Mean flow tokens | Mean seconds | File-set Jaccard | Mean SD of R@10 |
| --- | ---: | ---: | ---: | ---: | ---: |
| Full Workspace | 2.96 | 95,315 | 227.5 | 0.545 | 0.083 |
| Codex | 6.49 | 283,913 | 121.7 | 0.685 | 0.033 |
| Without CodeGraph | 2.81 | 82,177 | *not comparable* | 0.597 | 0.098 |
| Without adaptive controller | 2.66 | 47,566 | 127.4 | 0.657 | 0.079 |
| Without either capability | 2.50 | 46,176 | 115.8 | 0.601 | 0.092 |

<!-- POST-REPAIR STATISTICS NOTE: Replace these cost and stability values with measurements from the completed repaired evaluation. Do not infer them from the ranking figures supplied so far. Ensure that the replacement graphless runs contain valid timing records so the old timing caveat can be removed rather than carried into the final evaluation. -->

Codex uses approximately three times the flow tokens of Full Workspace but completes sooner on average. Provider-reported tokens and elapsed time therefore describe different operational costs and should not be treated as interchangeable measures. Disabling adaptive exploration nearly halves Workspace token use and reduces mean runtime by about 100 seconds. Removing CodeGraph while retaining the controller reduces mean flow tokens by approximately 13,100, while removing it after controller removal saves only about 1,400. The controller, rather than CodeGraph, is therefore the dominant source of native retrieval-flow token cost.

Runtime is not reported for Workspace without CodeGraph, as several imported runs have placeholder zero-duration timestamps and one run's recorded interval includes an approximately four-hour interruption. These records do not provide a reliable estimate of ordinary execution time. Token accounting and final artifacts are available and remain included. The condition ledgers retain invalid infrastructure or schema attempts, but the validity rule in Chapter 4 excludes them from the aggregates; collection continues until every cell contains four valid runs.

Repeated-run agreement is moderate for every condition. Codex has the highest mean file-set Jaccard similarity and the lowest R@10 variation. Removing the controller increases Workspace file-set agreement from 0.545 to 0.657, consistent with eliminating stochastic later exploration, although round-zero semantic stages and final evidence selection remain model-backed. Graphless retrieval does not improve recall stability: its mean R@10 standard deviation is the highest of the five conditions.

## Representative Trace Cases

<!-- POST-REPAIR TRACE NOTE: Chapter 6 now contains the Vue 10519 diagnosis and the resulting owner-validation rationale. Add only the case's measured outcome here after the replacement evaluation is complete; do not repeat the repair mechanism in Chapter 7. -->

Pandas 10068 illustrates a positive structural and controller interaction. Full Workspace recovers its complete implementation Oracle in every repetition. Removing CodeGraph reduces this to one repetition, removing only the controller to two, and removing both to none. A saved Full Workspace trace also shows a limitation: the relevant `_binop` owner remains in final evidence, but its visible source shrinks during a later coverage call and loses part of the function signature and return context. The case therefore supports both sides of the aggregate result. Structural and adaptive processing can preserve an implementation that the simpler variants often miss, while survival of its file and identity does not guarantee stable presentation of its complete proof.

<!-- POST-REPAIR CASE NOTE: Replace this historical positive example. In the current repaired runs, Full Workspace recovered complete implementation evidence in only 1/4 runs rather than the old 4/4. The final evaluation must report this regression and should not attribute its cause to owner repair without a boundary audit. -->

TypeScript 35468 provides a second controller-sensitive mechanism case. Full Workspace reaches R@5 of 0.625 and complete recall in two of four repetitions. The graphless condition reaches the same R@5 but never complete recall, while both controller-disabled conditions reach R@5 of 0.500 and also never complete the Oracle. The result is consistent with later exploration helping assemble more of a multi-file mechanism, but the variation across repetitions prevents treating one recovered set as a deterministic controller outcome.

Finally, TypeScript 19074 shows the size of the complete-system divergence on a difficult held-out case. Codex recovers the full implementation Oracle in all four repetitions. Full Workspace finds an implementation Oracle once and never achieves full recall; the graphless condition behaves similarly, and both controller-disabled conditions produce no final implementation overlap. These final outputs do not reveal whether the native pipeline fails at raw retrieval or at a later selection boundary. They establish only that the agentic system repeatedly returns the known implementation while the native evidence pipeline does not.

Together, the aggregate and trace results separate three findings. Codex provides the strongest file localisation at the highest token cost. Adaptive exploration supplies small but consistent native recall gains while accounting for most of the native retrieval-flow cost. CodeGraph does not improve aggregate early file ranking and only slightly improves complete recall when paired with the controller, although individual cases show useful structural recovery. The next chapter interprets these findings in relation to the research questions and the limits of the file-level Oracle.

<!-- POST-REPAIR SUMMARY NOTE: Replace this summary with conclusions from the completed repaired evaluation. The current direction is that the controller-enabled CodeGraph ranking penalty disappears, recall remains broadly stable, the controller-disabled interaction stays mixed, and all current native runs remain `partial/false`. Retain a Codex conclusion only if the replacement evaluation includes a directly comparable Codex condition. -->
