---
title: "OpenStreetMap data access and processing"
teaching: 10
exercises: 0
questions:
- "What is OSM?"
- "What makes OSM data distinctive? How is the data organized internally, and how is that different from usual GIS vector data?"
- "How do we query and read OSM vector data into the familiar GeoPandas GeoDataFrames?"
objectives:
- Learn about how OSM data are organized.
- Learn to query and access OSM vector data, converting it into familiar GeoPandas GeoDataframes.

keypoints:
- OSM is awesome, specially with OSMnx and GeoPandas.
---

## OSM via OSMnx for hotosm
[Emilio Mayorga, University of Washington](https://github.com/emiliom/). 2019-9-10

## Introduction

This material will expand on the [OpenStreetMap (OSM) Missing Maps presentation and exercises](https://geohackweek.github.io/preliminary/04-missing-maps/) from Day 1. The notebook will demonstrate the use of the [OSMnx package](https://osmnx.readthedocs.io) to request and access OSM vector data and convert from the source "graph" data structure to familiar GeoPandas GeoDataFrames. It will highlight the distinct pathways for accessing building footprints vs. other physical features, focusing on water features. The area of interest is the "hotosm" task area used in this GeoHackWeek: [hotosm task 5977, Cyclone Kenneth, Comores: Nzwani Central Buildings 1](https://tasks.hotosm.org/project/5977).

OSM data is primarily vector data, though it relies heavily on images to enable digitizing features. OSM ["represents physical features on the ground (e.g., roads or buildings) using tags attached to its basic data structures (its nodes, ways, and relations)"](https://wiki.openstreetmap.org/wiki/Map_Features). Its data structure diverges from the common geospatial structures we saw earlier. [This article](https://labs.mapbox.com/mapping/osm-data-model/) presents a brief overview of the OSM data model and structure; extensive documentation can be found at the [OSM Elements wiki site](https://wiki.openstreetmap.org/wiki/Elements).

Access to OSM data is available via a couple of APIs. The most widely used one is probably the [Overpass API](http://overpass-api.de/). However, it can get pretty complex and hairy to use the API directly; see the article ["Loading Data from OpenStreetMap with Python and the Overpass API"](https://janakiev.com/blog/openstreetmap-with-python-and-overpass-api/) for a nice introduction. There are several Python packages available for interacting with OSM data via the Overpass API; we'll focus on [OSMnx](https://osmnx.readthedocs.io), a powerful package for accessing OSM data with great integration with GeoPandas.

OSM tags can be messy and hard to navigate. Go to the [OSM Map Features wiki site](https://wiki.openstreetmap.org/wiki/Map_Features) and [OpenStreetMap taginfo](https://taginfo.openstreetmap.org/) to navigate the (overlapping) hierarchies of tags. The OSM [Nominatim API](https://nominatim.org) may be of interest, too: "Nominatim (from the Latin, 'by name') is a tool to search OSM data by name and address and to generate synthetic addresses of OSM points (reverse geocoding).

## Open the Jupyter Notebook

- [Open the Jupyter Notebook in nbviewer](http://nbviewer.jupyter.org/github/geohackweek/tutorial_contents/blob/master/vector/notebooks/OSMnx_hotosm.ipynb) to follow along the rendered notebook without running it.
- On pangeo, open and run the notebook `tutorial_contents/vector/notebooks/OSMnx_hotosm.ipynb`.
