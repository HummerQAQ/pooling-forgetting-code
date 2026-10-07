# Supplementary code

Anonymized code and result files accompanying a conference submission under review.

Naming: the method is called EBB in the paper. In the code, `eb_hurdle` (label `EB-Hurdle`) is the
hierarchical empirical-Bayes hurdle model, and `EBB` / `ebb` denotes that model with the selected
pooling structure and discount.

## Layout
- `src/models/eb_hurdle.py`     hierarchical empirical-Bayes hurdle model, discounted sufficient
                                 statistics, joint selection (`select_pooling_and_discount`), online updates
- `src/models/mixture_pooling.py` learned partition (EM mixture of priors, BIC)
- `src/models/conformal.py`, `baselines.py`, ...   comparators
- `src/experiments/`            fixed-origin and walk-forward drivers, scoring
- `scripts/integrity/`          one script per reported experiment (see the table below)
- Some script headers name internal design notes (`docs/DESIGN_*.md`); these are working notes
  and are not part of this archive.
- `results/`                    the small result files from which the paper's tables and figures are
                                 generated, grouped by topic (see the table below)
- `tests/`                      symbolic and determinism checks of the theory and the scalar code path

## Environment
Python 3.10+ with numpy, pandas, scipy, statsforecast, matplotlib (`pyproject.toml`).
Set `ARS_SF_NJOBS=1` before running any script that calls StatsForecast or the conformal wrappers.
TweedieGP is run from its authors' released implementation in a separate environment; it is not
redistributed here.

## Data
All five panels are public. Online Retail (UCI Machine Learning Repository) and the M5 accuracy
competition files are read from `data/`; the three monthly panels (Auto, Carparts, RAF) are those
distributed with the TweedieGP paper and are converted to long format by
`src/tools/convert_intermittent_datasets.py`. The M5 sample is the seed-42 sample of 5,000 series
drawn by `preprocess_m5`.

## Paper item -> script -> result file
| Paper item | Script | Result file |
|---|---|---|
| Selection (24 candidates), credibility by panel | `scripts/integrity/t1_leakage_safe_selection.py` | `results/selection/` |
| Table 1, fixed origin | `scripts/integrity/p0_ebb_external_corrected.py`, `f57_significance_rescore.py` | `results/fixed_origin/rescored_tab_prob.csv`, `ebb_corrected_rows.csv` |
| Table 1, walk-forward | `f1_wf_ebb_corrected.py`, `w2_wf_monthly_roster.py`, `h4_m5_wf_plan_c.py`, `w1_wf_tweediegp.py`, `w8_wf_aci_ebb.py`, `a1_aci_selector.py`, `w3_assemble_wf_tables.py` | `results/walk_forward/wf_all_panels_wide.csv`, `wf_ebb_corrected.csv` |
| Table 1, layout | `scripts/integrity/v8_tab_main.py` | (LaTeX) |
| DeepAR | `d1_deepar_export.py`, `d2_deepar_runner.py` (GluonTS 0.11.12 / MXNet 1.7 environment), `d3_deepar_score.py`, `d5_deepar_paired_all.py`, `d6_deepar_tex.py` | `results/deepar/` |
| Paired tests | `f57_significance_rescore.py`, `w9_wf_significance.py` | `results/fixed_origin/spl_significance_corrected.csv`, `results/walk_forward/spl_significance_wf.csv` |
| Short-history experiment | `c1_coldstart.py`, `c2_coldstart_report.py` | `results/short_history/coldstart_wide.csv`, `coldstart_all.csv` |
| Prior on / off, by block | `c3_coldstart_nopool.py`, `c5_coldstart_blocks.py`, `c4_coldstart_nopool_tex.py` | `results/short_history/coldstart_nopool_wide.csv`, `coldstart_blocks_wide.csv` |
| Controlled surface | `s1b_separation_surface_v2.py`, `s2_real_separation.py`, `s3_separation_report.py` | `results/surface/` |
| Figure with both panels; appendix figures on the prior gain and on DeepAR | `scripts/integrity/v8_figs.py` | (figures), `results/deepar/deepar_vs_credibility.csv` |
| Pre-fit screen | `r1_room_synthetic.py` ... `r5_room_rows.py`, `room_features.py` | (regenerated; seeds in the scripts) |
| No-pooling candidate | `t2_selection_with_none.py`, `t3_or_none_walkforward.py`, `t4_none_candidate_tex.py` | `results/none_candidate/` |
| Selection weighting check | `t5_selection_weighting_check.py` | `results/none_candidate/selection_weighting_check.csv` |
| Appendix point figures | `h5_point_figs_audited.py` | `results/fixed_origin/point_figs_audited_check.csv` |

Each script writes its output to a run folder under `outputs/`, named in the script; the files
under `results/` are copies of those outputs, grouped by topic.
EBB is deterministic given the data: rerunning a script reproduces its file to numerical precision.
