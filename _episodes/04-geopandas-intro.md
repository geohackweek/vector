---
title: "GeoPandas Introduction"
teaching: 40
exercises: 0
questions:
- "What is GeoPandas?"
- "What functionality and advantages does GeoPandas offer over other Python geospatial tools?"
- "What geospatial storage, analytical and plotting capabilities does it include?"
- "What is its relationship to Pandas?"
objectives:
- Learn to use GeoPandas by reading from common vector geospatial formats (shape files, GeoJSON, etc), PostGIS databases, and from geospatial data generated on the fly.
- Learn to leverage Pandas functionality in GeoPandas, for effective, mixed attribute-based and geospatial analyses.
- Learn about common geospatial operations, including reprojection, spatial joins, and overlays.
keypoints:
- Leveraging Pandas and core vector geospatial libraries, Python GeoPandas greatly simplifies the use of vector geospatial data and provides powerful capabilities.
---

## View episode content in a Jupyter Notebook

[Open the Jupyter Notebook in nbviewer](http://nbviewer.jupyter.org/github/geohackweek/tutorial_contents/blob/master/vector/notebooks/geopandas_intro.ipynb)


## GeoPandas: Pandas + geometry data type + custom geo goodness
[Emilio Mayorga, University of Washington](https://github.com/emiliom/). 2018-9-9

## 1. Background

[GeoPandas](http://geopandas.org) adds a spatial geometry data type to `Pandas` and enables spatial operations on these types, using [shapely](http://toblerity.org/shapely/). GeoPandas leverages Pandas together with several core open source geospatial packages and practices to provide a uniquely simple and convenient framework for handling geospatial feature data, operating on both geometries and attributes jointly, and as with Pandas, largely eliminating the need to iterate over features (rows). Also as with Pandas, it adds a very convenient and fine-tuned plotting method, and read/write methods that handle multiple file and "serialization" formats.

> ## Additional Notes
> * Like `shapely`, these spatial data types are limited to discrete entities/features and do not address continuously varying rasters or fields.
> * While GeoPandas spatial objects can be assigned a Coordinate Reference System (`CRS`), operations can not be performed across CRS's. Plus, geodetic ("unprojected", lat-lon) CRS are not handled in a special way; the area of a geodetic polygon will be in degrees.
{: .callout}

GeoPandas builds on mature, stable and widely used packages (Pandas, shapely, etc). It is being supported more and more as a preferred Python data structure for geospatial vector data.

**When should you use GeoPandas?**
* For exploratory data analysis, including in Jupyter notebooks.
* For highly compact and readable code. Which in turn improves reproducibility.
* If you're comfortable with Pandas, R dataframes, or tabular/relational approaches.

**When it may not be the best tool?**
* For polished map creation and multi-layer, interactive visualization; if you're comfortable with GIS software, one option is to use a desktop GIS like QGIS! You can generate intermediate GIS files and plots with GeoPandas, then shift over to QGIS. Or refine the plots in Python with matplotlib or additional packages. GeoPandas can help you manage and pre-process the data, and do initial visualizations.
* If you need very high performance, though I'm not sure about current limitations. Performance has been increasing and substantial enhancements are in the works (including possibly a [Dask](http://dask.pydata.org) parallelization implementation).

## 2. Set up packages and data file path
We'll use these throughout the rest of the tutorial.


{% highlight python %}
%matplotlib inline

from __future__ import (absolute_import, division, print_function)
import os

import matplotlib as mpl
import matplotlib.pyplot as plt

from shapely.geometry import Point
import pandas as pd
import geopandas as gpd
from geopandas import GeoSeries, GeoDataFrame

data_pth = "../data"
{% endhighlight %}


{% highlight python %}
mpl.__version__, pd.__version__, gpd.__version__
{% endhighlight %}




    ('2.2.3', '0.23.4', '0.4.0')



## 3. GeoSeries: The geometry building block

Like a Pandas `Series`, a `GeoSeries` is the building block for the more broadly useful and powerful `GeoDataFrame` that we'll focus on in this tutorial. Here we'll first take a bit of time to examine a `GeoSeries`.

A `GeoSeries` is made up of an index and a GeoPandas `geometry` data type. This data type is a [shapely.geometry object](http://toblerity.org/shapely/manual.html#geometric-objects), and therefore inherits their attributes and methods such as `area`, `bounds`, `distance`, etc.

GeoPandas has six classes of **geometric objects**, corresponding to the three basic single-entity geometric types and their associated homogeneous collections of multiple entities:
* **Single entity (core, basic types):**
  * Point
  * Line (*formally known as a LineString*)
  * Polygon
* **Homogeneous entity collections:**
  * Multi-Point
  * Multi-Line (*MultiLineString*)
  * Multi-Polygon

A `GeoSeries` is then a list of geometry objects and their associated index values.

> ## Entries (rows) in a GeoSeries can store different geometry types
> GeoPandas does not constrain the geometry column to be of the same geometry type. This can lead to unexpected problems if you're not careful! Specially if you're used to thinking of a GIS file format like shape files, which store a single geometry type. Also beware that certain export operations (say, to shape files ...) will fail if the list of geometry objects is heterogeneous.
{: .callout}

But enough theory! Let's get our hands dirty (so to speak) with code. We'll start by illustrating how GeoSeries are constructured.

### Create a `GeoSeries` from a list of `shapely Point` objects constructed directly from `WKT` text
**Though you will rarely need this raw approach!**


{% highlight python %}
from shapely.wkt import loads

GeoSeries([loads('POINT(1 2)'), loads('POINT(1.5 2.5)'), loads('POINT(2 3)')])
{% endhighlight %}




    0        POINT (1 2)
    1    POINT (1.5 2.5)
    2        POINT (2 3)
    dtype: object



### Create a `GeoSeries` from a list of `shapely Point` objects using the `Point` constructor


{% highlight python %}
gs = GeoSeries([Point(-120, 45), Point(-121.2, 46), Point(-122.9, 47.5)])
gs
{% endhighlight %}




    0        POINT (-120 45)
    1      POINT (-121.2 46)
    2    POINT (-122.9 47.5)
    dtype: object




{% highlight python %}
type(gs), len(gs)
{% endhighlight %}




    (geopandas.geoseries.GeoSeries, 3)



A GeoSeries (and a GeoDataFrame) can store a CRS implicitly associated with the geometry column. This is useful as essential spatial metadata and for transformation (reprojection) to another CRS. Let's assign the CRS.


{% highlight python %}
gs.crs = {'init': 'epsg:4326'}
{% endhighlight %}

The `plot` method accepts standard `matplotlib.pyplot` style options, and can be tweaked like any other `matplotlib` figure.


{% highlight python %}
gs.plot(marker='*', color='red', markersize=100, figsize=(4, 4))
plt.xlim([-123, -119.8])
plt.ylim([44.8, 47.7]);
{% endhighlight %}


![png](../fig/04/geopandas_intro_21_0.png)


**Let's get a bit fancier, as a stepping stone to GeoDataFrames.** First, we'll define a simple dictionary of lists, that we'll use again later.


{% highlight python %}
data = {'name': ['a', 'b', 'c'],
        'lat': [45, 46, 47.5],
        'lon': [-120, -121.2, -122.9]}
{% endhighlight %}

Note this convenient, compact approach to create a list of `Point` shapely objects out of X & Y coordinate lists (an alternate approach is shown in the Advanced notebook):


{% highlight python %}
geometry = [Point(xy) for xy in zip(data['lon'], data['lat'])]
geometry
{% endhighlight %}




    [<shapely.geometry.point.Point at 0x7fb9438989e8>,
     <shapely.geometry.point.Point at 0x7fb9438b46d8>,
     <shapely.geometry.point.Point at 0x7fb9438b4e10>]



We'll wrap up by creating a GeoSeries where we explicitly define the index values.


{% highlight python %}
gs = GeoSeries(geometry, index=data['name'])
gs
{% endhighlight %}




    a        POINT (-120 45)
    b      POINT (-121.2 46)
    c    POINT (-122.9 47.5)
    dtype: object



## 4. GeoDataFrames: The real power tool

> ## Additional Notes
> * It's worth noting that a GeoDataFrame can be described as a *Feature Collection*, where each row is a *Feature*, a *geometry* column is defined (thought the name of the column doesn't have to be "geometry"), and the attribute *Properties* includes the other columns (the Pandas DataFrame part, if you will).
> * More than one column can store geometry objects! We won't explore this capability in this tutorial.
{: .callout}

### Start with a simple, manually constructed illustration

We'll build on the GeoSeries examples. Let's reuse the `data` dictionary we defined earlier, this time to create a DataFrame.


{% highlight python %}
df = pd.DataFrame(data)
df
{% endhighlight %}




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>lat</th>
      <th>lon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>45.0</td>
      <td>-120.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>46.0</td>
      <td>-121.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>c</td>
      <td>47.5</td>
      <td>-122.9</td>
    </tr>
  </tbody>
</table>
</div>



Now we use the DataFrame and the "list-of-shapely-Point-objects" approach to create a GeoDataFrame. Note the use of two DataFrame attribute columns, which are effectively just two simple Pandas Series.


{% highlight python %}
geometry = [Point(xy) for xy in zip(df['lon'], df['lat'])]
gdf = GeoDataFrame(df, geometry=geometry)
{% endhighlight %}

There's nothing new to visualize, but this time we're using the `plot` method from a GeoDataFrame, *not* from a GeoSeries. They're not exactly the same thing under the hood.


{% highlight python %}
gdf.plot(marker='*', color='green', markersize=50, figsize=(3, 3));
{% endhighlight %}


![png](../fig/04/geopandas_intro_36_0.png)


### FINALLY, we get to work with real data! Load and examine the simple "oceans" shape file

`gpd.read_file` is the workhorse for reading GIS files. It leverages the [fiona](http://toblerity.org/fiona/README.html) package.


{% highlight python %}
oceans = gpd.read_file(os.path.join(data_pth, "oceans.shp"))
{% endhighlight %}


{% highlight python %}
oceans.head()
{% endhighlight %}




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>my_polygon</th>
      <th>ID</th>
      <th>Oceans</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>S.Atlantic</td>
      <td>1</td>
      <td>South Atlantic Ocean</td>
      <td>POLYGON ((-67.26025728926088 -59.9309210526315...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>N.Pacific</td>
      <td>0</td>
      <td>North Pacific Ocean</td>
      <td>(POLYGON ((180 66.27034771241199, 180 0, 101.1...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Southern</td>
      <td>3</td>
      <td>Southern Ocean</td>
      <td>POLYGON ((180 -60, 180 -90, -180 -90, -180 -60...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arctic</td>
      <td>2</td>
      <td>Arctic Ocean</td>
      <td>POLYGON ((-100.1196521436255 52.89103112710165...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Indian</td>
      <td>5</td>
      <td>Indian Ocean</td>
      <td>POLYGON ((19.69705552221351 -59.94160091330382...</td>
    </tr>
  </tbody>
</table>
</div>



The `crs` was read from the shape file's `prj` file:


{% highlight python %}
oceans.crs
{% endhighlight %}



{% highlight json %}
    {"init": "epsg:4326"}
{% endhighlight %}


Now we finally plot a real map (or blobs, depending on your aesthetics), from a dataset that's global-scale and stored in "geographic" (latitude & longitude) coordinates. It's *not* the actual ocean shapes defined by coastal boundaries, but bear with me. A colormap has been applied to distinguish the different Oceans.


{% highlight python %}
oceans.plot(cmap='Set2', figsize=(10, 10));
{% endhighlight %}


![png](../fig/04/geopandas_intro_44_0.png)


`oceans.shp` stores both `Polygon` and `Multi-Polygon` geometry types (a `Polygon` may also be viewed as a `Multi-Polygon` with 1 member). We can get at the geometry types and other geometry properties easily.


{% highlight python %}
oceans.geom_type
{% endhighlight %}




    0         Polygon
    1    MultiPolygon
    2         Polygon
    3         Polygon
    4         Polygon
    5    MultiPolygon
    6         Polygon
    dtype: object




{% highlight python %}
# Beware that these area calculations are in degrees, which is fairly useless
oceans.geometry.area
{% endhighlight %}




    0     5287.751094
    1    11805.894558
    2    10822.509589
    3     9578.786157
    4     9047.879388
    5     9640.457926
    6     8616.721287
    dtype: float64




{% highlight python %}
oceans.geometry.bounds
{% endhighlight %}




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>minx</th>
      <th>miny</th>
      <th>maxx</th>
      <th>maxy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-71.183612</td>
      <td>-60.000000</td>
      <td>28.736134</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-180.000000</td>
      <td>0.000000</td>
      <td>180.000000</td>
      <td>67.479386</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-180.000000</td>
      <td>-90.000000</td>
      <td>180.000000</td>
      <td>-59.806846</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-180.000000</td>
      <td>47.660532</td>
      <td>180.000000</td>
      <td>90.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19.697056</td>
      <td>-59.945004</td>
      <td>146.991853</td>
      <td>37.102940</td>
    </tr>
    <tr>
      <th>5</th>
      <td>-180.000000</td>
      <td>-60.000000</td>
      <td>180.000000</td>
      <td>2.473291</td>
    </tr>
    <tr>
      <th>6</th>
      <td>-106.430148</td>
      <td>0.000000</td>
      <td>45.468236</td>
      <td>76.644442</td>
    </tr>
  </tbody>
</table>
</div>



The `envelope` method returns the bounding box for each polygon. This could be used to create a new spatial column or GeoSeries; directly for plotting; etc.


{% highlight python %}
oceans.envelope.plot(cmap='Set2', figsize=(8, 8), alpha=0.7, edgecolor='black');
{% endhighlight %}


![png](../fig/04/geopandas_intro_50_0.png)


Does it seem weird that some envelope bounding boxes, such as the North Pacific Ocean, span all longitudes? That's because they're Multi-Polygons with edges at the ends of the -180 and +180 degree coordinate range.


{% highlight python %}
oceans[oceans['Oceans'] == 'North Pacific Ocean'].plot(figsize=(8, 8));
plt.ylim([-100, 100]);
{% endhighlight %}


![png](../fig/04/geopandas_intro_52_0.png)


### Load "Natural Earth" countries dataset, bundled with GeoPandas
*"[Natural Earth](http://www.naturalearthdata.com) is a public domain map dataset available at 1:10m, 1:50m, and 1:110 million scales. Featuring tightly integrated vector and raster data, with Natural Earth you can make a variety of visually pleasing, well-crafted maps with cartography or GIS software."* A subset comes bundled with GeoPandas and is accessible from the `gpd.datasets` module. We'll use it as a helpful global base layer map.


{% highlight python %}
world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))
world.head(2)
{% endhighlight %}




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>pop_est</th>
      <th>continent</th>
      <th>name</th>
      <th>iso_a3</th>
      <th>gdp_md_est</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>28400000.0</td>
      <td>Asia</td>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>22270.0</td>
      <td>POLYGON ((61.21081709172574 35.65007233330923,...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12799293.0</td>
      <td>Africa</td>
      <td>Angola</td>
      <td>AGO</td>
      <td>110300.0</td>
      <td>(POLYGON ((16.32652835456705 -5.87747039146621...</td>
    </tr>
  </tbody>
</table>
</div>



Its CRS is also EPSG:4326:


{% highlight python %}
world.crs
{% endhighlight %}



{% highlight json %}
    {"init": "epsg:4326"}
{% endhighlight %}



{% highlight python %}
world.plot(figsize=(8, 8));
{% endhighlight %}


![png](../fig/04/geopandas_intro_57_0.png)


### Map plot overlays: Plotting multiple spatial layers

Here's a compact, quick way of using the GeoDataFrame plot method to overlay two GeoDataFrames while customizing the styles for each layer.


{% highlight python %}
world.plot(ax=oceans.plot(cmap='Set2', figsize=(10, 10)), facecolor='gray');
{% endhighlight %}


![png](../fig/04/geopandas_intro_60_0.png)


We can also compose the plot using conventional `matplotlib` steps and options that give us more control.


{% highlight python %}
f, ax = plt.subplots(1, figsize=(12, 6))
ax.set_title('Countries and Ocean Basins')
# Other nice categorical color maps (cmap) include 'Set2' and 'Set3'
oceans.plot(ax=ax, cmap='Paired')
world.plot(ax=ax, facecolor='lightgray', edgecolor='gray')
ax.set_ylim([-90, 90])
ax.set_axis_off()
plt.axis('equal');
{% endhighlight %}


![png](../fig/04/geopandas_intro_62_0.png)


> ## Time to explore
> Let's stop for a bit to explore on your own, hack with your neighbors, ask questions.
{: .callout}

## 5. Extras: Reading from other data source types; fancier plotting
* Read from remote PostgreSQL/PostGIS database.
* Read from a remote OGC WFS service.

### Read PostgreSQL/PostGIS dataset from the Amazon Cloud
The fact that it's on an Amazon Cloud is irrelevant. The approach is independent of the location of the database server; it could be on your computer.


{% highlight python %}
import json
import psycopg2
{% endhighlight %}

First we'll read the database connection information from a hidden JSON file, to add a level of security and not expose all that information on the github GeoHackWeek repository. This is also a good practice for handling sensitive information.


{% highlight python %}
with open(os.path.join(data_pth, "db.json")) as f:
    db_conn_dict = json.load(f)
{% endhighlight %}

Open the database connection, returning a connection object:


{% highlight python %}
conn = psycopg2.connect(**db_conn_dict)
{% endhighlight %}

Now that we've used the connection information, we'll overwrite the `user` and `password` keys (for security) and print out the dictionary, to give you a look at what needs to be in it:


{% highlight python %}
db_conn_dict['user'] = '*****'
db_conn_dict['password'] = '*****'
db_conn_dict
{% endhighlight %}



{% highlight json %}
    {"host": "dssg2017.csya4zsfb6y4.us-east-1.rds.amazonaws.com",
     "port": 5432,
     "database": "geohack",
     "user": "*****",
     "password": "*****"}
{% endhighlight %}


Finally, the magic: Read in the `world_seas` PostGIS dataset (a spatially enabled table in the PostgreSQL database) into a GeoDataFrame, using the opened connection object. Note the use of a simple SQL query string: `select * from world_seas`


{% highlight python %}
seas = gpd.read_postgis("select * from world_seas", conn, 
                        coerce_float=False)
{% endhighlight %}


{% highlight python %}
seas.crs
{% endhighlight %}




{% highlight json %}
    {"init": "epsg:4326"}
{% endhighlight %}



Close the connection. Clean up after yourself.


{% highlight python %}
conn.close()
{% endhighlight %}

Let's take a look at the GeoDataFrame.


{% highlight python %}
seas.head()
{% endhighlight %}




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>gid</th>
      <th>name</th>
      <th>id</th>
      <th>gazetteer</th>
      <th>is_generic</th>
      <th>oceans</th>
      <th>geom</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Inner Seas off the West Coast of Scotland</td>
      <td>18</td>
      <td>4283</td>
      <td>False</td>
      <td>North Atlantic Ocean</td>
      <td>(POLYGON ((-6.496945454545455 58.0874909090909...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Mediterranean Sea - Western Basin</td>
      <td>28A</td>
      <td>4279</td>
      <td>False</td>
      <td>North Atlantic Ocean</td>
      <td>(POLYGON ((12.4308 37.80325454545454, 12.41498...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Mediterranean Sea - Eastern Basin</td>
      <td>28B</td>
      <td>4280</td>
      <td>False</td>
      <td>North Atlantic Ocean</td>
      <td>(POLYGON ((23.60853636363636 35.60874545454546...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Sea of Marmara</td>
      <td>29</td>
      <td>3369</td>
      <td>False</td>
      <td>North Atlantic Ocean</td>
      <td>(POLYGON ((26.21790909090909 40.05290909090909...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Black Sea</td>
      <td>30</td>
      <td>3319</td>
      <td>False</td>
      <td>North Atlantic Ocean</td>
      <td>(POLYGON ((29.04846363636364 41.25555454545454...</td>
    </tr>
  </tbody>
</table>
</div>



### More advanced plotting and data filtering

Color the layer based on one column that aggregates individual polygons; using a categorical map, as before, but explicitly selecting the column (`column='oceans'`) and categorical mapping (`categorical=True`); displaying an auto-generated legend, while displaying all polygon boundaries. Each "oceans" entry (ocean basins, actually) contain one or more 'seas'.


{% highlight python %}
seas.plot(column='oceans', categorical=True, legend=True, figsize=(14, 6));
{% endhighlight %}


![png](../fig/04/geopandas_intro_82_0.png)


> ## Additional plotting examples
> See [http://darribas.org/gds17/labs/Lab_02.html](http://darribas.org/gds17/labs/Lab_02.html) for great examples of lots of other cool GeoPandas map plotting tips.
{: .callout}


Combine what we've learned. A map overlay, using `world` as a background layer, and filtering `seas` based on an attribute value (from `oceans` column) and an auto-derived GeoPandas geometry attribute (`area`). **`world` is in gray, while the filtered `seas` is in color.**


{% highlight python %}
seas_na_arealt1000 = seas[(seas['oceans'] == 'North Atlantic Ocean') 
                          & (seas.geometry.area < 1000)]
{% endhighlight %}


{% highlight python %}
seas_na_arealt1000.plot(ax=world.plot(facecolor='lightgray', figsize=(8, 8)), 
                        cmap='Paired', edgecolor='black')

# Use the bounds geometry attribute to set a nice
# geographical extent for the plot, based on the filtered GeoDataFrame
bounds = seas_na_arealt1000.geometry.bounds

plt.xlim([bounds.minx.min()-5, bounds.maxx.max()+5])
plt.ylim([bounds.miny.min()-5, bounds.maxy.max()+5]);
{% endhighlight %}


![png](../fig/04/geopandas_intro_86_0.png)


### Save the filtered seas GeoDataFrame to a shape file
The `to_file` method uses the [fiona](http://toblerity.org/fiona/README.html) package to write to a GIS file. The default `driver` for output file format is 'ESRI Shapefile', but many others are available because `fiona` leverages [GDAL/OGR](http://www.gdal.org).


{% highlight python %}
seas_na_arealt1000.to_file(os.path.join(data_pth, "seas_na_arealt1000.shp"))
{% endhighlight %}

### Read from OGC WFS GeoJSON response into a GeoDataFrame
Use an [Open Geospatial Consortium](http://www.opengeospatial.org) (OGC) [Web Feature Service](https://en.wikipedia.org/wiki/Web_Feature_Service) (WFS) request to obtain geospatial data from a remote source. OGC WFS is an open geospatial standard.

We won't go into all details about what's going on. Suffice it to say that we issue an OGC WFS request for all features from the layer named "oa:goainv" found in a [GeoServer](http://geoserver.org) instance from [NANOOS](http://nanoos.org), requesting the response in `GeoJSON` format. Then we use the [geojson](https://github.com/frewsxcv/python-geojson) package to "load" the raw response (a GeoJSON string) into a `geojson` feature object (a dictionary-like object).

The "oa:goainv" layer is a global dataset of monitoring sites and cruises where data relevant to ocean acidification are collected. It's a work in progress from the [Global Ocean Acidification Observation Network (GOA-ON)](http://www.goa-on.org); for additional information see the [GOA-ON Data Portal](http://portal.goa-on.org).


{% highlight python %}
import requests
import geojson

wfs_url = "http://data.nanoos.org/geoserver/ows"
params = dict(service='WFS', version='1.0.0', request='GetFeature',
              typeName='oa:goaoninv', outputFormat='json')

r = requests.get(wfs_url, params=params)
wfs_geo = geojson.loads(r.content)
{% endhighlight %}

Let's examine the general characteristics of this GeoJSON object, including its `__geo_interface__` interface, which we discussed earlier.


{% highlight python %}
print(type(wfs_geo))
print(wfs_geo.keys())
print(len(wfs_geo.__geo_interface__['features']))
{% endhighlight %}

    <class 'geojson.feature.FeatureCollection'>
    dict_keys(['type', 'totalFeatures', 'crs', 'features'])
    572


Now use the `from_features` constructor method to create a GeoDataFrame directly from the  `geojson.feature.FeatureCollection` object.


{% highlight python %}
wfs_gdf = GeoDataFrame.from_features(wfs_geo)
{% endhighlight %}

Finally, let's visualize the data set as a simple map overlay plot; and as an example, display the values for the last feature.


{% highlight python %}
wfs_gdf.plot(ax=world.plot(cmap='Set3', figsize=(10, 6)),
             marker='o', color='red', markersize=15);
{% endhighlight %}


![png](../fig/04/geopandas_intro_97_0.png)



{% highlight python %}
wfs_gdf.iloc[-1]
{% endhighlight %}




    Oceans                                                            Indian Ocean
    additional_organizations                                                      
    agency                                                                        
    city                                                                          
    comments                                                                      
    comments_about_overlaps                                                       
    contact_email                                                                 
    contact_name                              http://www.go-ship.org/Contacts.html
    country                                                                       
    data_url                            https://cchdo.ucsd.edu/cruise/33RO20180423
    department                                                                    
    deploy_date                                                                   
    depth_range                                                                   
    duration                                                                      
    frequency                                                                     
    geometry                                                      POINT (54.5 -30)
    id                                                                         626
    latitude                                                                   -30
    line_xy                      54.5,-30.0::54.4975,-29.494::54.4988,-28.9747:...
    location                                                                      
    longitude                                                                 54.5
    method                                                                        
    method_documentation                                                          
    organization                 Global Ocean Ship-Based Hydrographic Investiga...
    organization_abbreviation                                              GO-SHIP
    overlaps_with                                                                 
    parameters                   dissolved inorganic carbon; total alkalinity; ...
    parameters_planned                                                            
    platform_name                                                             I07N
    platform_name_kml                                                      626.kml
    platform_type                                                               RH
    point_xy                                                            54.5,-30.0
    project                                                                GO-SHIP
    seas                                                              Indian Ocean
    sensors                                                                       
    source_doc                                                   RepeatHydrography
    track_pt_lat                                                             -30.0
    track_pt_lon                                                              54.5
    type                                                                          
    url                                 https://cchdo.ucsd.edu/cruise/33RO20180423
    Name: 571, dtype: object



> ## Time to Explore
> Let's stop for a bit to explore on your own, hack with your neighbors, ask questions. Then we'll transition to the next notebook, on more advanced topics.
{: .callout}
