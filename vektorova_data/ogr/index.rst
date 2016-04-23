.. _ogr:

Knihovna OGR
============

**OGR** jako součást `GDAL <http://www.gdal.org>`_ je tradiční
knihovna pro práci s vektorovými daty.  Knihovna OGR slouží především
k převodům mezi vektorovými formáty (ale i další práci s vektorovými
daty, jejich geometrií a atributy). V současné době knihovna podporuje
`více než 80 formátů <http://gdal.org/ogr_formats.html>`_.

.. _ogr-model:

Knihovna OGR pracuje s konceptem vrstev (*layers*) uložených v datových
zdrojích (*data source*). OGR používá pro čtení a zápis dat do
podporovaných datových formátů svůj vlastní *abstraktní model*, který
se může jevit jako těžkopádný, nicméně spolehlivě funguje pro všechny
případy:

* **Driver** - ovladač pro čtení a zápis dat
* **Data Source** - datový zdroj (soubor, databáze, protokol, ...)
* **Layer** - datová vrstva (obsah souboru, databázová tabulka, ...)
* **Feature** - geoprvek (vzhledy jevu)
* **Field, Geometry** - atributy, geometrie
    
.. aafig::
    :aspect: 70
    :scale: 90

                                               +-------+          +---------+
                                               |       |          |         |
                                          +--->+ Layer |     +--->+ Feature |
                                         /     |       |    /     |         |
                                        /      +-------+   /      +---------+
                                       /                  /
    +--------+         +------------+ /        +-------+ /        +---------+
    |        |         |            |/         |       |/         |         |
    | Driver +-------->+ DataSource +--------->+ Layer +--------->+ Feature |
    |        |         |            |\         |       |\         |         |
    +--------+         +------------+ \        +-------+ \        +---------+
                                       \                  \
                                        \      +-------+   \      +---------+
                                         \     |       |    \     |         |
                                          +--->+ ...   |     +--->+ ...     |
                                               |       |          |         |
                                               +-------+          +---------+
                                       
Popis abstraktního modelu pro vektorová data:
http://gdal.org/ogr_arch.html

*Rozhraní pro Python* představuje pouze abstraktní :abbr:`API (rozhraní pro
programování aplikací)` nad původními funkcemi a třídami z jazyka C++,
ve kterém je GDAL naprogramovaný. Také z tohoto důvodu se mohou
některé postupy jevit jako těžkopádné.

Dokumentace: http://www.gdal.org/ogr_apitut.html

API: http://gdal.org/python/

Cookbook: https://pcjericks.github.io/py-gdalogr-cookbook/vector_layers.html

Obalová zóna
------------

V tomto příkladu si ukážeme, jak otevřít vektorová data ve formátu
Esri Shapefile, načíst datovou vrstvu, zobrazit atributy geoprvků a
vytvořit obalovou zónu nad načtenými geoprvky.

Nejprve otevření souboru s daty:

.. code-block:: python

    >>> from osgeo import ogr
    >>> ds = ogr.Open("chko.shp")
    >>> ds
    <osgeo.ogr.DataSource; proxy of <Swig Object of type 'OGRDataSourceShadow *' at 0x7f98d8152a50> >
    >>> ds.GetLayerCount()
    1

Práce s vrstvou, její otevření:

.. code-block:: python

    >>> l = ds.GetLayer(0)
    >>> l
    <osgeo.ogr.Layer; proxy of <Swig Object of type 'OGRLayerShadow *' at 0x7f98d80fa870> >
    >>> l.GetFeatureCount()
    5626

Schéma vrstvy - definice typu geometrie a jednotlivých atributových polí:

