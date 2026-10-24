# Assignment 1: BEOL experiment results

This is a factual transcription of selected values in the submitted `AdvTraining_Tutorial_1_Exp_v2.csv`, not a new simulation. The source reports four runs completed on 20 October 2026. Local paths, account names, temporary-file flags, and machine-specific command details have been omitted.

## Experimental inputs

The boundary mask is `BB_Search` for all runs. The lithography parameter is `3.2.4:lithography.dimensionalBias.xBias` (step label `LTH Mx`), and the trench parameter is `3.7.5:type.basic.lateralRatio` (step label `Trench ME`). The two input values for each parameter form a 2-by-2 sweep.

| Run | Lithography x-bias | Trench lateral ratio | Via-bottom CD | Via contact area | MIN_LW value | MIN_Insulator value |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Run-0 | -2 | 0.01 | 37.13623456 | 1188.421875 | 28.5 | 34.78125 |
| Run-1 | -2 | 0.05 | 40.77589404 | 1310.78125 | 32 | 31.5 |
| Run-2 | 2 | 0.01 | 42.14552867 | 1332.078125 | 32 | 27.65625 |
| Run-3 | 2 | 0.05 | 45.82347249 | 1421.332031 | 32.5 | 23.5 |

Units are not specified in the CSV column headers and have not been independently validated against the model. Values are retained as exported; do not assume area and length share the same units.

## Check outcomes

| Run | MIN_LW status | MIN_LW locations | MIN_Insulator status | MIN_Insulator locations | NUM_Nets status | NUM_Nets locations | NUM_Nets value |
| --- | --- | ---: | --- | ---: | --- | ---: | ---: |
| Run-0 | FAILED | 2 | FAILED | 1 | MEASURED | 0 | 2 |
| Run-1 | FAILED | 2 | FAILED | 1 | MEASURED | 0 | 2 |
| Run-2 | FAILED | 2 | FAILED | 1 | MEASURED | 0 | 2 |
| Run-3 | FAILED | 3 | FAILED | 1 | MEASURED | 0 | 2 |

## Interpretation

Within these four samples, larger lithography x-bias and larger trench lateral ratio each accompany larger via-bottom CD and contact area when the other parameter is fixed. Minimum-insulator values decrease across those same comparisons. These are observations from a small tutorial sweep, not an optimized process window, a yield estimate, or a general causal model.

Both rule-check categories fail for every run. The archive alone does not establish a passing threshold or a corrective solution. `MEASURED` for net count is not a connectivity sign-off or a passed electrical test.

## Source and attribution

Source: the Assignment 1 archive's Module 2 training folder, experiment subfolder `AdvTraining_Tutorial_1_Exp_v2`, results file of the same name. The underlying BEOL process is a supplied training example. The user submitted the run outputs as coursework; this summary does not claim the underlying process recipe as an original invention. The original path-containing CSV and native model dependencies are not included in this portfolio.
