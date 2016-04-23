Práce s rastrovými daty
=======================

Rastrová data mohou být v porovnání s vektorovými daty často řádově
objemnější. Tomu je třeba přizpůsobit práci s nimi. Rastrová data jsou
většinou uspořádané do matice hodnot v číselné podobě.

.. note::
   
   Více k rastrové reprezentaci ve :skoleni:`školení Úvod do GIS
   <open-source-gis/formaty/uvod.html>`.

Pro práci s rastrovými geodaty se "tradičně" používá knihovna `GDAL
<http://gdal.org>`_. Knihovna GDAL je nízkoúrovňová, přistupuje k
datům pokud možno efektivním způsobem. Alternativou ke knihovně GDAL
je `Rasterio <https://github.com/mapbox/rasterio>`_, která je nad
touto knihovnou postavena. Jedná se o jakousi analogii ke knihovnám
:doc:`OGR <../vektorova_data/ogr/index>` a :doc:`Fiona
<../vektorova_data/fiona/index>` pro práci s vektorovými daty.

.. note::

    Téma *rasterio* je více popsáno v :skoleni:`části pro začátečníky
    <geopython-zacatecnik/rastrova_data/rasterio/>`.

.. toctree::
   :maxdepth: 2

   gdal/index

..   pil/index
