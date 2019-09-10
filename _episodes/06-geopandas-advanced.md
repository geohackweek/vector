---
title: "GeoPandas Advanced Topics"
teaching: 30
exercises: 0
questions:
- "What additional capabilities does GeoPandas provide, including data access, plotting and analysis?"
- "How does it integrate with other common Python tools?"
- "How do GeoPandas data objects integrate with analyses of raster data over vector geospatial features?"
objectives:
- Explore additional GeoPandas capabilities in reading from PostGIS and using its plot method.
- Learn how to dissolve (aggregate) polygons into larger units, and apply spatial joins across GeoDataFrames, as examples of GeoPandas spatial operators.
- Learn how to explore project (CRS) information and reproject.
- Plot choropleth maps
- Using folium to create interactive maps from a GeoDataFrame
- Explore simple but powerful capabilities offered by the rasterstats package to generate summaries and statistics of raster properties over vector features, and explore these via GeoPandas.
keypoints:
- GeoPandas offers powerful capabilities and interacts with other tools.
- GeoJSON and the common `__geo_interface__` enable convenient and widespread geospatial data object exchange across geospatial packages in Python.
---

## GeoPandas: Advanced topics
[Emilio Mayorga, University of Washington](https://github.com/emiliom/). 2019-9-8

We covered the basics of GeoPandas in the previous episode and notebook. Here, we'll extend that introduction to illustrate additional aspects of GeoPandas and its interactions with other Python libraries, covering fancier mapping, reprojection, analysis (unitary and binary spatial operators), raster zonal stats + GeoPandas.

## Open the Jupyter Notebook

- [Open the Jupyter Notebook in nbviewer](http://nbviewer.jupyter.org/github/geohackweek/tutorial_contents/blob/master/vector/notebooks/geopandas_advanced.ipynb) to follow along the rendered notebook without running it.
- On pangeo, open and run the notebook `tutorial_contents/vector/notebooks/geopandas_advanced.ipynb`.

## Other resources, tools, and overlap with other tutorials
* Advanced vector geospatial analytics
  * Go over all the analytics that have been wrapped into GeoPandas, including [Geometric Manipulations](http://geopandas.org/geometric_manipulations.html), [Set-Operations with Overlay](http://geopandas.org/set_operations.html), [Aggregation with Dissolve](http://geopandas.org/aggregation_with_dissolve.html) and [Merging Data](http://geopandas.org/aggregation_with_dissolve.html)
  * Take a look at [PySAL](http://pysal.org), the Python Spatial Analysis Library. It's in the conda environment. This is a powerful, multi-faceted package.
* Visualizations
  * [GeoPlot](https://residentmario.github.io/geoplot/index.html) makes it easy to generate a variety of useful plots from GeoDataFrames. [Here is a gallery of GeoPlot plots from GeoPandas](http://geopandas.org/gallery/plotting_with_geoplot.html)
  * More spatial visualization options are coming in the [Visualization tutorial](https://geohackweek.github.io/visualization/). Stay tuned, or look it up now. The mapping packages it'll cover include [cartopy](https://scitools.org.uk/cartopy/docs/latest/), [GeoViews](http://geoviews.org/) and [Folium](http://python-visualization.github.io/folium/) (we only covered a small subset of Folium capabilities, just to give you a taste)
* Overlap with raster processing
  * We illustrated `rasterstats` and [rasterio](https://rasterio.readthedocs.io/en/latest/). `rasterio` will be a pretty important component of your raster handling and manipulation toolbox. And it interacts with the GeoJSON-like objects we've examined; for example, see its [features module](https://rasterio.readthedocs.io/en/latest/topics/features.html).
  * [regionmask](https://regionmask.readthedocs.io/) works nicely with GeoDataFrames to support gridded operations, including ones in `xarray` that you'll see in the [nDarrays tutorial](https://geohackweek.github.io/nDarrays/)
