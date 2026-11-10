# GeoSense — Technical Notes

## 1. Project Overview

### 1.1 Project Title

**Water Detection and Temporal Change Analysis using NISAR L-band and S-band Data**

### 1.2 Objective

GeoSense is a radar-based geospatial analysis workflow developed to detect water and identify temporal changes in water-related surface conditions using observations from the NASA-ISRO Synthetic Aperture Radar (NISAR) mission.

The project uses NISAR L-band and S-band radar observations to derive backscatter-based water masks and compare observations acquired on different dates. The workflow is designed to demonstrate how radar measurements can be transformed into interpretable spatial water information and temporal change maps.

The main objectives are:

- Process and visualize NISAR radar observations.
- Analyse L-band and S-band backscatter behaviour.
- Detect potential water-covered areas using radar backscatter characteristics.
- Refine the detected water mask using spatial processing.
- Compare observations from different acquisition dates.
- Classify areas as persistent water, newly detected water, water loss/recession, or non-water.
- Produce interpretable maps and quantitative statistics.
- Document the limitations and uncertainty associated with the radar-based approach.

---

## 2. Data

### 2.1 NISAR Data

The analysis uses NISAR Level-2 Ground-Corrected Orthorectified / Geocoded Complex-to-Ground (GCOV) radar products.

The products were obtained for the study area using NISAR observations available through the relevant data distribution platforms.

The analysis uses both:

- **L-band observations**
- **S-band observations**

The selected observations provide radar backscatter information suitable for surface characterization and water detection.

### 2.2 Spatial Characteristics

The processed GeoSense products used for the analysis have a spatial resolution of approximately **10 m** and use the projected coordinate reference system **EPSG:32645** for the analysed scenes.

The processing workflow uses spatial subsets of the NISAR observations rather than processing the complete large satellite products whenever possible.

### 2.3 Temporal Observations

Two observations were used for the temporal comparison:

- **12 September 2026**
- **30 October 2026**

The temporal analysis compares the available radar-derived information from these dates.

Because acquisition characteristics and available observations can differ between dates, the resulting temporal classification represents changes detected by the implemented radar-based method rather than an independently validated measurement of physical water-volume change.

---

## 3. Processing Workflow

The GeoSense workflow consists of the following stages:

```text
NISAR L2 GCOV Products
          │
          ▼
Product Discovery & Metadata
          │
          ▼
Spatial Subsetting / AOI
          │
          ▼
Quality Control & Data Preparation
          │
          ▼
Backscatter Visualization
          │
          ├──────────────┐
          ▼              ▼
      L-band           S-band
          │              │
          └──────┬───────┘
                 ▼
        Water Detection
                 │
                 ▼
        Spatial Refinement
                 │
                 ▼
       Water Mask Statistics
                 │
                 ▼
      Multi-date Comparison
                 │
                 ▼
      Temporal Change Map
```

The processing was implemented using Python and raster/geospatial processing libraries together with the provided NISAR training utilities where required.

---

## 4. Water Detection

### 4.1 Radar Backscatter

Radar backscatter measures the amount of radar energy returned to the sensor after interacting with the Earth's surface.

Water surfaces can produce relatively low backscatter under suitable observation conditions because smooth water surfaces tend to produce specular reflection away from the radar sensor.

Therefore, low radar backscatter can be used as an indicator for potential water-covered areas.

The GeoSense workflow converts and analyses radar backscatter in decibel (dB) form.

### 4.2 Threshold-Based Detection

A backscatter threshold was evaluated to identify potential water pixels.

Several thresholds were tested:

| Threshold | Pixels detected | Approx. proportion |
|---|---:|---:|
| < −16 dB | 869,500 | 20.39% |
| < −17 dB | 430,139 | 10.09% |
| < −18 dB | 131,591 | 3.09% |
| < −19 dB | 30,357 | — |

The **−17 dB threshold** was selected as a practical working threshold for the GeoSense analysis because it provided a useful separation between low-backscatter candidate water regions and the surrounding non-water areas while avoiding the excessive extent obtained at the less restrictive threshold.

The threshold is a working analytical parameter rather than a universal physical definition of water.

### 4.3 L-band and S-band Information

Both L-band and S-band observations were considered in the workflow.

Where compatible observations were available, the analysis compared their backscatter behaviour and used the available information to support water detection.

