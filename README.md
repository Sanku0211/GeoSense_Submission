# GeoSense

## NISAR-Based Water Detection and Temporal Change Analysis

### 1. Project Overview

**GeoSense** is a geospatial analysis workflow built using **NISAR radar observations**. The project demonstrates how radar backscatter information can be used to identify water-covered regions and analyse how those regions change between different observation dates.

Unlike optical satellite imagery, radar data can provide useful information independent of daylight and is less dependent on cloud-free optical observations. GeoSense uses this property of NISAR data to build a repeatable workflow for water detection and temporal analysis.

The project builds upon the supplied **NISAR Polarimetric Training** workflow and extends the processed NISAR outputs with GeoSense-specific analysis notebooks.

The main idea is:

> **NISAR radar data → backscatter analysis → water detection → L/S-band fusion → temporal comparison → change map and statistics**

---

## 2. What Does GeoSense Do?

GeoSense performs the following main tasks:

### Water Detection

The radar backscatter values are analysed to distinguish likely water pixels from surrounding non-water areas.

A backscatter threshold of approximately **-17 dB** was selected as the working threshold for the final water-detection analysis.

The threshold-based approach produces a binary water mask in which each pixel is classified approximately as:

- **Water**
- **Non-water**

This allows the spatial distribution of detected water to be visualised and quantified.

### L-band and S-band Analysis

NISAR provides observations from different radar bands.

GeoSense analyses the available **L-band and S-band** observations separately before combining their water classifications.

The purpose of the fusion step is to use information from both radar bands rather than relying on only one observation source.

The final combined classification follows a simple rule-based approach:

> A pixel is considered water when it is identified as water by the available L-band or S-band water mask.

### Temporal Change Analysis

Water detection is performed for two observation dates:

- **12 September 2026**
- **30 October 2026**

The two resulting water masks are compared pixel-by-pixel.

This allows GeoSense to identify four main categories:

| Classification | Meaning |
|---|---|
| **Persistent Water** | Water detected on both dates |
| **Water Loss / Recession** | Water detected on the earlier date but not the later date |
| **New Water** | Water detected on the later date but not the earlier date |
| **Non-water** | Not classified as water in either observation |

The temporal result provides both a visual change map and quantitative statistics describing the detected changes.

---

## 3. Processing Workflow

```text
                 NISAR Observations
                        │
                        ▼
              NISAR Processing /
               Training Workflow
                        │
                        ▼
                 Radar Backscatter
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
          L-band                 S-band
             │                     │
             ▼                     ▼
       Water Detection       Water Detection
             │                     │
             └──────────┬──────────┘
                        ▼
                  L + S Fusion
                        │
                        ▼
              Temporal Comparison
             12 Sep → 30 Oct 2026
                        │
                        ▼
             Change Classification
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
   Persistent       Water Loss       New Water
      Water          / Recession
                        │
                        ▼
              Visualisation + Statistics
```

---

## 4. NISAR Training Workflow

GeoSense does not replace the supplied NISAR training workflow.

The original training notebooks and supporting utilities remain in the repository because they provide the processing environment and demonstrate the underlying NISAR data workflow.

The supplied notebooks cover different stages of working with NISAR products, including product selection, metadata inspection, L2 GCOV exploration, spatial processing, quality control, visualisation, GeoTIFF export, and related NISAR analysis.

GeoSense-specific processing is added separately so that the original training material remains intact.

---

## 5. GeoSense-Specific Notebooks

The main GeoSense analysis is contained in:

```text
GeoSense_Water_Analysis/
│
├── GeoSense_Water_Analysis.ipynb
├── GeoSense_Temporal_Change_Analysis.ipynb
└── GeoSense_Final_Analysis-Submission.ipynb
```

### `GeoSense_Water_Analysis.ipynb`

This notebook performs the initial radar-based water analysis.

It includes:

- Loading processed NISAR backscatter data
- Converting and analysing backscatter values
- Testing water-detection thresholds
- Selecting the working threshold
- Generating water masks
- Applying spatial cleanup to the detected regions
- Calculating detected water area
- Visualising the resulting water masks

