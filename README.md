# Spatially calibrated uncertainty for satellite-based soil organic carbon (SOC)

HKBK College of Engineering (VTU) mini project, guided by Prof. Afreen Kubra.

## Goal
Estimate SOC (%) from free satellite data and give prediction intervals that stay
reliable in districts the model has never seen.

## Data (1,640 unique locations, SOC in %)
- SIS-India Bihar (CIMMYT): 1,347 locations, 2018-2020. https://hdl.handle.net/11529/10548646
  (no licence stated on the page; permission requested)
- Akole, Maharashtra (Kaushal 2025, CC BY 4.0): 165 samples. https://doi.org/10.17632/36xkf32pph.1
- WoSIS India (ISRIC, Dec 2023): 128 locations. https://doi.org/10.17027/isric-wdcsoils-20231130

Raw data files are not included.

## Features
Sentinel-2 (blue, green, red, nir, swir1, swir2, NDVI, BSI), Sentinel-1 (VV, VH),
SRTM (elevation, slope), CHIRPS rainfall, ERA5-Land temperature. Extracted with Google Earth Engine.
SoilGrids 2.0 is used as a comparison baseline only, not as a model feature.

## Files
- data/soc_master.csv: cleaned points
- data/soc_features.csv: points plus Sentinel-2, Sentinel-1, terrain, rainfall and temperature features
- data/soc_split.csv: features plus a `split` column (bihar_trainval, bihar_test, akole_test, wosis_test)
- data/soc_split_sg.csv: the same plus SoilGrids values (baseline)
- data/final_test_results.csv: final test results (coverage, width, interval score)
- notebooks/: cleaning, satellite extraction, split and modelling code

## Data split
| Set | Points | Use |
|---|---|---|
| bihar_trainval | 1,082 (19 districts) | training, validation, calibration |
| bihar_test | 265 (Madhubani, Banka, Bhojpur, Madhepura) | final test, used once |
| akole_test | 165 | final test, used once |
| wosis_test | 128 | final test, used once |

## Rules
Train, tune and calibrate only on bihar_trainval. Keep the three test groups untouched until the final test.

## Evaluation protocol (frozen before the final test)
- Validation: leave-one-district-out. Random splits are not used, because they leak spatial information.
- Main method: conformalized quantile regression (CQR) with district-blocked calibration
  (25% of districts held out for calibration).
- Baselines: random forest with split conformal, mean-only conformal, and SoilGrids 2.0.
- Target coverage: 90% (alpha = 0.10). Fixed random seed 0.
- Metrics: R2, RMSE, rank correlation, interval coverage, mean interval width, interval score.
- The three test sets are used once, after all choices are fixed. No tuning on them.

## Findings (validation)
- Satellite features give almost no skill in unseen districts (leave-district R2 about -0.05).
- SoilGrids alone also has no skill here (rank correlation -0.04) and reads 2-4x higher than measured values.
- Randomly calibrated intervals undercover. District-blocked calibration restores coverage near 90%.

## Final test results (run once, protocol frozen)
| Test set | n | RF R2 | Coverage (target 0.90) |
|---|---|---|---|
| bihar_test | 265 | -0.03 | RF 0.875, CQR 0.860, mean 0.849 |
| akole_test | 165 | -0.60 | RF 0.818, CQR 0.733, mean 0.721 |
| wosis_test | 128 | -0.06 | RF 0.492, CQR 0.469, mean 0.438 |

All three methods used the same district-blocked calibration set, so this compares base models.
- Coverage is close to target in unseen Bihar districts (0.85 to 0.88).
- Coverage drops under regional shift (Akole 0.72 to 0.82, WoSIS 0.44 to 0.49), so intervals
  calibrated in one region should not be trusted in another without recalibration.
- CQR was not better than RF. The point model has almost no skill, so intervals are wide.

## Planned
Sampling-budget experiment (random vs uncertainty-guided), uncertainty maps, report.
