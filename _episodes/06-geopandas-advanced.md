---
title: "GeoPandas Advanced Topics"
teaching: 30
exercises: 0
questions:
- "What additional capabilities does GeoPandas provide, including data access, plotting and analysis?"
- "How does it integrate with other common Python tools?"
- "How do GeoPandas data objects integrate with analyses of raster data over vector geospatial features?"
objectives:
- Explore additional GeoPandas capabilities in reading from PostGIS and using its plot method.
- Learn how to dissolve (aggregate) polygons into larger units, and apply spatial joins across GeoDataFrames, as examples of GeoPandas spatial operators.
- Learn how to explore project (CRS) information and reproject.
- Plot choropleth maps
- Using folium to create interactive maps from a GeoDataFrame
- Explore simple but powerful capabilities offered by the rasterstats package to generate summaries and statistics of raster properties over vector features, and explore these via GeoPandas.
keypoints:
- While still a young package, GeoPandas offers powerful capabilities.
- GeoJSON and the common `__geo_interface__` enable convenient and widespread geospatial data object exchange across geospatial packages in Python.
---

## View episode content in a Jupyter Notebook

[Open the Jupyter Notebook in nbviewer](https://github.com/geohackweek/tutorial_contents/blob/master/vector/notebooks/geopandas_advanced.ipynb)


## GeoPandas: Advanced topics

## 1. Introduction

We covered the basics of GeoPandas in the previous episode and notebook. Here, we'll extend that introduction to illustrate additional aspects of GeoPandas and its interactions with other Python libraries, covering fancier mapping, analysis (unitary and binary spatial operators), raster zonal stats + GeoPandas. 

**Here are the main sections in this episode / notebook:**
* Read HydroBASINS Pacific NW / Western Washington from a shape file.
* Dissolve into larger watersheds, and reproject
* Plot `choropleth` map based on calculated watershed areas
* Choropleth map as an interactive map with folium
* Spatial join, `sjoin`, of polygons on points
* rasterstats: "zonal" statistics from polygons on rasters

## 2. Set up packages and data file path
We'll use these throughout the rest of the tutorial. Note that we're not using the latest and greatest `Matplotlib`, 2.x. Some package dependency is forcing the use of `Matplotlib` 1.5.


{% highlight python %}
%matplotlib inline

from __future__ import (absolute_import, division, print_function)
import os
import json
import psycopg2

import matplotlib as mpl
import matplotlib.pyplot as plt
# The two statemens below are used mainly to set up a plotting
# default style that's better than the default from Matplotlib 1.x
# Matplotlib 2.0 supposedly has better default styles.
import seaborn as sns
plt.style.use('bmh')

from shapely.geometry import Point
import pandas as pd
import geopandas as gpd
from geopandas import GeoSeries, GeoDataFrame

data_pth = "../data"
{% endhighlight %}


{% highlight python %}
mpl.__version__, pd.__version__, gpd.__version__
{% endhighlight %}




    ('1.5.3', '0.20.3', '0.3.0')




{% highlight python %}
with open(os.path.join(data_pth, "db.json")) as f:
    db_conn_dict = json.load(f)
{% endhighlight %}

## 3. Read HydroBASINS North America / Western Washington
Read [HydroBASINS](http://hydrosheds.org/page/hydrobasins) "all-levels" (lev00) hierarchical watersheds dataset for the Pacific NW, from Amazon Cloud PostgreSQL/PostGIS database. **Watersheds in the dataset are at the finest (highest resolution) "Pfastetter" hierarchical level, level 12.**

`read_postgis` is called as before, except now we'll apply a SQL filter (server side) to the PostGIS dataset to select only the Pfastetter level-4 watershed with code 7831: `WHERE pfaf_4 = 7831`. This is **most of Western Washington.** Watershed polygons will still be read at their original level 12 resolution.


{% highlight python %}
conn = psycopg2.connect(**db_conn_dict)
{% endhighlight %}


{% highlight python %}
hydrobas_ww = gpd.read_postgis(
    "SELECT * FROM hybas_na_lev00_v1c WHERE pfaf_4 = 7831", 
    conn, geom_col='polygongeom', crs={'init': u'epsg:4326'}, 
    coerce_float=False)
{% endhighlight %}


{% highlight python %}
conn.close()
{% endhighlight %}


{% highlight python %}
len(hydrobas_ww)
{% endhighlight %}




    413



413 polygon features returned. Let's examine the attributes for the first feature.


{% highlight python %}
hydrobas_ww.iloc[0]
{% endhighlight %}




    gid                                                        19945
    hybas_id                                             7.00001e+09
    next_down                                                      0
    next_sink                                            7.00001e+09
    main_bas                                             7.00001e+09
    dist_sink                                                      0
    dist_main                                                      0
    sub_area                                                   135.4
    up_area                                                    135.4
    endo                                                           0
    coast                                                          0
    order                                                          1
    sort                                                       19945
    pfaf_1                                                         7
    pfaf_2                                                        78
    pfaf_3                                                       783
    pfaf_4                                                      7831
    pfaf_5                                                     78310
    pfaf_6                                                    783101
    pfaf_7                                                   7831010
    pfaf_8                                                  78310101
    pfaf_9                                                 783101010
    pfaf_10                                              7.83101e+09
    pfaf_11                                              7.83101e+10
    pfaf_12                                              7.83101e+11
    polygongeom    (POLYGON ((-123.35 46.36666666666669, -123.349...
    Name: 0, dtype: object



**Plot a categorical map with coloring based on the aggregating column `pfaf_7`.** Watershed *boundaries* are at the high-resolution Pfastetter level 12.


{% highlight python %}
hydrobas_ww.plot(column='pfaf_7', cmap='Paired', categorical=True, figsize=(14, 8));
{% endhighlight %}


![png](../fig/06/geopandas_advanced_16_0.png)


## 4. Dissolve into larger watersheds, and reproject

### Dissolve source polygons into larger watersheds based on attribute values
Apply GeoDataFrame [dissolve](http://geopandas.org/aggregation_with_dissolve.html) aggregation method (implemented from lower level `shapely` operators) on level-7 Pfastetter codes (`pfaf_7`) shown in the plot above. Aggregate attributes, retaining only `pfaf_7` and `pfaf_6` (plus `polygongeom`, of course). This operation results in only 17 polygons, from the original 413.


{% highlight python %}
cols = ['pfaf_6', 'pfaf_7', 'polygongeom']
hydrobas_ww_p7 = hydrobas_ww[cols].dissolve(by='pfaf_7', aggfunc='first', as_index=False)
len(hydrobas_ww_p7)
{% endhighlight %}




    17



Let's examine some of the features.


{% highlight python %}
hydrobas_ww_p7.head()
{% endhighlight %}




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>pfaf_7</th>
      <th>polygongeom</th>
      <th>pfaf_6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7831010</td>
      <td>(POLYGON ((-123.5722222222222 46.2458333333333...</td>
      <td>783101</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7831020</td>
      <td>POLYGON ((-123.1791666666666 46.33333333333336...</td>
      <td>783102</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7831031</td>
      <td>(POLYGON ((-123.9597222222222 46.9666666666667...</td>
      <td>783103</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7831032</td>
      <td>POLYGON ((-123.8583333333333 47.39583333333336...</td>
      <td>783103</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7831033</td>
      <td>POLYGON ((-124.3 47.34583333333336, -124.30221...</td>
      <td>783103</td>
    </tr>
  </tbody>
</table>
</div>



Plot the results. Looks like the previous plot, except the polygon boundaries are now the pfaf_7 watersheds (same as the background colors).


{% highlight python %}
hydrobas_ww_p7.plot(column='pfaf_7', cmap='Paired', categorical=True, figsize=(14, 8));
{% endhighlight %}


![png](../fig/06/geopandas_advanced_23_0.png)


> ## Beware of "invalid" geometries!
> **Beware that `dissolve` may fail if there are "invalid" geometries.**
> This code is based on a GeoDataFrame examined in the previous, intro notebook. The 6 geometries/points reported are invalid (and are reported by the `is_valid()` method). This dissolve statement does work, though.
> ```python
> seas_grp = seas[['oceans', 'geometry']]
> seas_oceans_diss = seas_grp[seas_grp.geometry.is_valid].dissolve(by='oceans')
> 
> Ring Self-intersection at or near point 10.407218181818182 54.821390909090908
> Self-intersection at or near point -79.365827272727287 76.296645454545455
> Ring Self-intersection at or near point 10.979445510225332 54.380555030408686
> Ring Self-intersection at or near point 133.61550925464189 -4.3005540903175188
> Ring Self-intersection at or near point 121.91067196634913 -5.0593090510592447
> Ring Self-intersection at or near point 115.29553592754269 -7.0082630551828515
> ```
{: .callout}

### Reproject (transform) to WA State Plane South, epsg:2927
Partly so we can calculate polygon areas in linear units, not geodetic degrees. But also because that's the projection used by most state and local governments in Washington.
* [http://epsg.io/?q=2927](http://epsg.io/?q=2927)
* [http://spatialreference.org/ref/epsg/2927/](http://spatialreference.org/ref/epsg/2927/)
* [Report from http://www.epsg-registry.org](http://www.epsg-registry.org/report.htm?type=selection&entity=urn:ogc:def:crs:EPSG::2927&reportDetail=short&style=urn:uuid:report-style:default-with-code&style_name=OGP%20Default%20With%20Code&title=EPSG:2927)

No need to go to a web site to learn more about what `epsg:2927` is. Use `pyepsg`, which issues queries to [http://epsg.io](http://epsg.io) web services.


{% highlight python %}
import pyepsg
{% endhighlight %}

Extract the epsg code from the string returned by `crs['init']`, then query epsg `2927`.


{% highlight python %}
hydrobas_ww_p7_epsg_str = hydrobas_ww_p7.crs['init'].split(':')[1]
pyepsg.get(hydrobas_ww_p7_epsg_str)
{% endhighlight %}




    <GeodeticCRS: 4326, WGS 84>




{% highlight python %}
pyepsg.get('2927')
{% endhighlight %}




    <ProjectedCRS: 2927, NAD83(HARN) / Washington South (ftUS)>



**Apply the crs transformation (reprojection)** using `to_crs` method.


{% highlight python %}
hydrobas_ww_p7_wasp = hydrobas_ww_p7.to_crs(epsg=2927)
{% endhighlight %}

**Plot the reprojected map.** Note that, being in a planar project (not geodetic), the shape looks different compared to the previous map. More "normal". And the axes are now in `feet` relative to some origin.


{% highlight python %}
hydrobas_ww_p7_wasp.plot(column='pfaf_7', cmap='Paired', categorical=True, figsize=(14, 8));
{% endhighlight %}


![png](../fig/06/geopandas_advanced_34_0.png)


## 5. Plot `choropleth` map based on calculated watershed areas
As the projection is in `feet`, auto-calculated polygon areas will be in feet<sup>2</sup>. So let's convert to miles<sup>2</sup> first (why not!). We'll add a new column to the GeoDataFrame.


{% highlight python %}
hydrobas_ww_p7_wasp['area_mi2'] = hydrobas_ww_p7_wasp.geometry.area / 27878400
hydrobas_ww_p7_wasp.head(3)
{% endhighlight %}




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>pfaf_7</th>
      <th>polygongeom</th>
      <th>pfaf_6</th>
      <th>area_mi2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7831010</td>
      <td>(POLYGON ((863343.6925921033 347890.2242189167...</td>
      <td>783101</td>
      <td>1375.137396</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7831020</td>
      <td>POLYGON ((963803.8027083561 376154.9965688475,...</td>
      <td>783102</td>
      <td>2107.945774</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7831031</td>
      <td>(POLYGON ((776917.1877152125 614568.383332147,...</td>
      <td>783103</td>
      <td>528.472846</td>
    </tr>
  </tbody>
</table>
</div>



> ## Pandas `groupby` aggregation
> Now you could get the area of a pfaf_6 watershed via simple Pandas DataFrame `groupby` aggregation (sum).
{: .callout}

Plot the choloropleth, using `area_mi2`.

The "fisher_jenks" value segmentation `scheme` (using 7 segments, k=7) used is one of the available `pysal.esda.mapclassify.Map_Classifier` classifiers from the powerful [PySAL package](http://pysal.github.io) (Python Spatial Analysis Library); GeoPandas can use these classifiers if PySAL is installed, as it is here. To get the list of classifiers, use:
{% highlight python %}
import pysal
print(pysal.esda.mapclassify.Map_Classifier.__doc__)
{% endhighlight %}


{% highlight python %}
f, ax = plt.subplots(1, figsize=(10, 6))
ax.set_title('Watersheds by area ($mi^2$)')
hydrobas_ww_p7_wasp.plot(column='area_mi2', scheme='fisher_jenks', k=7, 
                         cmap=plt.cm.Blues, legend=True, ax=ax)
ax.set_axis_off()
plt.axis('equal');
{% endhighlight %}


![png](../fig/06/geopandas_advanced_39_0.png)


## 6. Choropleth map as an interactive map with folium
[Folium](https://github.com/python-visualization/folium) is very cool, specially for use in Jupyter notebooks; or to export into stand-alone HTML.


{% highlight python %}
import folium
{% endhighlight %}


{% highlight python %}
folium.__version__
{% endhighlight %}




    '0.4.0'



`m.choropleth` internally splits the geometry from the other attributes in `hydrobas_ww_p7_wasp`, and rejoins them based on the key `pfaf_7`. `key_on` uses an attribute reference derived from GeoJSON representations; this is awkward, and hopefully will be simplified in future folium implementations.


{% highlight python %}
m = folium.Map(location=[47.8, -122.5], zoom_start=7, tiles="cartodbpositron")

m.choropleth(
    geo_data=hydrobas_ww_p7_wasp,
    data=hydrobas_ww_p7_wasp,
    columns=['pfaf_7', 'area_mi2'],
    key_on='feature.properties.pfaf_7',
    legend_name='Area (sq mi)', 
    fill_color='YlGn',
    fill_opacity=0.4,
    highlight=True)

m
{% endhighlight %}


![png](../fig/06/geopandas_advanced_folium_screenshot.png)


This map is interactive, so play with it (zoom and pan). There is a lot more to explore in Folium! This is just a teaser.

## 7. Spatial join, `sjoin`, of polygons on points
We'll use an old, local snapshot of NANOOS coastal and marine monitoring stations in the Pacific NW, from the [NANOOS Visualization System (NVS) Data Explorer](http://nvs.nanoos.org/Explorer). While many stations are moorings on marine waters, some are inshore or in tidal shores and will overlap the watershed boundaries. The point file is in the [GeoPackage](http://www.geopackage.org) format, an OGC format implemented in SQLite.


{% highlight python %}
nanoosstations_gdf = gpd.read_file(os.path.join(data_pth, "nanoos_nvs.gpkg"))
len(nanoosstations_gdf)
{% endhighlight %}




    194




{% highlight python %}
nanoosstations_gdf.iloc[-1]
{% endhighlight %}




    platform_label                                     WADOH_HendrsnBay1
    platform_type                                   Fixed Shore Platform
    name                                     Henderson Bay site, W shore
    short_name                                       WADOH Henderson Bay
    lat                                                          47.3612
    lon                                                         -122.697
    state                                                     Washington
    region                                                   Puget Sound
    provider                                                       WADOH
    provider_url                                   http://www.doh.wa.gov
    provider_type                                                  State
    data_source                                                  Decagon
    data_source_url                               http://www.decagon.com
    url                http://www.doh.wa.gov/CommunityandEnvironment/...
    status                                                        online
    status_date                                     2016-06-24T18:00:00Z
    nvs_station_url    http://nvs.nanoos.org/Explorer?action=oiw:fixe...
    geometry                                   POINT (-122.6966 47.3612)
    Name: 193, dtype: object



Points are on the coasts of the Pacific NW (BC, WA, OR) and out in the open ocean.


{% highlight python %}
nanoosstations_gdf.plot(markersize=15);
{% endhighlight %}


![png](../fig/06/geopandas_advanced_50_0.png)


**Apply "inner" spatial join with `sjoin` operator** from the `gpd.tools` package. An inner join will retain only overlapping features. Then plot as a map overlay on top of `hydrobas_ww_p7`, categorizing (coloring) each point by the `pfaf_6` watershed it's in.


{% highlight python %}
nanoossta_hydrobas_ww_gdf = gpd.tools.sjoin(nanoosstations_gdf, hydrobas_ww_p7, how="inner")
len(nanoossta_hydrobas_ww_gdf)
{% endhighlight %}




    63




{% highlight python %}
f, ax = plt.subplots(1, figsize=(10, 6))
ax.set_axis_off()
plt.axis('equal')
hydrobas_ww_p7.plot(ax=ax, cmap='Greys_r', linewidth=1)
nanoossta_hydrobas_ww_gdf.plot(column='pfaf_6', markersize=30, 
                               categorical=True, legend=True, ax=ax);
{% endhighlight %}


![png](../fig/06/geopandas_advanced_53_0.png)


## 8. rasterstats: "zonal" statistics from polygons on rasters
We'll end by mixing features from a GeoDataFrame with a raster, applying zonal statistics using the cool and light weight [rasterstats](https://github.com/perrygeo/python-rasterstats) package.

Monthly Juy long-term climatology precipitation. The original monthly time series data are from the [PRISM Climate Group](http://prism.oregonstate.edu); the monthly climatology and Pacific NW clip were created by your truly and Don Setiawan for the [BiGCZ project](http://bigcz.org).


{% highlight python %}
ppt_july_tif_pth = os.path.join(data_pth, 'prism_precipitation_july_climatology.tif')
{% endhighlight %}

### rasterio

Rasterstas uses [rasterio](https://mapbox.github.io/rasterio) to read rasters (and `fiona` to read vector datasets), so we'll first do a quick exploration of rasterio.


{% highlight python %}
import rasterio
import rasterio.plot as rioplot
{% endhighlight %}


{% highlight python %}
rasterio.__version__
{% endhighlight %}




    '0.36.0'




{% highlight python %}
ppt_july = rasterio.open(ppt_july_tif_pth)
ppt_july
{% endhighlight %}




    <open RasterReader name='../data/prism_precipitation_july_climatology.tif' mode='r'>



Examine the metadata read from the raster file (we can confirm CRS is epsg:4326), then plot the raster. 


{% highlight python %}
ppt_july.meta
{% endhighlight %}



{% highlight json %}
    {"affine": Affine(0.04, 0.0, -125.12921197411002,
           0.0, -0.04, 49.045740291264174),
     "count": 1,
     "crs": CRS({"init": "epsg:4326"}),
     "driver": "GTiff",
     "dtype": "float32",
     "height": 177,
     "nodata": -9999.0,
     "transform": (-125.12921197411002, 0.04, 0.0, 49.045740291264174, 0.0, -0.04),
     "width": 353}
{% endhighlight %}


> ## Plotting issue
> `with_bounds=True` is supposed to display the X & Y coordinate labels (lat & lon) from the metadata. We don't know why this isn't working.
{: .callout}

{% highlight python %}
rioplot.show(ppt_july, with_bounds=True, cmap=plt.cm.Blues);
{% endhighlight %}


![png](../fig/06/geopandas_advanced_65_0.png)


### Apply rasterstas `zonal_stats`

Apply `zonal_stats` from `rasterstats` package. Can pass a `GeoDataFrame` directly (instead of the file path to a GIS file) because it implements our old friend, the `__geo_interface__` method. For the raster, we pass its file path.

`zonal_stats` returns a geojson with the original properties plus the zonal statistics.


{% highlight python %}
import rasterstats as rs
{% endhighlight %}


{% highlight python %}
rs.__version__
{% endhighlight %}




    '0.12.0'



Unfortunately, `rasterstats` requires the geometry column to be named "geometry". So, we'll rename it first.


{% highlight python %}
hydrobas_ww_p7 = hydrobas_ww_p7.rename(columns={'polygongeom': 'geometry'})
hydrobas_ww_p7._geometry_column_name = 'geometry'
{% endhighlight %}


{% highlight python %}
zonal_ppt_gjson = rs.zonal_stats(hydrobas_ww_p7, ppt_july_tif_pth, prefix='pptjuly_',
                                 geojson_out=True)
{% endhighlight %}



{% highlight python %}
type(zonal_ppt_gjson), len(zonal_ppt_gjson)
{% endhighlight %}




    (list, 17)




{% highlight python %}
zonal_ppt_gdf = GeoDataFrame.from_features(zonal_ppt_gjson)
zonal_ppt_gdf.head(2)
{% endhighlight %}




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geometry</th>
      <th>pfaf_6</th>
      <th>pfaf_7</th>
      <th>pptjuly_count</th>
      <th>pptjuly_max</th>
      <th>pptjuly_mean</th>
      <th>pptjuly_min</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(POLYGON ((-123.5722222222222 46.2458333333333...</td>
      <td>783101</td>
      <td>7831010</td>
      <td>259</td>
      <td>49.824776</td>
      <td>33.674382</td>
      <td>23.438837</td>
    </tr>
    <tr>
      <th>1</th>
      <td>POLYGON ((-123.1791666666666 46.33333333333336...</td>
      <td>783102</td>
      <td>7831020</td>
      <td>404</td>
      <td>96.738289</td>
      <td>29.443886</td>
      <td>15.356521</td>
    </tr>
  </tbody>
</table>
</div>



#### And finally, a choropleth map of July precipitation by watershed!


{% highlight python %}
f, ax = plt.subplots(1, figsize=(8, 6))
ax.set_title('Mean July precipitation ($mm/month$) by watershed')
zonal_ppt_gdf.plot(column='pptjuly_mean', scheme='Equal_Interval', k=5, 
                   cmap=plt.cm.Blues, legend=True, linewidth=1, ax=ax)
plt.axis('equal');
{% endhighlight %}


![png](../fig/06/geopandas_advanced_76_0.png)

