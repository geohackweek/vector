---
title: "Introduction to multidimensional arrays"
teaching: 15
exercises: 0
questions:
- "When do we need to use multidimensional arrays?"
- "What are current challenges is manipulating these datasets?" 
objectives:
- explore how most people currently handle these types of datasets
- discuss how current methods are limiting the science that can be accomplished
keypoints:
- gridded data that vary in space and time are common in many geospatial applcations (e.g. climatology)
- in-memory operations are needed to process and visualize these datasets
- high resolution models and observations are producing gridded datasets that are too large for in-memory processes
- new tools are needed that can accommodate the complexity and size of modern multidimensional datasets
---
### Overview:

Geoscientists often need to manipulate datasets structured as arrays. A common example is gridded data consisting of a set of climate variables (e.g. temperature and precipitation) that varies in space and time. Often we need to subset a large global dataset to look at data for a particular region, or select a specific time slice. Then we might want to apply statistical functions to these subsetted groups to generate summary information.

<img src="http://xray.readthedocs.org/en/stable/_images/dataset-diagram.png" width = "800" border = "10">

> ## Isn't this the same as raster processing? 
> The tools in this tutorial have some similarity to raster image processing tools.
> Both require computational engines that can manipulate large stacks of data formatted as arrays. 
> Here we focus on tools that are optimized to handle data that have many variables spanning dimensions
> of time and space. See the raster tutorials for tools that are optimized for image processing of remote sensing datasets.
{: .callout}

### Common data formats:

Prior to about 1990, multidimensional array data were usually stored in binary formats that would be read by Fortran 
or C++ libraries. Users were responsbile for setting up their own file structures and custom codes to handle these files.

In the early 1990s various US funding agencies began exploring portable, self-describing,
 machine independent scientific data formats. Two notable products came out of this effort:
[netcdf](http://www.unidata.ucar.edu/software/netcdf/docs/), optimized
for climate data analysis, and [hdf](https://www.hdfgroup.org/), used for many applications including
distribution of remote sensing datasets. The benefits of these formats is that users could access information about the 
file structure and variable contents from the file itself (assuming the creators of the data took the time to generate
the appropriate metadata and attributes!).

Note that these file formats are structured in ways that enable rapid subsetting and anaylsis using simple command line tools.
For example, the climate community has developed their own [netcdf toolkits](http://www.unidata.ucar.edu/software/netcdf/software.html) 
that accomplish tasks like subsetting and grouping. Similar tools exist for [hdf](https://support.hdfgroup.org/HDF5/Tutor/HDF5Intro.pdf). 

### Common data handling methods:

A common approach for handling multidimensional grids is to read the data into an array and then write a series of nested loops with conditional statements to look for a specific range of index values associated with the temporal or spatial slice needed. Also, clever use of matrix algebra is often used to summarize data across spatial and temporal dimensions.

### Challenges:

Many multidimensional datasets are becoming very large as model resolution and sensing capabilities improve. Traditional methods for looping through array datasets to perform subsetting are no longer viable options for handling these large datasets, because we are limited by what our computers can read into memory. In addition, it is often challenging to keep track of index values when manipulating arrays that span multiple dimensions. 
