---
title: "Geospatial encoding"
teaching: 45
exercises: 15
questions:
- "What are the common ways to encode vector geospatial data in Python, and how much is borrowed from broader encoding standards?" 
objectives:
- "Learn abut common community standards (broader than Python) for vector data encoding, and how they're implemented in core Python libraries."
- "Learn to transform from one encoding type to another. Learn the `GeoJSON` format and exchange encoding storage, including the `__geo_interface__` method implemented across libraries."
keypoints
- Python vector packages implement community standards for vector encoding. While these can seem complex, tools exist for conversion into various forms, and many of the tools including common interfaces for exchanging data across tools.
- Tools exist for easily reading data from file-based and other serialized data formats.
---

## Student Prerequesites
- Python conda installed
- conda environment will be distributed with `geopandas` and its dependencies, which include (for vector-handling packages) `shapely`, `fiona`, `pyproj`, `descartes` and `pysal`, in addition to `pandas`, `numpy`, etc. These will be handled automatically by the conda environmet. Additional conda packages include `geojson` (likely installed by other higher-level packages), `folium` (for interactive maps in Jupyter notebooks) and possibly `cartopy` and `mplleaflet`. Finally, `python-rasterstats` for simplified raster-vector analysis, and `psycopg2` for access to vector data stored in PostGIS.