### `GeoSense_Temporal_Change_Analysis.ipynb`

This notebook focuses on the change between the two observation dates.

It includes:

- Loading the corresponding L-band and S-band outputs
- Preparing the water masks
- Combining L-band and S-band water information
- Comparing the September and October observations
- Classifying persistent water, water loss, and new water
- Calculating pixel counts and percentages
- Generating the temporal change visualisation

### `GeoSense_Final_Analysis-Submission.ipynb`

This is the consolidated, presentation-oriented analysis notebook.

It brings the important GeoSense processing and results together into a cleaner final workflow intended for submission and review.

---

## 6. Generated Outputs

Processed results are stored under the project's `outputs/` directory.

The available outputs include radar visualisations and derived products such as:

- HH backscatter images
- HV backscatter images
- HH/HV ratio products
- RGB visualisations
- L-band outputs
- S-band outputs
- Water masks
- Combined L+S results
- Temporal change visualisations

These outputs provide intermediate and final evidence of the processing performed by the GeoSense workflow.

---

## 7. Temporal Results

The final temporal analysis compares:

**12 September 2026 → 30 October 2026**

The comparison is based on the water masks generated from the available NISAR observations.

The resulting map makes it possible to visually identify areas where water remained persistent, areas where water was no longer detected, and areas where water appeared in the later observation.

The quantitative statistics produced by the notebook report the number and percentage of pixels belonging to each temporal category.

---

## 8. Reproducibility

The repository contains the environment configuration and the notebooks required to understand and reproduce the workflow.

The main environment configuration is provided in:

```text
environment.yml
```

The project uses Python and scientific/geospatial libraries including NumPy, SciPy, pandas, Matplotlib, h5py, xarray, rasterio, GeoPandas, and related tools defined by the supplied environment.

Large NISAR data products should be kept outside the software environment where appropriate and referenced through the configured project paths.

---

## 9. Project Structure

```text
nisar-polarimetric-training/
│
├── notebooks/
│   └── Supplied NISAR training notebooks
│
├── GeoSense_Water_Analysis/
│   ├── GeoSense_Water_Analysis.ipynb
│   ├── GeoSense_Temporal_Change_Analysis.ipynb
│   └── GeoSense_Final_Analysis-Submission.ipynb
│
├── outputs/
│   └── Processed NISAR and visualisation outputs
│
├── nisar_utils/
│   └── NISAR utility functions
│
├── docs/
│   └── NISAR training documentation
│
├── environment.yml
│
├── GeoSense_README.md
└── GeoSense_Technical_Notes.md
```
### NISAR Curriculum and Supporting Code

This project was developed using the provided NISAR polarimetric training curriculum as a reference and supporting framework. The repository includes selected NISAR training notebooks and the associated `nisar_utils` modules required for data processing and visualization.

The GeoSense-specific notebooks implement the project workflow for water detection and temporal change analysis using NISAR L-band and S-band observations. The included NISAR training material is retained separately as supporting/reference material and is not presented as original GeoSense development.

The workflow combines:

**NISAR data → radar backscatter → water masks → L/S-band fusion → temporal comparison → change classification → visualisation and statistics.**

The resulting workflow provides a practical foundation for analysing water extent and its temporal variation using NISAR observations.
---

## 10. Limitations

The current GeoSense implementation focuses on **NISAR-based water detection and temporal change analysis**.

The detected water regions depend on factors such as:

- Radar backscatter characteristics
- The selected detection threshold
- Input product quality
- Acquisition date and observation conditions
- Availability of matching L-band and S-band observations
- Spatial and temporal differences between observations

The temporal classification therefore represents the change detected by the implemented radar-based method and should not automatically be interpreted as a ground-truth measurement of physical water change.

---

## 11. Summary

GeoSense demonstrates how NISAR radar observations can be transformed from raw/processed backscatter information into an interpretable geospatial analysis.
