Knihovna GDAL pro přístup k rastrovým a vektorovým datům
========================================================

Programátorská knihovna `GDAL <http://gdal.org>`_ (**Geospatial Data
Abstraction Library**) je určena pro práci s množstvím `rastrových
<http://gdal.org/formats_list.html>`_ i `vektorových
<http://gdal.org/ogr_formats.html>`_ formátů používaných v GIS. GDAL
je využíván celou řadou dalších programů jako základní knihovna
(`GRASS GIS <http://grass.osgeo.org>`_, `QGIS <http://qgis.org>`_,
...), dokonce i v proprietárním produktu `ArcGIS
<http://www.arcgis.com>`_.

.. note:: V dřívějšich verzích byla tato knihovna rozdělena na dvě
    části. GDAL pracující s rastrovými daty a OGR pro vektorová
    data. Ve verzi 2.0 byly tyto dvě větve sloučeny. Stále však můžete
    narazit na označení části pro práci s vektory jako *OGR*.

.. tip:: Více ke knihovně GDAL z uživatelského pohledu ve školení
         :skoleni:`Úvod do (open source) GIS <open-source-gis>`.
                  
.. toctree::
   :maxdepth: 2

   vektorova_data/index.rst
   rastrova_data/index.rst
