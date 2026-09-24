# Vortex-tube helicity study — final report
*Generated 2026-09-18 18:19 · dataset SHA-256 `c5b8a4bf16b51c89…` · seed 42 · 2500 rows → 1500/500/500 train/calibration/test*

## 🔑 FINAL FINDING — prediction accuracy ≠ explanation fidelity

The selected **Quadratic Ridge** predicts the three proxy outputs accurately on unseen test rows (R² = Vorticity 0.990, Helicity 0.983, Q-Criterion 0.905), and its 90 % conformal intervals reach 91.0%, 92.6%, 90.0% empirical coverage. **That accuracy does not certify its explanations.**

- **Mach-number sensitivity:** Vorticity: grade *unresolved* (reference SNR 0.4, nMAE 1.06); Helicity: grade *unresolved* (reference SNR 0.5, nMAE 0.70); Q-Criterion: grade *unresolved* (reference SNR 0.4, nMAE 0.85). Mach is nearly collinear with pressure ratio (VIF 35), so its separate effect is poorly identified.
- **Attribution mass on features the data cannot verify:** Vorticity: 10% of SHAP mass sits on inputs whose local slopes are unresolved/poor; Helicity: 11% of SHAP mass sits on inputs whose local slopes are unresolved/poor; Q-Criterion: 20% of SHAP mass sits on inputs whose local slopes are unresolved/poor.
- **Derivative-fidelity score** (1 − nMAE over resolvable inputs): Vorticity 0.98 (2/12 inputs resolvable); Helicity 0.88 (2/12 inputs resolvable); Q-Criterion 0.92 (2/12 inputs resolvable).
- **3-input ablation** (kept: Reynolds Number, Swirl Number, Mach Number): mean derivative error on the retained inputs 0.34 → 0.15 (improved) against the ablated model's own 3-input reference, and 0.34 → 0.33 against the stricter 12-input reference; mean test-R² changed by +0.0028.
- **High-swirl extrapolation** (train Swirl ≤ 1.14): Vorticity: R² 0.99 (quad) vs 0.87 (RF) vs 0.89 (GB); Helicity: R² 0.95 (quad) vs -0.61 (RF) vs -0.45 (GB); Q-Criterion: R² 0.86 (quad) vs 0.54 (RF) vs 0.56 (GB).
- **Polynomial vs tree:** Vorticity: fidelity 0.98 (quad) vs 0.73 (RF) / 0.79 (GB) at test R² 0.990/0.987/0.987; Helicity: fidelity 0.88 (quad) vs 0.68 (RF) / 0.73 (GB) at test R² 0.983/0.978/0.979; Q-Criterion: fidelity 0.92 (quad) vs 0.60 (RF) / 0.67 (GB) at test R² 0.905/0.897/0.893.

**Reading:** only 2 of 12 inputs have slopes that the data can verify; the remaining inputs (including Mach) carry attributions that look plausible but cannot be confirmed. Tree ensembles reach almost the same R² but have clearly worse derivatives, and they also fail to extrapolate to high swirl while the quadratic model does not. High R² should therefore never be read as evidence that the model's explanation is right.

## 1 · Data / provenance audit

- **INFO** · *redundancy* — L/D Ratio ≈ Tube Length / Tube Diameter (max rel. error 0.01%) → the 3 columns are NOT independent inputs.
- **INFO** · *redundancy* — Pressure Ratio ≈ Inlet / Outlet pressure (max rel. error 0.08%) → redundant with two other inputs.
- **WARN** · *collinearity* — corr(Mach Number, Pressure Ratio) = 0.983 → Mach is almost a function of pressure ratio; its separate effect is barely identifiable.
- **WARN** · *definition* — 'Pressure Drop' differs from Inlet−Outlet pressure by 0.288 bar on average (max 0.71) → definition of this column is undocumented.
- **WARN** · *physics* — 'Maximum Temperature' ≥ Hot-exit T in only 1.2% of rows → 'Maximum Temperature' is not a field maximum; clarify its definition.
- **WARN** · *physics* — 'Maximum Mach' ≥ 'Mach Number' in 77.0% of rows → in 23% of rows the 'maximum' is below the reported Mach number; the two columns use different (undocumented) definitions.
- **INFO** · *regime* — 35.5% of rows have Maximum Mach > 1 (supersonic pockets / shocks).
- **INFO** · *targets* — corr(Q-criterion, vorticity²) = 0.926; corr(Vorticity, Helicity) = 0.678 → the three 'primary' outputs are strongly related.
- **INFO** · *provenance* — 89% of the base inputs are statistically uniform (KS p>0.05) and mutually uncorrelated (max |r|=0.20) → looks like a space-filling / random design-of-experiments, NOT measured operating points.
- **WARN** · *provenance* — No case ID, solver, mesh, run-time or uncertainty columns → provenance of Vorticity / Helicity / Q cannot be verified from the file alone. Your instruments (K-type TC, pressure transducer, flow controller) do not measure these quantities → treat them as simulation / proxy outputs.
- **WARN** · *rig-vs-data* — Only 2 rows (0.1%) resemble your physical rig geometry (tube Ø19, L380, 6×2 nozzle). The dataset is a broad parametric envelope → conclusions are about the dataset, not directly about the rig.
- **INFO** · *rig-vs-data* — Rig thermocouples sit at the outlet plane: cold end on the axis, hot end at the periphery → 'exit temperature' columns are location-dependent point values, not mixed-mean values.
- **WARN** · *collinearity* — Highest-VIF inputs: Pressure Ratio (40), Mach Number (35), Inlet Pressure (29), L/D Ratio (16) → attributions/derivatives for these are ambiguous.

