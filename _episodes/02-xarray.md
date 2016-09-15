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
- "xarray is amazing"
- "try it out today"
---

## Getting this set up

Begin by importing the relevant libraries

~~~
import boto3
import matplotlib.image as mpimg
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import xarray
import xarray.ufuncs as xu 
import seaborn as sn
from datetime import datetime
from dask.diagnostics import ProgressBar
~~~
{: .python}