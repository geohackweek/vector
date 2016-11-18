---
title: "Introduction"
teaching: 5
exercises: 0
---

## Tutorial provided by Emilio Mayorga, with help from Don Setiawan

## Student Prerequesites
- Python conda installed
- Conda environment will be distributed with `geopandas` and its dependencies, which include (for vector-handling packages) `shapely`, `fiona`, `pyproj`, `descartes` and `pysal`, in addition to `pandas`, `numpy`, etc. These will be handled automatically by the conda environment. Additional conda packages include `geojson`, `folium` (for interactive maps in Jupyter notebooks) and possibly `cartopy` and `mplleaflet`. Finally, `python-rasterstats` for simplified raster-vector analysis, and `psycopg2` for access to vector data stored in PostGIS.
- Ideally some familiarity with GIS concepts regarding vector spatial objects (points, lines, polygons, etc)

## Docker Instruction to follow along

Docker image is located on this link: [geohackweek2016/vectortutorial](https://hub.docker.com/r/geohackweek2016/vectortutorial/)

Here are the steps to create a docker container that runs the ipython notebook within the docker container:

**Linux/OS X**

Pull the image
  
~~~
$ docker pull geohackweek2016/vectortutorial
~~~
{: .bash}

Create docker container

~~~
$ docker run -i -t -p 8888:8888 --name vector_tutorial geohackweek2016/vectortutorial
~~~
{: .bash}

Activate `vectorenv` conda environment

~~~
\# source activate vectorenv
~~~
{: .bash}

Run jupyter notebook

~~~
\# jupyter notebook --notebook-dir=/notebooks --ip="*" --port=8888 --no-browser
~~~
{: .bash}

Open web browser on your local host machine and put `localhost:8888` on your address bar to view notebook.

**Windows**

In the docker prompt:

~~~
$ docker-machine ip default
~~~
{: .bash}

*let's call the returned values the "IPaddress"*

Pull the image

~~~
$ docker pull geohackweek2016/vectortutorial
~~~
{: .bash}

Create docker container

~~~
$ docker run -i -t -p 8888:8888 --name vector_tutorial geohackweek2016/vectortutorial
~~~
{: .bash}

Activate `vectorenv` conda environment

~~~
\# source activate vectorenv
~~~
{: .bash}

Run jupyter notebook

~~~
\# jupyter notebook --notebook-dir=/notebooks --ip="*" --port=8888 --no-browser
~~~
{: .bash}

Open web browser on your local host machine and put `IPaddress:8888` on your address bar to view notebook.

## Overview
1. Geospatial Concepts
2. Encodings, Formats and Libraries
3. GeoPandas Introduction
4. GeoPandas Advanced Topics