## 2 · Baselines (5-fold CV on the training partition)

| model | mean_R2 | mean_nRMSE | rank |
|---|---|---|---|
| Quadratic Ridge | 0.9561 | 0.1872 | 1 |
| Linear Ridge | 0.9499 | 0.2063 | 2 |
| Gradient Boosting | 0.9476 | 0.2065 | 3 |
| Random Forest | 0.948 | 0.2074 | 4 |
| MLP | 0.9486 | 0.2087 | 5 |

CV winner: **Quadratic Ridge**; selected for the explanation audit: **Quadratic Ridge** (exact SHAP and analytic derivatives require a polynomial model).

## 3 · Test-set performance

| target | R2 | RMSE | MAE | nRMSE | Bias |
|---|---|---|---|---|---|
| Vorticity | 0.9898 | 20.32 | 16.22 | 0.1012 | 0.1779 |
| Helicity | 0.9832 | 26.53 | 21.63 | 0.1295 | -0.6525 |
| Q-Criterion | 0.905 | 1310 | 1030 | 0.3083 | 23.14 |

## 4 · Conformal uncertainty

| target | nominal | empirical_coverage | half_width | mean_width | width_over_target_sd |
|---|---|---|---|---|---|
| Vorticity | 0.9 | 0.91 | 34.1 | 68.19 | 0.3397 |
| Helicity | 0.9 | 0.926 | 45.22 | 90.43 | 0.4413 |
| Q-Criterion | 0.9 | 0.9 | 2137 | 4274 | 1.005 |

Coverage by swirl tercile:

| target | swirl_tercile | n | coverage |
|---|---|---|---|
| Vorticity | low | 167 | 0.9102 |
| Vorticity | mid | 166 | 0.8916 |
| Vorticity | high | 167 | 0.9281 |
| Helicity | low | 167 | 0.9341 |
| Helicity | mid | 166 | 0.9157 |
| Helicity | high | 167 | 0.9281 |
| Q-Criterion | low | 167 | 0.8802 |
| Q-Criterion | mid | 166 | 0.9337 |
| Q-Criterion | high | 167 | 0.8862 |

## 5 · Explanation audit

Top-3 inputs by mean |SHAP|: **Vorticity** → Reynolds Number, Swirl Number, Mach Number; **Helicity** → Swirl Number, Reynolds Number, Mach Number; **Q-Criterion** → Reynolds Number, Swirl Number, Mach Number

| target | spearman_shap_vs_perm | top3_overlap |
|---|---|---|
| Vorticity | 0.951 | 3 |
| Helicity | 0.965 | 3 |
| Q-Criterion | 0.8462 | 3 |

Derivative fidelity (model slope vs data-driven local-linear reference):