The response of radar backscatter depends on several factors, including:

- Surface roughness
- Incidence geometry
- Soil and vegetation conditions
- Water surface conditions
- Sensor frequency
- Polarization
- Acquisition date

Consequently, water classification cannot be interpreted from a single backscatter value without considering the observation conditions.

### 4.4 Spatial Refinement

The initial threshold-based water mask was further processed spatially to reduce isolated pixels and improve the spatial coherence of detected water regions.

The refined mask provides a more interpretable representation of the detected water extent than the raw pixel-level threshold alone.

The refined result produced an estimated water proportion of approximately **15.76%** for the analysed scene.

This value is an estimate produced by the implemented processing workflow and should not be interpreted as independently validated ground-truth water coverage.

---

## 5. Temporal Change Analysis

### 5.1 Objective

Temporal analysis was performed to identify differences between radar-derived water masks from different acquisition dates.

The comparison used observations from:

- **12 September 2026**
- **30 October 2026**

The water masks from the available dates were spatially compared to identify areas that remained classified as water and areas where the classification changed.

### 5.2 Change Classes

The temporal analysis produced four main categories:

- **Persistent Water** — classified as water in both observations.
- **New Water** — classified as water in the later observation but not in the earlier observation.
- **Water Loss / Recession** — classified as water in the earlier observation but not in the later observation.
- **Non-water** — not classified as water in either observation.

The resulting temporal change map provides a spatial representation of the radar-derived classification changes.

### 5.3 Interpretation

The temporal classification identifies where the implemented radar-based water detection method changed between observations.

A detected change can be caused by actual surface-water change, but it can also result from differences in:

- Radar acquisition geometry
- Surface roughness
- Soil moisture
- Vegetation
- Atmospheric/environmental conditions
- Sensor frequency or polarization
- Processing differences
- Threshold sensitivity

Therefore, the temporal classification should **not automatically be interpreted as ground-truth physical water change**.

---

## 6. L-band and S-band Comparison

The project evaluates the usefulness of both NISAR L-band and S-band observations for water detection.

### 6.1 L-band

L-band radar has a longer wavelength and can interact differently with vegetation, soil, and surface structures compared with shorter-wavelength radar observations.

The available L-band data provided the primary basis for the radar backscatter analysis and water detection workflow.

### 6.2 S-band

S-band observations provide a different radar frequency and therefore potentially provide complementary information about surface scattering.

The available S-band observations were incorporated where suitable data were available.

However, the availability of matching L-band and S-band acquisitions is not uniform across the required dates and study area.

### 6.3 Comparison

The analysis demonstrates that radar frequency can influence the observed backscatter response and therefore the resulting water classification.

A direct numerical comparison between L-band and S-band should be interpreted carefully because the observations may not have identical acquisition conditions, spatial coverage, temporal timing, or polarization configuration.

The project therefore treats the L-band/S-band comparison as an observational and methodological assessment rather than claiming that one frequency universally provides superior water detection.

---

## 7. Results

### 7.1 Radar Visualizations

The GeoSense workflow generated radar visualizations including:

- HHHH backscatter
- HVHV backscatter
- HHHH/HVHV ratio in dB
- RGB combinations derived from the radar channels

These visualizations were used to inspect the spatial behaviour of radar backscatter before water classification.

### 7.2 Water Detection

The threshold analysis demonstrated the sensitivity of the detected water extent to the selected backscatter threshold.

The −17 dB threshold was selected as the working threshold for the GeoSense workflow.

The subsequent spatial refinement produced a more coherent water mask and an estimated water proportion of approximately **15.76%** for the analysed scene.

### 7.3 Temporal Results

The temporal analysis generated a change map showing:

- Persistent water
- New water
- Water loss/recession
- Non-water

The map provides a spatial representation of where the radar-based water classification changed between the September and October observations.

The results demonstrate the feasibility of using NISAR radar observations for multi-date water-related change analysis.

---

## 8. Validation and Uncertainty

The GeoSense workflow is primarily an analytical demonstration based on radar backscatter characteristics. Independent ground-truth water labels were not available for a rigorous supervised accuracy assessment of the analysed observations.

Therefore, conventional metrics such as overall accuracy, precision, recall, IoU, or F1-score are **not reported as validated performance metrics**.

