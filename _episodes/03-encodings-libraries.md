---
title: "Encodings, Formats and Libraries"
teaching: 4
exercises: 0
questions:
- "What are common ways to encode vector geospatial data in Python, and how do they relate to broader encoding standards?"
objectives:
- Learn about common community standards (broader than Python) for vector data encoding, and how they're implemented in core Python libraries.
- Learn to transform from one encoding type to another. Learn the `GeoJSON` format and exchange encoding interface, including the `__geo_interface__` method implemented across libraries.
keypoints:
- Python vector packages implement community standards for vector encoding. While these can seem complex, tools exist for conversion into various forms, and many of the tools include common interfaces for seamles exchange of data across tools.
- Packages exist for easily reading data from file-based and other serialized data formats.
- Most of these packages involve a series of steps to handle data, such as stepping through features via a loop, etc. Most tools do one or a couple of things only. `GeoPandas` addresses these challenges by enabling operations on feature collections in one step and bundling multiple tools via a coherent interface that builds on `Pandas`.
---


## Standard Geometry/Data Encodings

Standards exist for text (and binary) encoding of the geometry and feature types we saw. These include OGC/ISO [Well-Known Text (WKT)](https://en.wikipedia.org/wiki/Well-known_text) and Well-Known Binary (WKB), OGC [Geography Markup Language (GML)](https://en.wikipedia.org/wiki/Geography_Markup_Language), and [GeoJSON](http://geojson.org/) (based on JSON, and now an ISO standard). GML is a complex XML-based encoding that we won't discuss much here. We also won't touch on WKB in much detail. In this tutorial we'll focus on **WKT** and specially **GeoJSON.**

### Well-Known Text (WKT)
[WKT](https://en.wikipedia.org/wiki/Well-known_text) text representations of geometric entities are fairly intuitive. Here are some examples of the basic geometric entities we saw earlier (where coordinates are ordered as X Y):
* `POINT(0 0)`
* `LINESTRING(0 0, 1 1, 1 2)`
* `POLYGON((0 0, 4 0, 4 4, 0 4, 0 0), (1 1, 2 1, 2 2, 1 2, 1 1))`
* `MULTIPOINT((0 0), (1 2))`

WKT is specially common in interactions with spatially enabled relational databases, and also as readable representations of internal binary encodings.

### GeoJSON and `__geo_interface__` interface

GeoJSON arose as a community convention leveraging the ubiquity of JSON encoding, specially on the web. Being JSON, it maps easily into Python dictionaries. See [http://geojson.org](http://geojson.org), including examples at [http://geojson.org/geojson-spec.html#examples](http://geojson.org/geojson-spec.html#examples). Its straightforward data structure has made it popular for data exchange in web applications and for passing data between software libraries. But beware that for large data, GeoJSON, being a text-based file format, is a poor choice for geospatial data storage.

The Python `__geo_interface__` method has been widely adopted for passing feature data around between code packages as GeoJSON-like objects (basically, Python dictionaries). We'll explore this method in the next two episodes. To read more about it, see [GeoJSON and the geo interface for Python](https://sgillies.net/2013/06/27/geojson-and-the-geo-interface-for-python.html),  [https://gist.github.com/sgillies/2217756](https://gist.github.com/sgillies/2217756), and [Processing vector features in Python](http://www.perrygeo.com/processing-vector-features-in-python.html). For converting to and from convenient, Pythonic, GeoJSON-like objects, the light-weight [`geojson`](https://github.com/jazzband/geojson) package is a good option.

* Additional GeoJSON resources and tools:
  * [GitHub lets you view GeoJSON files natively](https://help.github.com/articles/mapping-geojson-files-on-github/)). See this [live example, a preview of the OSM building data we'll be covering)](https://github.com/geohackweek/tutorial_contents/blob/master/vector/data/hotosmtask5977_buildings_subset.geojson).
  * GitHub's [http://geojson.io](http://geojson.io) provides interactive creation and viewing of small GeoJSON serializations (as strings or files)
  * [http://dropchop.io](http://dropchop.io). Spatial operations on GeoJSON data, online and interactive. Brought to you by the great people of [CUGOS](https://cugos.org/), an open geospatial community in the broader Puget Sound area.

GeoJSON Example of a point feature collection with two features:

{% highlight json %}
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [102.0, 0.5]
      },
      "properties": {
        "prop0": "value0",
        "prop1": 0
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [-90.0, 10.5]
      },
      "properties": {
        "prop0": "value3",
        "prop1": 3
      }
    }
  ]
}
{% endhighlight %}


## GIS file formats, including Relational Databases (RDBMS)
* Most of the time you will likely interact (read/write) with geospatial data in common (open or otherwise) GIS file formats and geospatial relational database servers.
  * File formats: Shape files (the most ubiquitous), GeoPackage (based on SQLite), KML, GeoJSON, ESRI File Geodatabase (proprietary but fairly accessible), etc
  * Relational Database servers with geospatial extensions: PostgreSQL (PostGIS), MS SQL Server, Oracle, and to a lesser extent MySQL
  * [Here](https://en.wikipedia.org/wiki/GIS_file_formats) and [here](https://gisgeography.com/gis-formats/) are two good lists of common GIS file formats, including some relational databases.
* Core Python packages for accessing geospatial data: [OGR](http://gdal.org/python/) (see also [http://gdal.org/](http://gdal.org/) and [https://pcjericks.github.io/py-gdalogr-cookbook/](https://pcjericks.github.io/py-gdalogr-cookbook/)), [fiona](https://github.com/Toblerity/Fiona), [PyShp](https://github.com/GeospatialPython/pyshp)


## Standard encodings and libraries for projections, codes
* [Projections]((https://en.wikipedia.org/wiki/Map_projection)) and [Coordinate Reference Systems (**CRS**)](https://en.wikipedia.org/wiki/Spatial_reference_system) are fundamental!
* We'll get into this in more detail in the GeoPandas notebooks, and in another tutorial
* Some common libraries and functions: pyepsg, pyproj, `fiona.from_epsg`, `GeoPandas.to_srs`
