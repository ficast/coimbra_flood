# Flood Mapping Project — Context, Current Progress, and Methodological Plan

## Assignment Context

Course: Spatial Data Analysis

Main objective:
Produce a flooded area map for the Mondego River region, between Coimbra and Figueira da Foz, using a combination of:
- satellite image classification;
- temporal Sentinel-2 analysis;
- terrain/topographic analysis;
- citizen-contributed data.

The assignment explicitly requires:

1. Download Sentinel-2 imagery for the Mondego region (tile T29TNE) during the February 2026 flood event.
2. Train image classifiers using benchmark Sentinel-2 datasets.
3. Train:
   - a simple CNN;
   - a transfer learning approach.
4. Apply the trained models to the Mondego region.
5. Discuss:
   - model performance;
   - comparison between methods;
   - temporal analysis;
   - flood evolution;
   - possible improvements.
6. Integrate citizen-contributed data into the flood mapping workflow.
7. Compare classification performance before and after citizen data integration.

Expected deliverables include:
- written report;
- flood rasters;
- methodology description;
- preprocessing description;
- model details;
- evaluation metrics;
- fusion strategy discussion.

---

# Current Project State

## Study Area

Region:
- Mondego River floodplain;
- Coimbra → Figueira da Foz;
- Portugal.

Sentinel-2 tile:
- T29TNE.

---

# Data Sources

## Sentinel-2

Source:
- Copernicus Data Space Browser
- ESA Copernicus Programme

Satellite:
- Sentinel-2

Products used:
- B03 (Green)
- B04 (Red)
- B08 (NIR)
- B11 (SWIR)

Dates currently downloaded:
- 2025-08-21 (reference / dry season)
- 2026-02-22 (flood event)

Coordinate system:
- EPSG:32629
- WGS84 / UTM Zone 29N

---

## DEM / Terrain Data

Source:
- Copernicus DEM

Dataset:
- Copernicus Global DEM (90m)

Operations already performed:
- merge/mosaic of multiple DEM tiles;
- reprojection from EPSG:4326 to EPSG:32629;
- clipping to Sentinel-2 extent.

Derived terrain products already generated:
- hillshade;
- slope raster.

---

# Software and Tools

## GIS

Main GIS platform:
- QGIS

Operations already performed in QGIS:
- raster reprojection;
- raster merge;
- raster clipping;
- raster calculations;
- terrain analysis;
- hillshade generation;
- slope generation;
- vector export;
- extraction of raster values into point layers.

---

## Planned Programming Stack

Primary language:
- Python

Primary libraries planned:
- rasterio
- rioxarray
- xarray
- geopandas
- numpy
- pandas
- matplotlib
- scikit-image
- scikit-learn
- PyTorch
- segmentation_models_pytorch

Potential API usage:
- Copernicus Data Space API
- Sentinel Hub Process API
- STAC APIs

---

# Spectral Indices Already Explored

## NDWI

Formula:

NDWI = (Green - NIR) / (Green + NIR)

Bands:
- B03
- B08

Observation:
The NDWI produced visually better flood/water delineation for the Mondego region than MNDWI.

Possible reasons:
- consistent 10m resolution;
- better sensitivity to flooded vegetation and wet agricultural areas;
- floodplain characteristics are mostly natural/agricultural rather than urban.

---

## MNDWI

Formula:

MNDWI = (Green - SWIR) / (Green + SWIR)

Bands:
- B03
- B11

Observation:
Contrary to some literature expectations, MNDWI visually underperformed NDWI in this specific case.

Possible reasons:
- B11 is 20m resolution;
- loss of spatial detail after resampling;
- flood water mixed with vegetation and sediment.

Important note:
This does NOT invalidate MNDWI.
It becomes an important empirical discussion point for the report.

---

## NDVI

Formula:

NDVI = (NIR - Red) / (NIR + Red)

Bands:
- B08
- B04

Purpose:
- vegetation analysis;
- flooded vegetation interpretation;
- agricultural floodplain analysis.

---

# Terrain Analysis Findings

The DEM analysis revealed:
- the Mondego valley structure;
- floodplain geometry;
- low-relief agricultural areas;
- topographic coherence of potential flooded areas.

Slope interpretation:
- floods are expected primarily in low-slope regions;
- steep terrain detections are likely false positives.

Hillshade purpose:
- visual interpretation of terrain;
- geomorphological validation;
- improved map readability.

---

# Important Methodological Insight

The project is gradually evolving from:

"simple spectral thresholding"

towards:

"terrain-aware and temporally-informed flood mapping"