| target | feature | ref_snr | resolvable | sign_agree | nMAE | grade |
|---|---|---|---|---|---|---|
| Vorticity | Inlet Pressure | 0.3369 | False | 0.646 | 1.092 | unresolved |
| Vorticity | Outlet Pressure | 0.3242 | False | 0.578 | 1.112 | unresolved |
| Vorticity | Cold Mass Fraction | 0.35 | False | 0.588 | 1.115 | unresolved |
| Vorticity | Nozzle Diameter | 0.3071 | False | 0.582 | 1.103 | unresolved |
| Vorticity | Tube Diameter | 0.351 | False | 0.572 | 1.091 | unresolved |
| Vorticity | Tube Length | 0.3354 | False | 0.518 | 1.081 | unresolved |
| Vorticity | L/D Ratio | 0.3183 | False | 0.524 | 1.07 | unresolved |
| Vorticity | Reynolds Number | 31.87 | True | 1 | 0.01505 | good |
| Vorticity | Pressure Ratio | 0.3307 | False | 0.61 | 1.305 | unresolved |
| Vorticity | Mach Number | 0.3783 | False | 0.69 | 1.064 | unresolved |
| Vorticity | Swirl Number | 13.72 | True | 1 | 0.03241 | good |
| Vorticity | Turbulence Intensity | 0.3707 | False | 0.64 | 1.194 | unresolved |
| Helicity | Inlet Pressure | 0.2892 | False | 0.586 | 1.025 | unresolved |
| Helicity | Outlet Pressure | 0.3034 | False | 0.616 | 1.029 | unresolved |
| Helicity | Cold Mass Fraction | 0.3367 | False | 0.622 | 1.032 | unresolved |
| Helicity | Nozzle Diameter | 0.3623 | False | 0.668 | 0.9364 | unresolved |
| Helicity | Tube Diameter | 0.2874 | False | 0.56 | 1.077 | unresolved |
| Helicity | Tube Length | 0.3128 | False | 0.562 | 1.066 | unresolved |
| Helicity | L/D Ratio | 0.2583 | False | 0.572 | 1.082 | unresolved |
| Helicity | Reynolds Number | 7.376 | True | 1 | 0.1577 | good |
| Helicity | Pressure Ratio | 0.2884 | False | 0.564 | 1.049 | unresolved |
| Helicity | Mach Number | 0.4977 | False | 0.806 | 0.6998 | unresolved |
| Helicity | Swirl Number | 21.34 | True | 1 | 0.08222 | good |
| Helicity | Turbulence Intensity | 0.2982 | False | 0.528 | 1.138 | unresolved |
| Q-Criterion | Inlet Pressure | 0.3195 | False | 0.572 | 1.059 | unresolved |
| Q-Criterion | Outlet Pressure | 0.352 | False | 0.556 | 1.088 | unresolved |
| Q-Criterion | Cold Mass Fraction | 0.3741 | False | 0.6 | 1.045 | unresolved |
| Q-Criterion | Nozzle Diameter | 0.3332 | False | 0.63 | 1.053 | unresolved |
| Q-Criterion | Tube Diameter | 0.3314 | False | 0.642 | 0.9419 | unresolved |
| Q-Criterion | Tube Length | 0.2908 | False | 0.556 | 1.036 | unresolved |
| Q-Criterion | L/D Ratio | 0.2988 | False | 0.588 | 0.9756 | unresolved |
| Q-Criterion | Reynolds Number | 8.734 | True | 1 | 0.04841 | good |
| Q-Criterion | Pressure Ratio | 0.3068 | False | 0.57 | 1.052 | unresolved |
| Q-Criterion | Mach Number | 0.4418 | False | 0.75 | 0.8454 | unresolved |
| Q-Criterion | Swirl Number | 6.229 | True | 1 | 0.1103 | good |
| Q-Criterion | Turbulence Intensity | 0.3523 | False | 0.602 | 1.087 | unresolved |

## 6 · 3-input ablation

| target | R2_full | R2_ablated | dR2 | RMSE_full | RMSE_ablated | conf_coverage_ablated | conf_width_ablated | conf_width_full |
|---|---|---|---|---|---|---|---|---|
| Vorticity | 0.9898 | 0.9904 | 0.0005985 | 20.32 | 19.72 | 0.902 | 64.35 | 68.19 |
| Helicity | 0.9832 | 0.9839 | 0.0006416 | 26.53 | 26.02 | 0.92 | 87.96 | 90.43 |
| Q-Criterion | 0.905 | 0.9121 | 0.007135 | 1310 | 1260 | 0.902 | 4260 | 4274 |

| target | feature | full_grade | full_nMAE | abl_grade | abl_nMAE | abl_vs12D_nMAE |
|---|---|---|---|---|---|---|
| Vorticity | Reynolds Number | good | 0.01505 | good | 0.02092 | 0.01637 |
| Vorticity | Swirl Number | good | 0.03241 | good | 0.03959 | 0.03384 |
| Vorticity | Mach Number | unresolved | 1.064 | unresolved | 0.3001 | 0.9257 |
| Helicity | Reynolds Number | good | 0.1577 | good | 0.08679 | 0.1606 |
| Helicity | Swirl Number | good | 0.08222 | good | 0.03711 | 0.08503 |
| Helicity | Mach Number | unresolved | 0.6998 | unresolved | 0.3992 | 0.7103 |
| Q-Criterion | Reynolds Number | good | 0.04841 | good | 0.06736 | 0.04978 |
| Q-Criterion | Swirl Number | good | 0.1103 | good | 0.09132 | 0.1062 |
| Q-Criterion | Mach Number | unresolved | 0.8454 | unresolved | 0.3331 | 0.8447 |

