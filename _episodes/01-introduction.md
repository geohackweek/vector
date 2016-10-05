---

title: "Introduction"
teaching: 15
exercises: 0

---

### Tutorial provided by Emilio Mayorga, with help from Don Setiawan

### Student Prerequesites
- Python conda installed
- conda environment will be distributed with `geopandas` and its dependencies, which include (for vector-handling packages) `shapely`, `fiona`, `pyproj`, `descartes` and `pysal`, in addition to `pandas`, `numpy`, etc. These will be handled automatically by the conda environmet. Additional conda packages include `geojson` (likely installed by other higher-level packages), `folium` (for interactive maps in Jupyter notebooks) and possibly `cartopy` and `mplleaflet`. Finally, `python-rasterstats` for simplified raster-vector analysis, and `psycopg2` for access to vector data stored in PostGIS.

### Overview
1. Geospatial Encodings
2. GeoPandas Basics
3. Raster-vector analysis with `python-rasterstats`