This is important because:
- flood occurrence is constrained by topography;
- temporal evolution matters;
- spectral information alone is insufficient.

---

# Current Missing Steps

The project has NOT yet generated:
- final temporal flood masks;
- multi-date flood evolution maps;
- machine learning models;
- pseudo-label datasets;
- citizen-data integration.

The project is currently in:
- exploratory GIS analysis;
- terrain analysis;
- spectral analysis;
- methodological planning.

---

# Recommended Next Steps

## STEP 1 — Multi-Temporal Sentinel Pipeline in Python

Goal:
Move temporal analysis from manual QGIS workflow into reproducible code.

Recommended approach:
- use Copernicus API or Sentinel Hub;
- automatically download multiple dates;
- compute indices programmatically.

Suggested dates:
- several days before flood;
- flood peak;
- several days after flood.

Goal:
Analyze flood temporal evolution.

---

## STEP 2 — Automated Spectral Index Generation

For each date:
- NDWI;
- MNDWI;
- NDVI.

Export outputs as:
- GeoTIFF;
- cloud-optimized GeoTIFF if possible.

---

## STEP 3 — Temporal Change Detection

Core idea:

Delta_NDWI = NDWI_current - NDWI_reference

or:

FloodCandidate = Water_current AND NOT Water_reference

Potential analyses:
- flood expansion;
- flood recession;
- permanent water vs temporary flood;
- threshold stability.

---

# STEP 4 — Pseudo-Label Generation

Goal:
Create heuristic flood labels.

Potential rule-based pseudo-label:

FloodCandidate =
- NDWI > threshold
- temporal increase over baseline
- low elevation
- low slope

Example conceptual logic:

Flood =
(NDWI_current > 0.05)
AND
(Delta_NDWI > 0.1)
AND
(Elevation < threshold)
AND
(Slope < threshold)

Important:
These are weak labels / heuristic labels.
Not ground truth.

---

# STEP 5 — Terrain-Constrained Flood Analysis

Goal:
Improve physical plausibility.

Possible filtering:
- remove detections on steep slopes;
- remove detections at implausible elevations.

Potential future improvements:
- HAND model;
- flow accumulation;
- river distance modeling.

---

# STEP 6 — Benchmark Dataset Selection

Potential benchmark datasets:
- Sen1Floods11;
- WorldFloods;
- other Sentinel-2 flood datasets.

Goal:
Train:
- simple CNN;
- transfer learning model.

---

# STEP 7 — CNN Baseline

Potential architecture:
- lightweight segmentation CNN;
- U-Net style model;
- binary segmentation.

Input channels may include:
- B02;
- B03;
- B04;
- B08;
- B11;
- NDWI;
- MNDWI;
- NDVI;
- DEM;
- slope.

---

# STEP 8 — Transfer Learning

Potential approaches:
- pretrained ResNet;
- remote sensing foundation models;
- SatMAE;
- Prithvi;
- segmentation backbones.

---

# STEP 9 — Citizen Data Integration

Planned workflow:
- load citizen points;
- extract DEM/slope values;
- validate plausibility;
- spatially fuse with flood raster.

Potential fusion approaches:
- buffer-based reinforcement;
- probabilistic fusion;
- weighted confidence maps.

---

# STEP 10 — Evaluation

Potential metrics:
- Accuracy;
- Precision;
- Recall;
- F1-score;
- IoU/Jaccard.

Comparison groups:
- spectral thresholding baseline;
- CNN;
- transfer learning;
- terrain-aware filtering;
- citizen-data-enhanced outputs.

---

# Key Current Scientific Observations

1. NDWI appears empirically stronger than MNDWI for this floodplain case.
2. Terrain analysis strongly improves physical interpretation.
3. Temporal analysis is likely more important than static thresholding.
4. Flood detection quality is highly dependent on:
   - spatial resolution;
   - vegetation flooding;
   - sediment/turbidity;
   - topographic context.
5. The workflow is evolving toward physically-informed flood mapping rather than pure spectral segmentation.

---

# Immediate Recommended Priority

Priority order:

1. Build Python pipeline for temporal Sentinel acquisition.
2. Automate NDWI/MNDWI generation.
3. Generate temporal flood masks.
4. Create pseudo-labels.
5. Build terrain-aware masks.
6. Begin ML experimentation.

---

# Overall Project Direction

The current direction combines:
- remote sensing;
- GIS;
- terrain analysis;
- temporal analysis;
- machine learning;
- weak supervision;
- citizen sensing.

The methodology is progressively moving toward a realistic research-oriented flood mapping workflow rather than a purely academic toy example.

