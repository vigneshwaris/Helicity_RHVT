# Data / provenance audit
*Dataset fingerprint (SHA-256): `c5b8a4bf16b51c891d0e233fc2f55dd1d5bd4adb72333f7f1e9b1c680e2f7ab0`*

- **WARN** · *collinearity* — corr(Mach Number, Pressure Ratio) = 0.983 → Mach is almost a function of pressure ratio; its separate effect is barely identifiable.
- **WARN** · *definition* — 'Pressure Drop' differs from Inlet−Outlet pressure by 0.288 bar on average (max 0.71) → definition of this column is undocumented.
- **WARN** · *physics* — 'Maximum Mach' ≥ 'Mach Number' in 77.0% of rows → in 23% of rows the 'maximum' is below the reported Mach number; the two columns use different (undocumented) definitions.
- **WARN** · *physics* — 'Maximum Temperature' ≥ Hot-exit T in only 1.2% of rows → 'Maximum Temperature' is not a field maximum; clarify its definition.
- **WARN** · *rig-vs-data* — Only 2 rows (0.1%) resemble your physical rig geometry (tube Ø19, L380, 6×2 nozzle). The dataset is a broad parametric envelope → conclusions are about the dataset, not directly about the rig.
- **WARN** · *provenance* — No case ID, solver, mesh, run-time or uncertainty columns → provenance of Vorticity / Helicity / Q cannot be verified from the file alone. Your instruments (K-type TC, pressure transducer, flow controller) do not measure these quantities → treat them as simulation / proxy outputs.
- **WARN** · *collinearity* — Highest-VIF inputs: Pressure Ratio (40), Mach Number (35), Inlet Pressure (29), L/D Ratio (16) → attributions/derivatives for these are ambiguous.
- **INFO** · *redundancy* — L/D Ratio ≈ Tube Length / Tube Diameter (max rel. error 0.01%) → the 3 columns are NOT independent inputs.
- **INFO** · *provenance* — 89% of the base inputs are statistically uniform (KS p>0.05) and mutually uncorrelated (max |r|=0.20) → looks like a space-filling / random design-of-experiments, NOT measured operating points.
- **INFO** · *regime* — 35.5% of rows have Maximum Mach > 1 (supersonic pockets / shocks).
- **INFO** · *targets* — corr(Q-criterion, vorticity²) = 0.926; corr(Vorticity, Helicity) = 0.678 → the three 'primary' outputs are strongly related.
- **INFO** · *redundancy* — Pressure Ratio ≈ Inlet / Outlet pressure (max rel. error 0.08%) → redundant with two other inputs.
- **INFO** · *rig-vs-data* — Rig thermocouples sit at the outlet plane: cold end on the axis, hot end at the periphery → 'exit temperature' columns are location-dependent point values, not mixed-mean values.
- **OK** · *integrity* — 0 missing/non-numeric cells.
- **OK** · *shape* — 2500 rows × 35 columns as expected (12 inputs + 23 outputs).
- **OK** · *integrity* — Constant columns: none.
- **OK** · *physics* — Hot-exit T > Cold-exit T in 100.0% of rows.
- **OK** · *integrity* — 0 duplicated rows.

## Rig vs dataset
| Quantity | Rig value | Dataset range | Overlap |
|---|---|---|---|
| Tube diameter (mm) | 19 | 8.0 – 20.0 | 31.9% within ±15 % |
| Tube length (mm) | 380 | 100 – 800 | 15.9% within ±15 % |
| L/D | 20 | 5.2 – 95.7 | 11.5% within ±15 % |
| Nozzle: 6×2 mm rectangle → equivalent circular Ø (mm) | 3.91 | 0.80 – 3.00 | rig value lies OUTSIDE the dataset range |
| Nozzle: hydraulic Ø (mm) | 3 | 0.80 – 3.00 | at the upper edge |
| Nozzle / tube area ratio (shape-independent) | 0.0423 | 0.0017 – 0.1339 | 18.7% within ±30 % |
| Rows close to the rig on all 3 geometry criteria | — | — | 2 of 2500 |

## Variance-inflation factors
| feature | R2_vs_others | VIF |
|---|---|---|
| Inlet Pressure | 0.9659 | 29.37 |
| Outlet Pressure | 0.8991 | 9.913 |
| Cold Mass Fraction | 0.006076 | 1.006 |
| Nozzle Diameter | 0.005865 | 1.006 |
| Tube Diameter | 0.7881 | 4.72 |
| Tube Length | 0.9154 | 11.82 |
| L/D Ratio | 0.9358 | 15.58 |
| Reynolds Number | 0.004193 | 1.004 |
| Pressure Ratio | 0.9753 | 40.5 |
| Mach Number | 0.971 | 34.53 |
| Swirl Number | 0.003004 | 1.003 |
| Turbulence Intensity | 0.003512 | 1.004 |

## Target distributions
| target | skew | excess_kurtosis | n_abs_z_gt_4 | min | max |
|---|---|---|---|---|---|
| Vorticity | -0.02611 | -0.8672 | 0 | 655.2 | 1532 |
| Helicity | 0.3346 | -0.7738 | 0 | 84.97 | 1003 |
| Q-Criterion | -0.00291 | -0.4597 | 0 | 2.761e+04 | 4.977e+04 |