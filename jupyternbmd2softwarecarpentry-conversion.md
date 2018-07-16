# Cconverting from a Python Jupyter notebook to Markdown optimized for Software Carpentry template
[Emilio Mayorga](https://github.com/emiliom/)
2018-7-16

## Converting notebooks to markdown
- For a live reference, see this source notebook and resulting page in my vector tutorial:
	- Source notebook: https://github.com/geohackweek/tutorial_contents/blob/master/vector/notebooks/geopandas_intro.ipynb
	- Converted, edited and staged software-carpentry markdown file: https://github.com/geohackweek/vector/blob/gh-pages/_episodes/04-geopandas-intro.md
	- Final result: https://geohackweek.github.io/vector/04-geopandas-intro/
- Convert all notebooks in "notebooks" directory to markdown:
  `tutorial_contents/vector/notebooks$ jupyter nbconvert --to markdown *.ipynb`
- The command creates md files together with directories storing the image outputs (except for a Folium image). I moved and renamed these files into the `geohackweek/vector` repo.
- An image is not created for interactive elements like Folium maps. Manually create and save a screenshot of the Folium map.
- Manually apply the "{: .callout}" changes, including the line-starting "> ". See the examples below.
- Make sure markdown first-level list items use "*" rather than "-", except for the software carpentry keyword header. This can be done as a search-and-replace, except list sub-items will have to be handled manually
- Convert plain url's to markdown links, to make them more user friendly (as links)
- Syntax-highlighting code blocks: {% highlight python %} {% endhighlight %}
	- Replace "```python" with "{% highlight python %}"
	- Manually replace "```" with "{% endhighlight %}"
	- Be careful not to apply this to code blocks within markdown comments
	- Manually scan for and apply {% highlight json %} highlighting; also replace single quotes with double quotes
	- See examples below
- Use appropriate relative image references. In this example, replace image directory base paths as follows:
	- "geopandas_intro_files/" with "../fig/04/"
	- "geopandas_advanced_files/" with "../fig/06/"


## Sample initial header block
**NOTE:** colons can NOT be used in this block, within individual items!
---
title: "Introduction to multidimensional arrays"
teaching: 10
exercises: 0
questions:
- "When do we need to use multidimensional arrays?"
- "What are current challenges is manipulating these datasets?"
objectives:
- explore how most people currently handle these types of datasets
- discuss how current methods are limiting the science that can be accomplished
keypoints:
- unlabelled, N-dimensional arrays of numbers (e.g. NumPy's ndarray) are the most widely used data structure in scientific computing
- these arrays lack meaningful metadata, so users must track indices in an arbitrary fashion
- in-memory operations, needed to process and visualize large arrays, are reaching limits as datasets grow in size
---

## Syntax highlighting for Python and JSON code blocks
{% highlight python %}
import xarray as xr
{% endhighlight %}

{% highlight json %}
    {"database": "geohack",
     "host": "dssg2017.csya4zsfb6y4.us-east-1.rds.amazonaws.com",
     "password": "*****",
     "port": 5432,
     "user": "*****"}
{% endhighlight %}


## Image referencing with control on image size
Use HTML rather than standard markdown:
<br>
<img src="../fig/06/dsContents.png" width = "80" border = "10">
<br>

## Example "callout" block (open by default)
> ## Isn't this the same as raster processing?
> The tools in this tutorial have some similarity to raster image processing tools.
> Both require computational engines that can manipulate large stacks of data formatted as arrays.
> Here we focus on tools that are optimized to handle data that have many variables spanning dimensions
> of time and space. See the raster tutorials for tools that are optimized for image processing of remote sensing datasets.
{: .callout}

## Example "challenge" block (collapsed by default)
> ## Displaying `Dataset` properties
> Try looking up the coordinates (coords), attributes (attrs) and data variables (data_vars) for our existing dataset.
> Look at the output and think about what this tells us about our sample dataset.
{: .challenge}