## 7 · High-swirl extrapolation & polynomial-vs-tree

| target | model | R2_in-range | R2_extrapolation | RMSE_ratio_extrap_over_in |
|---|---|---|---|---|
| Helicity | Gradient Boosting | 0.9563 | -0.4456 | 5.037 |
| Helicity | Linear Ridge | 0.948 | 0.7176 | 2.042 |
| Helicity | Quadratic Ridge | 0.9629 | 0.9465 | 1.052 |
| Helicity | Random Forest | 0.9553 | -0.6101 | 5.256 |
| Q-Criterion | Gradient Boosting | 0.8699 | 0.557 | 1.702 |
| Q-Criterion | Linear Ridge | 0.8937 | 0.8589 | 1.063 |
| Q-Criterion | Quadratic Ridge | 0.8868 | 0.8594 | 1.028 |
| Q-Criterion | Random Forest | 0.8765 | 0.5417 | 1.777 |
| Vorticity | Gradient Boosting | 0.9855 | 0.8949 | 2.512 |
| Vorticity | Linear Ridge | 0.9891 | 0.988 | 0.9795 |
| Vorticity | Quadratic Ridge | 0.9884 | 0.9868 | 0.9929 |
| Vorticity | Random Forest | 0.9854 | 0.8669 | 2.812 |

| target | model | test_R2 | fidelity_score | mean_nMAE_resolvable | sign_agree_resolvable | n_good | n_poor | mach_grade | mach_nMAE | R2_extrapolation | RMSE_ratio_extrap_over_in |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Vorticity | Quadratic Ridge | 0.9898 | 0.9763 | 0.02373 | 1 | 2 | 0 | unresolved | 1.064 | 0.9868 | 0.9929 |
| Vorticity | Random Forest | 0.9869 | 0.7337 | 0.2663 | 1 | 2 | 0 | unresolved | 0.9399 | 0.8669 | 2.812 |
| Vorticity | Gradient Boosting | 0.9875 | 0.789 | 0.211 | 1 | 2 | 0 | unresolved | 0.9985 | 0.8949 | 2.512 |
| Helicity | Quadratic Ridge | 0.9832 | 0.88 | 0.12 | 1 | 2 | 0 | unresolved | 0.6998 | 0.9465 | 1.052 |
| Helicity | Random Forest | 0.9777 | 0.6759 | 0.3241 | 0.993 | 1 | 0 | unresolved | 0.9317 | -0.6101 | 5.256 |
| Helicity | Gradient Boosting | 0.9793 | 0.7297 | 0.2703 | 0.995 | 1 | 0 | unresolved | 0.8535 | -0.4456 | 5.037 |
| Q-Criterion | Quadratic Ridge | 0.905 | 0.9207 | 0.07934 | 1 | 2 | 0 | unresolved | 0.8454 | 0.8594 | 1.028 |
| Q-Criterion | Random Forest | 0.897 | 0.6021 | 0.3979 | 0.984 | 1 | 0 | unresolved | 0.9216 | 0.5417 | 1.777 |
| Q-Criterion | Gradient Boosting | 0.8928 | 0.6712 | 0.3288 | 1 | 1 | 0 | unresolved | 0.9218 | 0.557 | 1.702 |

## 8 · Limitations (read before quoting numbers)

- The 'reference derivative' is a data-driven local-linear estimate, **not ground truth**. It can only judge inputs whose effect is resolvable at n≈2000; others are reported as *unresolved*, not *poor*.
- Finite-difference sensitivities hold all other inputs fixed; for strongly collinear/derived inputs (Mach ↔ pressure ratio, L/D ↔ length & diameter) this leaves the region covered by the data (see *off-manifold ratio*).
- Results are for one random partition (seed 42); conformal coverage is marginal, not conditional.
- Vorticity, helicity and Q are proxy/simulation outputs of undocumented origin; the dataset spans a broad parameter envelope and only a handful of rows resemble the physical rig (see audit). Findings describe the **dataset/surrogate**, not the rig itself.