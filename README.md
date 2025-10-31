# Elevation-Dependent Glacier Albedo Modeling for Svalbard Glaciers

This repository contains the complete workflow for modeling glacier surface albedo using machine learning and multi-algorithm satellite approaches, as described in **"Elevation-Dependent Glacier Albedo Modeling Using Machine Learning and Multi-Algorithm Satellite Approach in Svalbard"** by Cyran & Ignatiuk (2025).

## 📋 Overview

The code implements elevation-dependent albedo modeling for Hansbreen and Werenskioldbreen glaciers in Svalbard, demonstrating that:
- **Linear regression** excels in ablation zones (R² = 0.65-0.86)
- **Neural networks** perform better in snow-dominated areas (R² = 0.65)
- **Spatial modeling** achieves good agreement with satellite validation (r = 0.62, R² = 0.40)

## 🗂️ Repository Structure
```
├── 01_snowfall_probability.py          # Calculate snowfall probability from AWS + precipitation data
├── 02_Temperature_dominated_linear_regression.py  # Linear model emphasizing thermal processes
├── 03_Snowfall_Dominated_Linear_Regression.py     # Linear model emphasizing precipitation
├── 04_Accumulation_Zone_Linear_Regression.py      # Neural network for snow-dominated surfaces
├── 05_Load_Satellite_Albedo.py         # Load and analyze Landsat albedo rasters
├── 06_Snowfall_Spatial_Albedo_Model.py # Spatial albedo with elevation-dependent probability
├── 07_Temperature_Spatial_Albedo_Model.py  # Alternative spatial model (thermal emphasis)
├── 08_Model_Validation.py              # Validate spatial model vs. satellite
├── 09_Multi_algorithm_satellite_validation.py  # Compare 5 albedo algorithms via GEE
└── README.md
```

## 🚀 Quick Start

### Prerequisites

**Option A: Using Anaconda (Recommended)**

