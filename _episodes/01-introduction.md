---
title: "Introduction"
teaching: 5
exercises: 0
---

## Tutorial provided by Emilio Mayorga, with help from Don Setiawan

## Student Prerequesites
* Python **conda**
  * To install conda and learn more about it, see the [GeoHackWeek conda tutorial](https://geohackweek.github.io/Introductory/00-conda-tutorial/)
  * A **conda** environment for the vector tutorial has been set up. It includes `geopandas` and its dependencies, which include (for vector-handling packages) `shapely`, `fiona`, `pyproj`, `descartes` and `pysal`, in addition to `pandas`, `numpy`, etc. These will be handled automatically by the conda environment. Additional conda packages available in the environment include `geojson`, `folium` (for interactive maps in Jupyter notebooks), `cartopy` and `mplleaflet`,`rasterstats` for simplified raster-vector analysis, and `psycopg2` for access to vector data stored in PostGIS. This environment will be available in the Docker image. It may be created independently with this command (once you understand how to work with conda): `conda create -c conda-forge -n vectorenv python=2.7 geopandas geojson pysal rasterstats seaborn mplleaflet folium cartopy jupyter`
* Ideally some familiarity with GIS concepts regarding vector spatial objects (points, lines, polygons, etc).

## Overview
1. Geospatial Concepts
2. Encodings, Formats and Libraries
3. GeoPandas Introduction
4. GeoPandas Advanced Topics