.. code-block:: python
    
   >>> l.GetGeomType()
   3
   >>> l.GetGeomType() == ogr.wkbPolygon
   True
   >>> l.schema
   [<osgeo.ogr.FieldDefn; proxy of <Swig Object of type 'OGRFieldDefnShadow *' at 0x7f98d80fa9f0> >,
   <osgeo.ogr.FieldDefn; proxy of <Swig Object of type 'OGRFieldDefnShadow *' at 0x7f98d80fa8...
   >>> ...
   >>> l.schema[4].name
   'NAZEV'

Vypsání názvů geoprvky (atribut ``NAZEV``):

.. code-block:: python

    >>> features_nr = l.GetFeatureCount()
    >>> for i in range(features_nr):
    ...     f = l.GetNextFeature()
    ...     print f.GetField('NAZEV')
    Český ráj
    ...

Vypsání vlastnosti geometrické složky popisu geoprvků (minimálního
ohraničujícího obdélíku a centroidu polygonu):

.. code-block:: python

    >>> f = l.GetFeature(54)
    >>> f.GetField('NAZEV')
    >>> print f.GetField('NAZEV')
    Český ráj
    >>> geom = f.GetGeometryRef()
    >>> geom.GetEnvelope()
    (-683329.1875, -681265.625, -993228.75, -991528.0)
    >>> c = geom.Centroid()
    >>> c.GetPoint()
    (-682407.4126500859, -992433.3498782327, 0.0)
    >>> buff = c.Buffer(100)
    >>> geom.Intersects(buff)
    True

Následující příklad ukazuje přístup k vektorovým datům *od A do Z*,
tedy vytvoření nové datové vrstvy, nastavení metadat, vytvoření a
zápis nového geoprvku, uložení změn do souboru. To celé by šlo vykonat
pomocí výše zmíněné knihovny :ref:`Fiona <fiona>` několikanásobně
jednodušeji. OGR přistupuje k datům na nižší úrovni, což může být
někdy výhodnější.

.. code-block:: python

    >>> from osgeo import osr
    >>> # Vytvoření driveru pro formát GML a vytvoření prázdného souboru
    >>> drv = ogr.GetDriverByName('GML')
    >>> ds = drv.CreateDataSource('/tmp/out.gml')
    >>> srs = osr.SpatialReference()
    >>> srs.ImportFromEPSG(5514)
    >>> srs.ExportToProj4()
    '+proj=krovak +lat_0=49.5 +lon_0=24.83333333333333 +alpha=30.28813972222222 +k=0.9999 +x_0=0 +y_0=0
    +ellps=bessel +towgs84=...
    >>> layer = ds.CreateLayer('out.gml', srs, ogr.wkbLineString)

    >>> # Vytvoření nového atributu 'Nazev' a 'Kod'
    >>> field_name = ogr.FieldDefn('Nazev', ogr.OFTString)
    >>> field_name.SetWidth(24)
    >>> field_number = ogr.FieldDefn('Kod', ogr.OFTInteger)
    >>> layer.CreateField(field_name)
    >>> layer.CreateField(field_number)

    >>> # Vytvoření nové geometrie typu linie - načtením z formátu WKT
    >>> line = ogr.CreateGeometryFromWkt('LINESTRING(%f %f, %f %f)' % (0, 0, 1, 1))

    >>> # Vytvoření nového prvku, nastavení geometrie a atributu Nazev
    >>> feature = ogr.Feature(layer.GetLayerDefn())
    >>> feature.SetGeometry(line)
    >>> feature.SetField("Nazev", 'Základní linie')
    >>> feature.SetField("Kod", 42)
    >>> ...
    >>> layer.CreateFeature(feature)
    >>> ...
    >>> # Úklid
    >>> feature.Destroy()
    >>> ds.Destroy()

Výsledek zkontrolujeme:

.. code:: python

    >>> ds = ogr.Open('/tmp/out.gml')
    >>> layer = ds.GetLayer(0)
    >>> layer.GetFeatureCount()
    1
    >>> ds.Destroy()

    
.. Malá odbočka k pyproj
.. 
.. .. code-block:: python
.. 
..     >>> import pyproj
..     >>> sjtsk = pyproj.Proj("+init=epsg:5514")
..     >>> wgs = pyproj.Proj("+init=epsg:4326")
.. 