1. Download and install [Anaconda Navigator](https://www.anaconda.com/products/navigator)
2. Open Anaconda Navigator
3. Launch Jupyter Lab
4. Create a new notebook or open the provided `.py` files
5. Install required packages in a cell:
```python
!pip install pandas numpy matplotlib seaborn scikit-learn rasterio earthengine-api geemap
```

**Option B: Using Command Line**
```bash
# Python 3.8+
pip install pandas numpy matplotlib seaborn scikit-learn rasterio earthengine-api geemap
```

### Google Earth Engine Setup (for script 09)

**In Jupyter Lab:**
```python
import ee
ee.Authenticate()  # First time only - opens browser window
ee.Initialize()
```

**In Command Line:**
```bash
earthengine authenticate
```

## 📊 Required Data Files

### ⚠️ Data Not Included in Repository

Due to size limitations, the following data must be obtained separately:

#### 1. AWS (Automatic Weather Station) Data
**Required files:**
- `hans4_2010_daily_ready.csv`
- `hans4_2011_daily_ready.csv`
- `hans9_2010_daily_ready.csv`
- `hans9_2011_daily_ready.csv`
- `werenskiold_2011_daily_ready.csv`
- `werenskiold_2012_daily_ready.csv`

**Required columns:** `date`, `TC` (temperature), `albedo`, `day_of_year`, `precipitation`

**Source:** Available via SIOS (Svalbard Integrated Arctic Earth Observing System) or contact University of Silesia in Katowice data repository (author - Dominik Cyran)

#### 2. Hornsund Precipitation Data
**Required files:**
- `hornsund_2010_precip.csv`
- `hornsund_2011_precip.csv`
- `hornsund_2012_precip.csv`

**Required columns:** `date`, `precipitation` (mm)

**Source:** [Polish Polar Station Hornsund](https://doi.org/10.5194/essd-12-805-2020)

#### 3. Digital Elevation Models (DEMs)
**Required files:**
- `Hansbreen_DEM.tif` (30m resolution)
- `Werenskioldbreen_DEM.tif` (30m resolution)

**Source:** [ArcticDEM](https://www.pgc.umn.edu/data/arcticdem/)

#### 4. Landsat 7 Albedo Rasters
**Required files:**
- `Hans_02_albedo_26_07_2011.tif`
- `Hans_02_albedo_20_08_2011.tif`
- `Werenskiold_02_albedo_26_07_2011.tif`
- `Werenskiold_02_albedo_20_08_2011.tif`

**Source:** Process from Landsat 7 ETM+ or use script 09 for automatic retrieval via Google Earth Engine

#### 5. Glacier Boundary Shapefiles (GeoJSON)
**Required files:**
- `Hansbreen_WGS84.geojson`
- `Werenskioldbreen_WGS84.geojson`
- `Combined_Glaciers_WGS84.geojson`

### 📁 Recommended Directory Structure
```
your_project_folder/
├── processed_data/
│   ├── daily_ready/
│   │   ├── hans4_2010_daily_ready.csv
│   │   ├── hans9_2011_daily_ready.csv
│   │   ├── hornsund_2011_precip.csv
│   │   └── processed_probability/  # Created by script 01
├── DEM/
│   ├── Hansbreen_DEM.tif
│   └── Werenskioldbreen_DEM.tif
├── Landsat_images/
│   └── [date folders with albedo .tif files]
└── Shapefiles_geojson/
    ├── Hansbreen_WGS84.geojson
    └── Werenskioldbreen_WGS84.geojson
```

## 🔄 Workflow

### Running the Scripts

**Method 1: Jupyter Lab (Recommended)**

1. Open Anaconda Navigator
2. Launch Jupyter Lab
3. Navigate to the repository folder
4. Open each `.py` file as a notebook (Jupyter Lab can open .py files directly)
5. Update file paths in the code
6. Run cells sequentially using `Shift + Enter`

**Method 2: Command Line**
```bash
python script_name.py
```

---

### Step 1: Calculate Snowfall Probability

**In Jupyter Lab:**
- Open `01_snowfall_probability.py`
- Update the `base_path` variable to your data location
- Run all cells

**In Command Line:**
```bash
python 01_snowfall_probability.py
```

**Output:** Daily snowfall probability, PDD, daily positive temperature in `processed_data/daily_ready/processed_probability/`

---

### Step 2: Train Point-Based Models

**Option A: Temperature-Dominated (Lower Elevation)**

**In Jupyter Lab:**
- Open `02_Temperature_dominated_linear_regression.py`
- Update `base_path` variable
- Run all cells

**In Command Line:**
```bash
python 02_Temperature_dominated_linear_regression.py
```

**Best for:** AWS_H4 (190m) - ablation zone | **Performance:** R² = 0.86

---

**Option B: Snowfall-Dominated (Lower Elevation)**

**In Jupyter Lab:**
- Open `03_Snowfall_Dominated_Linear_Regression.py`
- Update `base_path` variable
- Run all cells

**In Command Line:**
```bash
python 03_Snowfall_Dominated_Linear_Regression.py
```

**Best for:** AWS_H4, AWS_WRN - with snowfall events | **Performance:** R² = 0.84

---

**Option C: Neural Network (Higher Elevation)**

**In Jupyter Lab:**
- Open `04_Accumulation_Zone_Linear_Regression.py`
- Update `base_path` variable
- Run all cells

**In Command Line:**
```bash
python 04_Accumulation_Zone_Linear_Regression.py
```

**Best for:** AWS_H9 (420m) - snow accumulation zone | **Performance:** R² = 0.65

---

### Step 3: Load Satellite Validation Data

**In Jupyter Lab:**
- Open `05_Load_Satellite_Albedo.py`
- Update file paths in the `files_data` list
- Run all cells

**In Command Line:**
```bash
python 05_Load_Satellite_Albedo.py
```

---

### Step 4: Generate Spatial Albedo Maps

**Option A: Snowfall-Dominated Spatial Model**

**In Jupyter Lab:**
- Open `06_Snowfall_Spatial_Albedo_Model.py`
- Update `dem_paths`, `aws_data_paths` dictionaries
- Run all cells

**In Command Line:**
```bash
python 06_Snowfall_Spatial_Albedo_Model.py
```

---

**Option B: Temperature-Dominated Spatial Model**

**In Jupyter Lab:**
- Open `07_Temperature_Spatial_Albedo_Model.py`
- Update `dem_paths`, `aws_data_paths` dictionaries
- Run all cells

**In Command Line:**
```bash
python 07_Temperature_Spatial_Albedo_Model.py
```

---

### Step 5: Validate Spatial Models

**In Jupyter Lab:**
- Open `08_Model_Validation.py`
- Ensure `model` or `spatial_model` variable exists from previous step (Step 4)
- Update satellite file paths
- Run all cells

**In Command Line:**
```bash
python 08_Model_Validation.py
```

**Results:** r = 0.62, R² = 0.40, RMSE = 0.15 (173,133 pixel comparisons)

---

### Step 6: Multi-Algorithm Satellite Comparison

**In Jupyter Lab:**
- Open `09_Multi_algorithm_satellite_validation.py`
- Authenticate Google Earth Engine (first time only):
```python
  import ee
  ee.Authenticate()
  ee.Initialize()
```
- Update GeoJSON file paths
- Run all cells

**In Command Line:**
```bash
python 09_Multi_algorithm_satellite_validation.py
```

**Algorithms:** Liang (2001), Tasumi (2008), Silva (2016), Simple Vis-NIR, Knap (1999)

---

## 📝 Configuration

**Update file paths in each script before running:**
```python
# Example from script 02:
base_path = r"C:\Your\Path\To\processed_data\daily_ready\processed_probability"

# Example from script 06:
dem_paths = {
    'Hansbreen': r"D:\Your\Path\To\DEM\Hansbreen_DEM.tif",
    'Werenskioldbreen': r"D:\Your\Path\To\DEM\Werenskioldbreen_DEM.tif"
}
```

**💡 Tip for Jupyter Lab Users:**
- You can modify paths directly in the cells
- Use `%pwd` to check current working directory
- Use `%cd` to change directory if needed

## 📈 Expected Outputs

### Script 01 (Snowfall Probability):
- `{station}_{year}_snowfall_probability_clean.csv`
- `{station}_{year}_metadata.txt`
- `{station}_{year}_summary.csv`

### Scripts 02-04 (Model Training):
- Time series plots (measured vs. predicted) - displayed inline in Jupyter Lab
- Feature importance plots
- Performance statistics printed to console/output

### Scripts 06-07 (Spatial Modeling):
- `{glacier}_albedo_prediction_{MODEL}_{date}.png` - saved to working directory

### Script 08 (Validation):
- Scatter plots, difference maps (displayed inline)
- Validation statistics printed to console

### Script 09 (Multi-Algorithm):
- `glacier_albedo_results.csv`
- `glacier_albedo_temporal_changes.csv`
- `glacier_albedo_summary_report.txt`
- `glacier_albedo_analysis.png`

## ⚠️ Important Notes

1. **File Paths:** All scripts contain hardcoded Windows paths. Update these before running.

2. **Jupyter Lab Advantages:**
   - Interactive plotting (plots appear inline)
   - Easy to modify and re-run individual sections
   - Can inspect variables between runs
   - Better for data exploration and debugging

3. **Memory Requirements:** Spatial modeling (scripts 06-08) requires ~8GB RAM for DEM processing.

4. **Processing Time (in Jupyter Lab):** 
   - Scripts 01-05: ~2-5 minutes each
   - Scripts 06-08: ~10-30 minutes each
   - Script 09: ~5-15 minutes

5. **Google Earth Engine:** 
   - Script 09 requires GEE authentication
   - In Jupyter Lab, authentication opens in browser automatically
   - You only need to authenticate once per environment

6. **Sequential Execution:**
   - Script 08 requires that you've run either script 06 or 07 first in the same session
   - The `model` variable from script 06/07 needs to be in memory

## 🐍 Python Environment

**Tested with:**
- Python 3.8 - 3.11
- Anaconda Distribution (2023.x or later)
- Jupyter Lab 3.x or 4.x

**Note:** Some packages (especially `rasterio`) work better in Anaconda environment due to pre-compiled dependencies.

## 📚 Citation

If you use this code, please cite:
```bibtex
@article{cyran2025glacier,
  title={Elevation-Dependent Glacier Albedo Modeling Using Machine Learning and Multi-Algorithm Satellite Approach in Svalbard},
  author={Cyran, Dominik and Ignatiuk, Dariusz},
  journal={[Remote Sensing]},
  year={2025},
  note={Submitted}
}
```

## 📧 Contact

- **Dominik Cyran** - dcyran94@gmail.com
- **Dariusz Ignatiuk** - University of Silesia in Katowice

## 🙏 Acknowledgments

This work was funded by the European Union's Horizon Europe (LIQUIDICE, grant 101184962). Data provided by Polish Polar Station Hornsund and processed within SIOS infrastructure.

## 📄 License

MIT License

---

**Repository:** https://github.com/czawakiach/D_Cyran_Remote_Sensing_Glacier_Albedo
