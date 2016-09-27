---
title: "Using xarray"
teaching: 45
exercises: 15
questions:
- "What functionality does the xarray library offer?"
- "What are the benefits and limitations of this library?"
objectives:
- "selection and subsetting of array datasets using labeled indexing"
- "grouping data and applying statistical functions across multiple dimensions"
- "visualizing 1 and 2 dimensional slices of array data"
keypoints:
- "xarray simplifies handling of multidimensional arrays through labelled coordinates and adpotion of pandas-like data reduction tools" 
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
<br><br><br>
<img src="http://xray.readthedocs.org/en/stable/_images/dataset-diagram.png" width = "800" border = "10">
<br><br><br>
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

~~~
ds
~~~
{: .python}

## Dataset Properties

Xarray datasets have a number of [properties](http://xarray.pydata.org/en/stable/data-structures.html#dataarray) that you can view at any time:

### Dimensions

Dimension are the names of each of the dimensional axes:

~~~
ds.dims
~~~
{: .python}

### Coordinates
Think of coordinates as the labels for all elements along each dimension. So dimensions are the names of your axes, whereas coordinates are the values of all the ticks along those axes: 

~~~
ds.coords
~~~
{: .python}

### Attributes

Attributes are metadata associated with the dataset. It's very good practice to keep track of the details of your data this way. Here we are just reading back the attribute data already stored in the climate data netcdf file. Note that all of these dataset properties are returned as Python dictionaries.

~~~
ds.attrs
~~~
{: .python}

### Data Arrays

So far we have looked at the xarray data type called a "dataset". An xarray dataset is a collection of all the elements in this graphic:
<br>
<img src="http://xray.readthedocs.org/en/stable/_images/dataset-diagram.png" width = "400">
<br>

We have queried the dataset details about our datset's dimensions, coordinates and attributes. Next we will look at the variable data contained within the dataset. In the graphic above, there are two variables (temperature and precipitation). In xarray, these observations get stored as a "data array", which is similar to a conventional array you would find in numpy or matlab. Let's look again at our dataset and extract some data arrays:


Here we see that the "data variables" include "t2m", which is the air temperature at 2 m height. We can isolate the t2m "data cube" as follows: 


~~~
ds.t2m
~~~
{: .python}

This returns a "data array". Note that the associated coordinates and attributes get carried along for the ride. Also note that we are still not reading any data into memory. 

# Indexing

Indexing is used to select specific elements from xarray files. Let's manipulate the coordinate data array to try some examples, because it is a simple one dimensional array.

We begin by extracting a data array from the dataset coordinates. Let's work with the 'time' coordinate: 

~~~
ds.coords['time']
~~~
{: .python}

Now we will select some elements from the time array. You are probably already used to conventional ways of indexing an array using integers. Here's how that's done in xarray (it's called [positional indexing](http://xarray.pydata.org/en/stable/indexing.html)):

~~~
ds.coords['latitude'].isel(latitude=0)
~~~
{: .python}

~~~
ds.coords['latitude'].isel(latitude=slice(0,10))
~~~
{: .python}

### Data Arrays
* when we index a specific variable, it returns a Data Array:

