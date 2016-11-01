FROM continuumio/anaconda
RUN apt-get update --fix-missing && apt-get install -y tar git curl nano wget
RUN mkdir /data
RUN mkdir /notebooks
RUN conda create -c conda-forge -n vectorenv python=2.7 geopandas geojson folium mplleaflet cartopy rasterstats psycopg2 jupyter
