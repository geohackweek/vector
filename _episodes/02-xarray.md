---
title: "Using xarray"
teaching: 45
exercises: 15
questions:
- "What functionality does xarray offer?"
- "When should I use xarray?"
objectives:
- "selection and subsetting of array datasets using labeled indexing"
- "grouping data and applying statistical functions across multiple dimensions"
- "visualizing 1 and 2 dimensional slices of array data"
- "using multi-threading libraries to facilitate manipulation of larger-than-memory grids"
- "understand best practices for reading and storing large gridded datasets"
keypoints:
- xarray 
---

### What is xarray?

* originally developed by employees (Stephan Hoyer, Alex Kleeman and Eugene Brevdo) at [The Climate Corporation](https://climate.com/)
* xaray extends some of the core functionality of Pandas:
    * operations over _named_ dimensions
    * selection by label instead of integer location
    * powerful groupby functionality
    * database-like joins

### When to use xarray:

* if your data are multidimensional (e.g. climate data: x, y, z, time)
* if your data are structured on a regular grid
* if you can represent your data in NetCDF format

### Why not just numpy?
* joining arrays is awkward in numpy
* labeling is limited to index position
* arrays must be small enough to fit into memory


### Basic xray data structures:
* Dataset: multi-dimensional equivalent of a Pandas DataFrame
    * dict-like container of DataArray objects
<img src="http://xray.readthedocs.org/en/stable/_images/dataset-diagram.png" width = "800" border = "10">
* dimensions (x, y, time); variables (temp, precip); coords (lat, long); attributes

## Using xarray

### sample dataset

Let's use some climate data from the European Centre for Medium-Range Weather Forecasts ([ECMWF](http://www.ecmwf.int/)). We will use their ERA-Intrim climate reanalysis project. You can download the data in netcdf format [here](http://apps.ecmwf.int/datasets/data/interim-full-daily/levtype=sfc/). As is the case for many climate products, the process involves downloading large netcdf files to a local machine

Note: this example follows and expands from xarray developer Stephan Hoyer's [blog post](https://www.continuum.io/content/xray-dask-out-core-labeled-arrays-python).

### begin by importing these libraries

{% highlight python %}
import matplotlib.image as mpimg
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import xarray
import xarray.ufuncs as xu 
from datetime import datetime
from dask.diagnostics import ProgressBar
{% endhighlight %}

### Open the dataset

First we [open](http://xarray.pydata.org/en/stable/generated/xarray.open_dataset.html) the data and load it into a *data array*. (Note: the choice of engine depends on the format of the netcdf file).


~~~
ds = xarray.open_dataset('<rootDir>/ecmwf_airtemp.nc', engine = 'scipy')
~~~
{: .python}

You'll notice this seemed to go very fast. That is because this step does not actually ask Python to read the data into memory. Rather, Python is just scanning the contents of the file. This is called _lazy evaluation_. 

### Inspect the Dataset contents:

You can always remember what xarray has stored in each of your datasets simply by typing the variable name. 


