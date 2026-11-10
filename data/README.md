# GeoSense Data

## Project

**Water Detection and Temporal Change Analysis using NISAR L-band and S-band Data**

GeoSense uses NISAR Level-2 Geocoded Polarimetric Covariance Matrix (L2 GCOV) observations for L-band and S-band radar-based water detection and temporal change analysis.

## Study Area

The hackathon study region is the Ganga alluvial-plain setting in Bihar.

**Reference location:**

- Centre latitude: **25.5000° N**
- Centre longitude: **86.5000° E**

**Reference bounding box:**

- West: **86.3946° E**
- South: **25.4098° N**
- East: **86.6054° E**
- North: **25.5902° N**

This 20 km × 20 km bounding box is provided as a geographic reference for scene discovery and verification. The required analysis is performed over the selected NISAR scene footprint rather than treating this reference box as the processing boundary.

## NISAR Products

The project uses **NISAR L2 GCOV** radar observations.

### L-band

- Product type: **L2 GCOV**
- Frequency: **L-band**
- Relative track: **98**
- Frame: **14**
- Observation dates used:
  - **12 September 2026**
  - **30 October 2026**

### S-band

- Product type: **L2 GCOV**
- Frequency: **S-band**
- Relative track: **98**
- Frame: **14**
- Observation dates used:
  - **12 September 2026**
  - **30 October 2026**

The selected observations are used for L-band-only, S-band-only, combined L+S analysis where compatible, and temporal comparison.

## Data Sources

L-band NISAR data were obtained through the **NASA Alaska Satellite Facility (ASF) DAAC / ASF Vertex NISAR GCOV catalogue**.

S-band NISAR data were obtained through **Bhoonidhi, NRSC/ISRO**.

The official hackathon problem statement specifies L2 GCOV products and identifies relative track 98, frame 14 as an ascending candidate. The actual granules were selected after checking catalogue availability and metadata.

## Processing

The downloaded NISAR products were processed using the NISAR training curriculum, `nisar_utils`, and the GeoSense-specific notebooks in this repository.

The workflow includes:

1. NISAR product inspection
2. Radar backscatter extraction
3. Backscatter conversion/analysis in dB
4. L-band water detection
5. S-band water detection
6. L+S fusion where compatible
7. Spatial refinement of water masks
8. Multi-date temporal comparison
9. Classification of persistent water, newly detected water, water loss/recession, and non-water

A working backscatter threshold of approximately **−17 dB** was selected for the water-detection workflow based on threshold sensitivity testing.

## Raw Data Policy

The original NISAR HDF5 products are **not included in this GitHub repository** because of their large size.

The repository instead contains:

- GeoSense processing notebooks
- Supporting NISAR training notebooks
- `nisar_utils`
- Configuration/supporting code
- Generated analysis outputs
- Documentation describing the data and processing workflow

The raw NISAR products can be downloaded from the official NISAR data sources and processed using the provided workflow.

## Important Note

NISAR acquisition dates, footprints, polarization availability, and channel representations can differ between L-band and S-band observations. Therefore, the L-band and S-band observations are not assumed to be perfectly coincident solely because they share the same track and frame identifiers. The selected granules and their metadata were checked during processing.