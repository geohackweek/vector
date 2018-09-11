---
title: "Introduction"
teaching: 2
exercises: 0
objectives:
- Review pre-requisites, Python packages used, and tutorial outline
---

## Tutorial provided by Emilio Mayorga

## Prerequisites
* Ideally some familiarity with GIS (Geographical Information Systems) concepts regarding vector spatial objects (points, lines, polygons, etc).
* Python and conda
  * A conda environment with all required packages for this tutorial (and most of the other tutorials) will be available by default on the GeoHackWeek JupyterHub. See the [Pre-event Material](https://geohackweek.github.io/preliminary/), specially [Getting Started with conda](https://geohackweek.github.io/preliminary/01-conda-tutorial/) and [Introduction to Jupyter and JupyterHub](https://geohackweek.github.io/datasharing/03-Jupyter-tutorial/).
  * [Getting Started with conda](https://geohackweek.github.io/preliminary/01-conda-tutorial/) also provides instructions for installating conda and conda environments on your computer.
  * The core geospatial vector packages used in these tutorial include `geopandas` and its dependencies, which include `shapely`, `fiona`, `pyproj`, `descartes` and `pysal`, in addition to core Python packages `pandas`, `numpy`, `matplotlib`, etc; `pyepsg`, `geojson`, `folium` (interactive maps in Jupyter notebooks), `rasterstats` (simplified raster-vector analysis), and `psycopg2` (access to vector data stored in PostGIS, in this case hosted on the cloud on Amazon Web Services).


## Tutorial summary ("episodes")
1. Introduction
2. Geospatial Concepts
3. Encodings, Formats and Libraries
4. GeoPandas Introduction
5. GeoPandas Advanced Topics
