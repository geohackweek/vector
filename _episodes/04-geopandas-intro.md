---
title: "GeoPandas Introduction"
teaching: 40
exercises: 0
questions:
- "What is GeoPandas?"
- "What functionality and advantages does GeoPandas offer over other Python geospatial tools?"
- "What geospatial storage, analytical and plotting capabilities does it include?"
- "What is its relationship to Pandas?"
objectives:
- Learn to use GeoPandas by reading from common vector geospatial formats (shape files, GeoJSON, etc), PostGIS databases, and from geospatial data generated on the fly.
- Learn to leverage Pandas functionality in GeoPandas, for effective, mixed attribute-based and geospatial analyses.
- Learn about common geospatial operations, including reprojection, spatial joins, and overlays.
keypoints:
- Leveraging Pandas and core vector geospatial libraries, Python GeoPandas greatly simplifies the use of vector geospatial data and provides powerful capabilities.
---

## GeoPandas: Pandas + geometry data type + custom geo goodness
[Emilio Mayorga, University of Washington](https://github.com/emiliom/). 2019-9-8

## Background

[GeoPandas](http://geopandas.org) adds a spatial geometry data type to `Pandas` and enables spatial operations on these types, using [shapely](http://toblerity.org/shapely/). GeoPandas leverages Pandas together with several core open source geospatial packages and practices to provide a uniquely simple and convenient framework for handling geospatial feature data, operating on both geometries and attributes jointly, and as with Pandas, largely eliminating the need to iterate over features (rows). Also as with Pandas, it adds a very convenient and fine-tuned plotting method, and read/write methods that handle multiple file and "serialization" formats.

> ## Additional Notes
> * Like `shapely`, these spatial data types are limited to discrete entities/features and do not address continuously varying rasters or fields.
> * While GeoPandas spatial objects can be assigned a Coordinate Reference System (`CRS`), operations can not be performed across CRS's. Plus, geodetic ("unprojected", lat-lon) CRS are not handled in a special way; the area of a geodetic polygon will be in degrees.
{: .callout}

GeoPandas builds on mature, stable and widely used packages (Pandas, shapely, etc). It is being supported more and more as a preferred Python data structure for geospatial vector data.

**When should you use GeoPandas?**
* For exploratory data analysis, including in Jupyter notebooks.
* For highly compact and readable code. Which in turn improves reproducibility.
* If you're comfortable with Pandas, R dataframes, or tabular/relational approaches.

**When it may not be the best tool?**
* For polished map creation and multi-layer, interactive visualization; if you're comfortable with GIS software, one option is to use a desktop GIS like QGIS! You can generate intermediate GIS files and plots with GeoPandas, then shift over to QGIS. Or refine the plots in Python with matplotlib or additional packages. GeoPandas can help you manage and pre-process the data, and do initial visualizations.
* If you need very high performance, though I'm not sure about current limitations. Substantial performance enhancements are in the works; see [this 2017 article, Fast GeoSpatial Analysis in Python](https://matthewrocklin.com/blog/work/2017/09/21/accelerating-geopandas-1) discussing the performance challenges currently being addressed with [Dask parallelization](https://github.com/geopandas/geopandas/issues/461) and Cython optimization.


## Open the Jupyter Notebook

[Open the Jupyter Notebook in nbviewer](http://nbviewer.jupyter.org/github/geohackweek/tutorial_contents/blob/master/vector/notebooks/geopandas_intro.ipynb) to follow along without running it.
