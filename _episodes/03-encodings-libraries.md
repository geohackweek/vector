---
title: "Encodings, Formats and Libraries"
teaching: 10
exercises: 0
questions:
- "What are the common ways to encode vector geospatial data in Python, and how much is borrowed from broader encoding standards?"
objectives:
- Learn about common community standards (broader than Python) for vector data encoding, and how they're implemented in core Python libraries.
- Learn to transform from one encoding type to another. Learn the `GeoJSON` format and exchange encoding storage, including the `__geo_interface__` method implemented across libraries.
keypoints:
- Python vector packages implement community standards for vector encoding. While these can seem complex, tools exist for conversion into various forms, and many of the tools including common interfaces for exchanging data across tools.
- Packages exist for easily reading data from file-based and other serialized data formats.
- Most of these packages involve a series of steps to handle data, such as stepping through features via a loop, etc. Most tools do one or a couple of things only. `GeoPandas` solves these problems by enabling operations on feature collections in one step, and bundling multiple tools via a coherent interface that builds on `Pandas`.
---


## Standard Geometry/Data Encodings
* OGC WKT/WKB, OGC GML, GeoJSON, etc


## GeoJSON, `__geo_interface__` method
* [http://geojson.org](http://geojson.org), [http://geojson.org/geojson-spec.html#examples](http://geojson.org/geojson-spec.html#examples)
* GeoJSON and GeoJSON resources/tools online
  * github lets you view GeoJSON files natively
  * [http://geojson.io](http://geojson.io). From github. Lets you interactively create and view small GeoJSON files
  * [http://dropcho.io](http://dropcho.io). Spatial operations on GeoJSON data, online and interactive!
* Python `__geo_interface__` method
  * [GeoJSON and the geo interface for Python](https://sgillies.net/2013/06/27/geojson-and-the-geo-interface-for-python.html). See also [https://gist.github.com/sgillies/2217756](https://gist.github.com/sgillies/2217756)
  * [Processing vector features in Python](http://www.perrygeo.com/processing-vector-features-in-python.html)
* `geojson` package


GeoJSON Example:

~~~
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
~~~
{: .json}


## GIS file formats, RDBMS
* Shape files, GeoPackage, KML, GeoJSON, ESRI File Geodatabase, etc
* PostGIS, MS SQL Server, etc
* OGR, fiona, PyShp


## Standard encodings and libraries for projections, codes
* pyepsg, pyproj, `fiona.from_epsg`, `GeoPandas.to_srs`

