---
title: "Introduction"
teaching: 5
exercises: 0
---

## Tutorial provided by Emilio Mayorga and Don Setiawan

## Student Prerequesites
* Python **conda**
  * To install conda and learn more about it, see the [GeoHackWeek conda tutorial](https://geohackweek.github.io/Introductory/01-conda-tutorial/)
  * A **conda** environment for the vector tutorial has been set up. It includes `geopandas` and its dependencies, which include (for vector-handling packages) `shapely`, `fiona`, `pyproj`, `descartes` and `pysal`, in addition to `pandas`, `numpy`, etc. These will be handled automatically by the conda environment. Additional conda packages available in the environment include `geojson`, `folium` (interactive maps in Jupyter notebooks), `rasterstats` (simplified raster-vector analysis), and `psycopg2` (access to vector data stored in PostGIS, in this case hosted on the cloud on Amazon Web Services).
* Using JupyterHub; see the [GeoHackWeek JupyterHub tutorial](https://geohackweek.github.io/Introductory/05-Jupyter-tutorial/)
* Ideally some familiarity with GIS concepts regarding vector spatial objects (points, lines, polygons, etc).


### Creating the `vectorenv` conda environment on your computer

`vectorenv` is a Python 3.6 conda environment. But the two notebooks in this tutorial have also been successfully tested with Python 2.7.

Open a terminal window and type the commands to create the environment and "activate" it.

(If you're using miniconda you'll probably need to first add it to your path with a statement like this one on the terminal window: `export PATH=$HOME/miniconda/bin:$PATH`)

{% highlight bash %}
conda env create environment.yml  # Will create an environment called "vectorenv"
source activate vectorenv  # OSX and Linux
activate vectorenv  # Windows
{% endhighlight %}

### Starting Jupyter notebooks

On Windows and MacOSX you may have a conda GUI application already installed, specially if you installed Anaconda. That application should let you select the `vectorenv` environment, then launch Jupyter notebook with that environment.

Otherwise, on the command shell, you can launch Jupyter notebooks (after activating the environment as described above) like this:

{% highlight bash %}
jupyter notebook
{% endhighlight %}


## Overview
1. Introduction
2. Geospatial Concepts
3. Encodings, Formats and Libraries
4. GeoPandas Introduction
5. GeoPandas Advanced Topics