### 8.1 Sources of Uncertainty

The main sources of uncertainty include:

- **Threshold sensitivity:** Different backscatter thresholds produce different water extents.
- **Acquisition differences:** Observations from different dates may have different radar acquisition conditions.
- **Surface conditions:** Soil moisture, vegetation, surface roughness, and water-surface conditions can influence backscatter.
- **Frequency differences:** L-band and S-band interact differently with surface and vegetation characteristics.
- **Polarization:** Different polarization channels can respond differently to water and surrounding land cover.
- **Spatial alignment:** Small spatial registration differences can influence pixel-level temporal comparisons.
- **Data availability:** Matching L-band and S-band observations are not necessarily available for every date and location.
- **Lack of independent reference labels:** Without suitable ground-truth or reference water masks, quantitative classification accuracy cannot be established reliably.

The temporal classification therefore represents the change detected by the implemented radar-based method and should not automatically be interpreted as a ground-truth measurement of physical water change.

### 8.2 NISAR Curriculum and Supporting Code

The GeoSense workflow was developed using the provided NISAR polarimetric training curriculum as a reference and supporting framework.

The repository includes the relevant NISAR training notebooks and associated `nisar_utils` modules required by the workflow for NISAR data processing, visualization, and supporting operations.

The **GeoSense-specific notebooks** implement the project-specific workflow for water detection and temporal change analysis using NISAR L-band and S-band observations.

The provided NISAR curriculum material is retained separately as supporting/reference material and is not presented as original GeoSense development.

The distinction between the two components is therefore:

```text
Provided NISAR Curriculum
        │
        ├── Training / reference notebooks
        ├── NISAR processing utilities
        └── Supporting workflow
                │
                ▼
        GeoSense Development
                │
                ├── Water detection
                ├── Backscatter threshold analysis
                ├── Spatial refinement
                ├── L/S comparison
                └── Temporal change analysis
```

This separation allows the repository to remain reproducible while clearly distinguishing the provided NISAR training material from the project-specific GeoSense analysis.

---

## 9. Limitations

The current GeoSense implementation has several limitations.

First, the water-detection threshold is empirical and may not generalize to every NISAR acquisition, geographic region, surface condition, or radar configuration.

Second, the available L-band and S-band observations are not necessarily perfectly matched in time, geometry, polarization, or spatial coverage. Consequently, direct temporal and cross-frequency comparisons must be interpreted carefully.

Third, the absence of suitable independent reference labels prevents a rigorous quantitative accuracy assessment.

Fourth, radar backscatter can be affected by factors other than water presence, including soil moisture, vegetation, surface roughness, incidence geometry, and polarization.

Finally, the temporal change map represents changes in the **radar-derived classification**. Additional ground observations or independent reference datasets would be required to establish the physical validity of each detected change.

---

## 10. Conclusions

GeoSense demonstrates a practical workflow for analysing NISAR radar observations for water detection and temporal change analysis.

The project combines NISAR L-band and S-band observations with backscatter analysis, threshold-based classification, spatial refinement, and multi-date comparison.

A working threshold of **−17 dB** was selected after sensitivity testing, and the resulting water masks were used to generate spatial statistics and temporal change maps.

The analysis demonstrates the potential of NISAR radar observations for monitoring water-related surface changes while also highlighting the importance of acquisition conditions, polarization, radar frequency, threshold selection, and independent validation.

The current implementation should therefore be viewed as a reproducible radar-analysis workflow and demonstration rather than a fully validated operational water-monitoring system.

---

## 11. Summary

GeoSense demonstrates how NISAR radar observations can be transformed from processed backscatter information into an interpretable geospatial analysis.

The workflow covers:

- NISAR L2 GCOV product processing
- L-band and S-band radar analysis
- Backscatter visualization
- Threshold-based water detection
- Spatial water-mask refinement
- Multi-date temporal comparison
- Water-change classification
- Quantitative water-area estimation
- Uncertainty and limitation assessment

The project also maintains the provided NISAR training notebooks and supporting utilities required for reproducibility while clearly separating them from the GeoSense-specific analysis developed for this submission.

Overall, GeoSense provides a reproducible foundation for NISAR-based water detection and temporal change analysis and can be extended in future work through additional observations, improved adaptive classification methods, independent validation datasets, and more comprehensive polarization-aware analysis.
