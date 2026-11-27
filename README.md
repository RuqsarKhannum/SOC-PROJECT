# Spatially calibrated uncertainty for satellite-based soil organic carbon (SOC)

HKBK College of Engineering (VTU) mini project, guided by Prof. Afreen Kubra.

## Data (1,640 unique locations, SOC in %)
- SIS-India Bihar (CIMMYT): 1,347 locations, 2018-2020. https://hdl.handle.net/11529/10548646
  (no licence stated on the page; permission requested)
- Akole, Maharashtra (Kaushal 2025, CC BY 4.0): 165 samples. https://doi.org/10.17632/36xkf32pph.1
- WoSIS India (ISRIC, Dec 2023): 128 locations. https://doi.org/10.17027/isric-wdcsoils-20231130

Raw data files are not included.

## Files
- data/soc_master.csv: cleaned points
- data/soc_features.csv: points plus Sentinel-2, Sentinel-1, terrain, rainfall and temperature features
- data/soc_split.csv: features plus a `split` column (bihar_trainval, bihar_test, akole_test, wosis_test)
- notebooks/: cleaning, satellite extraction and split code

## Rules
Train, tune and calibrate only on bihar_trainval. Keep the three test groups untouched until the final test.
