---
title: "xarray and dask"
teaching: 25
exercises: 10
questions:
- "How can we do computations on array datasets that are too large to fit into working memory on a local machine?"
objectives:
- "understand best practices for reading and storing large gridded datasets"
- "using multi-threading libraries to facilitate manipulation of larger-than-memory grids"
keypoints:
- dask integration with xarray allows you to work with large datasets that "fit on disk" rather than having to "fit in memory". 
- It is important to chunk the data correctly for this to work.
---
