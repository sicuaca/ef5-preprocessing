# EF5 Preprocessing

This repository provides notebooks and sample data to prepare inputs for running the [EF5 hydrological model](https://github.com/HyDROSLab/EF5-dockerized).

---

## Requirements

To run EF5, the following datasets and resources are required:

1. **EF5 Dockerized**  
   Repository: [EF5-dockerized](https://github.com/HyDROSLab/EF5-dockerized)

2. **Basic data** (DEM, DDM, FAM)  
   Source: [HydroSHEDS Core Downloads](https://www.hydrosheds.org/hydrosheds-core-downloads)

3. **Model parameters**  
   - CREST parameters  
   - KW parameters  
   - PET data  
   Repository: [EF5-Global-Parameters](https://github.com/HyDROSLab/EF5-Global-Parameters)

4. **Precipitation data**  
   - **SCAMPR data** (available from February 2025): [NOAA Enterprise Rainrate PDS](https://noaa-enterprise-rainrate-pds.s3.amazonaws.com/index.html)  
   - **GSMaP Daily data** (for initialization before February 2025)

5. **NWP data**  
   Used to force the model in forecasting mode.

6. **Configuration file**  
   A sample control file is already included in [EF5-Dockerized](https://github.com/HyDROSLab/EF5-dockerized).

---

## Workflow

### 1. Setup EF5
- Clone and build the EF5 Docker image.  

### 2. Prepare basic data
- Clip DEM, DDM, and FAM data from HydroSHEDS to the area of interest.  
- Convert to `float32`.  
- Ensure all datasets are aligned (same extent, resolution, and grid).  
- Example: see `clip_basic_data.ipynb`.  

### 3. Prepare CREST grid parameters
- Use the gridded CREST data from the [Hydroslab repository](https://github.com/HyDROSLab/EF5-Global-Parameters).  
- Note: `soil_param_b`, `soil_param_fc`, and `soil_param_wm` are shifted northwest by a few pixels. Adjust them in QGIS, then shift back before clipping.  
- Regrid to match the DEM resolution.  
- Example: see *Align CREST Grid Parameters* and *Regridding CREST Parameters* in `clip_basic_data.ipynb`.

### 4. Prepare KW and PET data
- Apply the same clipping and regridding steps as for CREST parameters.  

### 5. Prepare precipitation data
- **SCAMPR (preferred, available from Feb 2025):**  
  Download BLEND GLB5 (NetCDF format), clip, and save to GeoTIFF.  
  See `scampr_preprocessing.ipynb`.  
- **GSMaP Daily (for earlier dates):**  
  Download, preprocess, and convert similarly.  
  See `gsmap_preprocessing.ipynb`.  

### 6. Virtual gauge placement
- EF5 requires gauge locations (real or virtual).  
- If no gauge data is available, create virtual gauges by:  
  - Clipping FAM with administrative boundaries.  
  - Selecting the coordinates of the maximum FAM value in each area.  
- See *Automate Gauge Placement* and *Generate Gauge and Basin* in `clip_basic_data.ipynb`.  

### 7. Configuration file
- EF5 runs are controlled by a configuration file.  
- Full documentation: [EF5 docs](https://chrimerss.github.io/EF5/docs/)  
- Update paths for:  
  - Basic data  
  - Precipitation (SCAMPR or GSMaP)  
  - PET  
  - CREST and KW parameter grids  
- Example control file: `control.txt`.  

### 8. Simulation period
- Set proper dates in the control file for:  
  - Simulation start  
  - Warm-up period  
  - State saving  
  - End of simulation  

### 9. Run EF5
- Once all inputs and configuration are ready, run EF5 inside Docker.  

---

## References
- [EF5 documentation](https://chrimerss.github.io/EF5/docs/)  
- [HydroSHEDS data portal](https://www.hydrosheds.org/hydrosheds-core-downloads)  
- [EF5 Global Parameters](https://github.com/HyDROSLab/EF5-Global-Parameters)  
- [NOAA Enterprise Rainrate PDS](https://noaa-enterprise-rainrate-pds.s3.amazonaws.com/index.html)  
