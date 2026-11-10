# GeoSense – Technical Notes

## 1. Project Overview

GeoSense is a NISAR-based geospatial analysis workflow developed for the InGARSS 2026 YP Hackathon. The objective is to use NISAR L2 GCOV radar observations to detect surface water and identify temporal changes between observations acquired at different dates.

The workflow focuses on radar backscatter because Synthetic Aperture Radar (SAR) observations can provide information about surface conditions independently of daylight and are useful for monitoring areas where optical imagery may be affected by cloud cover or illumination conditions.

GeoSense uses L-band and S-band NISAR observations where suitable data are available. The resulting radar products are processed into water masks and compared across dates to identify persistent water, water loss, newly detected water, and non-water regions.

The implementation emphasizes a transparent, reproducible approach based on threshold-based backscatter classification rather than an opaque machine-learning model.

---

## 2. Data

### 2.1 NISAR Products

The primary SAR data used in the workflow are NISAR Level-2 Geocoded Coordinate of Reference System (GCOV) products.

The analysis uses:

- L-band SAR observations
- S-band SAR observations
- HH polarization products
- HV polarization products where available
- Multi-date observations for temporal comparison

The working temporal analysis compares observations from:

- **12 September 2026**
- **30 October 2026**

The available L-band and S-band observations do not necessarily have identical acquisition conditions or dates. Therefore, the workflow documents the actual available observations rather than assuming that L-band and S-band measurements form perfectly matched acquisitions.

The processed GeoTIFF products used in the analysis have a spatial resolution of approximately **10 m** and use **EPSG:32645 (WGS 84 / UTM Zone 45N)** for the working scene.

### 2.2 Data Sources

NISAR data were obtained from the available NISAR data distribution sources, including the NASA ASF DAAC/Vertex catalogue and the NRSC/ISRO Bhoonidhi NISAR catalogue.

The original NISAR training notebooks supplied for the hackathon were used as references for product handling, visualization and processing.

---

## 3. Processing Workflow

The GeoSense workflow consists of the following major stages:

**NISAR L2 GCOV products**

↓

**Product inspection and metadata verification**

↓

**Backscatter extraction**

↓

**Conversion to decibels**

↓

**Water detection**

↓

**Spatial cleanup of water masks**

↓

**Multi-date comparison**

↓

**Temporal water-change classification**

↓

**Change maps and quantitative statistics**

### 3.1 Backscatter Preparation

Radar backscatter values are converted to decibel scale using:

`dB = 10 × log10(backscatter)`

Working in decibels makes the radar backscatter values easier to interpret and allows a consistent threshold to be applied during water detection.

The workflow also preserves the spatial characteristics of the original geocoded product so that resulting masks and change maps remain spatially referenced.

---

## 4. Water Detection

Water generally produces relatively low radar backscatter compared with many surrounding land surfaces because of the interaction between the radar signal and relatively smooth water surfaces.

GeoSense therefore applies a threshold-based classification to identify candidate water pixels.

A working threshold of approximately **−17 dB** was selected after testing multiple thresholds.

The tested L-band threshold candidates included approximately:

- −16 dB
- −17 dB
- −18 dB
- −19 dB

The −17 dB threshold was selected as a practical working value because it provided a more useful balance between detecting water and limiting excessive false-positive regions in the working scene.

The threshold is treated as an empirical working parameter rather than a universal physical boundary. Its suitability can vary with acquisition geometry, surface conditions, frequency, polarization and scene characteristics.

### 4.1 Spatial Refinement

The initial threshold mask can contain isolated pixels and small fragmented regions caused by radar speckle and heterogeneous surface backscatter.

GeoSense therefore applies spatial processing to improve the usability of the water mask. Small regions are removed and morphological/spatial gap-closing operations are used where appropriate.

The resulting mask is then used for temporal comparison.

---

## 5. Temporal Change Analysis

Temporal analysis is performed by comparing water masks generated for different observation dates.

For each pixel, the water classification from the earlier and later observations is compared.

Four main classes are identified:

| Classification | Meaning |
|---|---|
| Persistent Water | Water detected on both dates |
| Water Loss / Recession | Water detected on the earlier date but not the later date |
| New Water | Water detected on the later date but not the earlier date |
| Non-water | Not classified as water in either observation |

This produces a spatially explicit temporal change map.

The approach allows the workflow to identify where water conditions remained stable and where the detected water extent changed between observations.

The change analysis is intended as **water-surface change detection**. A newly detected water region is not automatically interpreted as flooding, rainfall-induced inundation, or another specific hydrological event without independent supporting evidence.

---

## 6. L-band and S-band Comparison

GeoSense incorporates both L-band and S-band observations where suitable data are available.

The purpose of using both frequencies is to examine how the detected water patterns vary between radar frequencies and to explore the usefulness of combining information from different NISAR observations.

The available data do not provide perfectly matched L-band and S-band acquisitions for every date. Consequently, the L-band/S-band comparison is interpreted with this limitation in mind.

Differences between results may arise from:

