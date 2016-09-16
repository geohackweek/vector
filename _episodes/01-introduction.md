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

> ### Isn't this the same as raster processing? 
> The tools in this tutorial have some similarity to raster image processing
> tools. Both require computational engines that can manipulate large stacks of data formatted as arrays. 
> Here we focus on tools that are optimized to handle data that have many variables spanning dimensions
> of time and space. See the raster tutorials for tools that are optimized for image processing tools 
> and remote sensing datasets.
{: .callout}

### Existing Methods:

A common approach for handling multidimensional grids is to read the data into an array and then write a series of nested loops with conditional statements to look for a specific range of index values associated with the temporal or spatial slice needed. Also, clever use of matrix algebra is often used to summarize data across spatial and temporal dimensions.

Many multidimensional datasets are stored in self-describing formats such as netcdf. Some organizations have developed their own netcdf toolkits that accomplish tasks like subsetting and grouping.

### Challenges:

Many multidimensional datasets are becoming very large as model resolution and sensing capabilities improve. Traditional methods for looping through array datasets to perform subsetting are no longer viable options for handling these large datasets, because we are limited by what our computers can read into memory. In addition, it is often challenging to keep track of index values when manipulating arrays that span multiple dimensions. 