- Acquisition date
- Viewing geometry
- Radar frequency
- Polarization
- Surface conditions
- Spatial coverage
- Preprocessing differences

Therefore, the combined result is treated as a complementary analysis rather than proof that frequency fusion is universally superior to a single-frequency approach.

---

## 7. Results

The GeoSense workflow produces several types of outputs:

### 7.1 Radar Visualization

The processed NISAR products can be visualized as radar backscatter images and RGB-style combinations derived from available radar channels.

These visualizations provide spatial context for interpreting the water masks and temporal changes.

### 7.2 Water Masks

The water detection stage produces spatial water masks for the available observations.

The masks provide:

- Detected water pixels
- Spatial distribution of detected water
- Candidate water extent
- Input for subsequent temporal comparison

### 7.3 Temporal Change Maps

The temporal analysis produces a multi-class change map showing:

- Persistent water
- Water loss/recession
- Newly detected water
- Non-water

The resulting maps allow changes to be interpreted spatially rather than only through a single total-area value.

### 7.4 Quantitative Statistics

The workflow also calculates pixel-based statistics for the detected water and temporal classes.

Because the working products have approximately 10 m spatial resolution, each pixel represents approximately 100 m² on the ground, subject to the geocoding and projection of the product.

Area calculations are derived from the number of classified pixels and the product spatial resolution.

---

## 8. Validation and Uncertainty

An independent reference dataset suitable for generating a formal validation label set was not available for the current implementation.

Therefore, GeoSense does **not** report fabricated accuracy, precision, recall, F1-score, IoU or confusion-matrix values.

Instead, validation is currently based on qualitative inspection of:

- Radar backscatter images
- Water-mask spatial patterns
- Threshold sensitivity
- Temporal change maps
- Spatial consistency of detected regions
- Consistency between available radar observations

This is an important limitation of the current implementation.

### 8.1 Sources of Uncertainty

Potential sources of error include:

- Radar speckle
- Mixed land/water pixels
- Vegetation over water
- Rough water surfaces
- Wet soil and other low-backscatter surfaces
- Threshold sensitivity
- Differences in acquisition geometry
- Differences between observation dates
- Differences between L-band and S-band observations
- Spatial registration and coverage differences

The −17 dB threshold should therefore be considered a scene-specific working parameter rather than a universally valid water threshold.

---

## 9. Reproducibility

The project is implemented using Python and Jupyter notebooks.

The processing environment uses scientific and geospatial Python libraries including:

- NumPy
- SciPy
- Pandas
- Matplotlib
- Rasterio
- h5py
- Xarray
- GeoPandas
- Shapely
- JupyterLab

The repository contains the GeoSense analysis notebooks and the supporting NISAR training workflow used to process and interpret the data.

The main GeoSense analysis notebooks are:

- `GeoSense_Water_Analysis.ipynb`
- `GeoSense_Temporal_Change_Analysis.ipynb`
- `GeoSense_Final_Analysis-Submission.ipynb`

The final submission notebook brings the major analysis components together into a reproducible analysis workflow.

---

## 10. Limitations

The current implementation has several limitations.

First, the available L-band and S-band observations are not perfectly matched in acquisition date and conditions. Therefore, differences between frequency-specific results cannot be attributed entirely to radar frequency.

Second, the water classifier currently relies on an empirical backscatter threshold rather than a supervised classification model trained using independently labelled reference data.

Third, a formal independent accuracy assessment could not be completed because a suitable reference dataset was not available for the selected observation period and AOI.

Fourth, the current workflow identifies changes in radar-derived water classification. It does not independently determine the physical cause of a newly detected water region.

These limitations are explicitly retained so that the reported results remain traceable and scientifically defensible.

---

## 11. Conclusions

GeoSense demonstrates a reproducible workflow for extracting water information from NISAR L2 GCOV observations and analysing temporal changes in detected water extent.

The workflow combines radar backscatter analysis, threshold-based water detection, spatial refinement and multi-date comparison to produce interpretable water masks, temporal change maps and quantitative statistics.

The approach provides a practical baseline for NISAR-based water monitoring while remaining transparent about the limitations caused by threshold selection, observation differences and the absence of independent reference labels.

Future improvements could include independently labelled validation data, adaptive or statistically derived thresholds, improved co-registration between observations, and more systematic comparison of L-band, S-band and polarization-specific features.

---

## 12. References

1. NASA/ASF DAAC – NISAR L2 GCOV product documentation and data catalogue.

2. NRSC/ISRO Bhoonidhi – NISAR data catalogue and product information.

3. NISAR Polarimetric Training materials supplied for the InGARSS 2026 YP Hackathon.

4. Rasterio documentation – geospatial raster data processing and GeoTIFF handling.

5. NumPy documentation – numerical array processing.

6. Matplotlib documentation – scientific visualization.

7. GeoPandas and Shapely documentation – geospatial data processing.

All datasets, software libraries, training materials and third-party resources used by the workflow should be appropriately cited in the final submission.