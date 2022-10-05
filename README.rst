..
    This file is part of Web Time Series Service Specification.
    Copyright (C) 2022 INPE.

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program. If not, see <https://www.gnu.org/licenses/gpl-3.0.html>.


=======================================
Web Time Series Service - Specification
=======================================

.. image:: https://img.shields.io/badge/License-GPLv3-blue.svg
        :target: https://github.com/brazil-data-cube/wtss-spec/blob/master/LICENSE
        :alt: Software License

.. image:: https://img.shields.io/badge/lifecycle-experimental-orange.svg
        :target: https://www.tidyverse.org/lifecycle/#experimental
        :alt: Software Life Cycle

.. image:: https://img.shields.io/github/tag/brazil-data-cube/wtss-spec.svg
        :target: https://github.com/brazil-data-cube/wtss-spec/releases
        :alt: Release

.. image:: https://img.shields.io/discord/689541907621085198?logo=discord&logoColor=ffffff&color=7389D8
        :target: https://discord.com/channels/689541907621085198#
        :alt: Join us at Discord


About
=====

**W**\ eb **T**\ ime **S**\ eries **S**\ ervice (WTSS) is a lightweight web service for handling time series data from remote sensing imagery. Given a location and a time interval you can retrieve the according time series as a JSON array of numbers.


In WTSS a coverage is a three dimensional array associate to spatial and temporal reference systems.


WTSS is based on three operations:

- ``list_coverages``: returns the list of all available coverages in the service.

- ``describe_coverage``: returns the metadata of a given coverage.

- ``time_series``: query the database for the list of values for a given location and time interval.


There are free and open source implementations based on this specification:

- `wtss-server <https://github.com/brazil-data-cube/wtss-server>`_: is a WTSS web server implemented in Python.

- `wtss.py <https://github.com/brazil-data-cube/wtss.py>`_: is a client API for Python.

- `wtss <https://github.com/e-sensing/wtss>`_: is a client API for R.


Repository Organization
-----------------------

- `api <./api>`_: WTSS Specification using `OpenAPI 3.0 <https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md>`_.

- `jsonschemas <./jsonschemas>`_: `JSON Schema <https://json-schema.org/>`_ for WTSS responses.


Overview of Service Operations
------------------------------


``list_coverages``
~~~~~~~~~~~~~~~~~~


The ``list_coverages`` operation can be used as follow::

    http://myserver/


It will return a JSON document such as:

.. code-block:: json

        {
            "wtss_version": "2.0.0",
            "links": [
                {
                    "rel": "service-docs",
                    "title": "Documentation for WTSS",
                    "href": "http://myserver/docs"
                },
                {
                    "rel": "data",
                    "title": "S2-SEN2COR_10_16D_STK-1",
                    "href": "http://myserver/S2-SEN2COR_10_16D_STK-1"
                },
                {
                    "rel": "data",
                    "title": "CB4_64_16D_STK-1",
                    "href": "http://myserver/CB4_64_16D_STK-1"
                }
            ]
        }


``describe_coverage``
~~~~~~~~~~~~~~~~~~~~~

If you need the metadata of a given coverage you can use the ``describe_coverage`` operation as follow::

    http://myserver/<CollectionId>


The result of ``describe_coverage`` operation is a JSON document such as:

.. code-block:: json

        {
            "name": "S2-SEN2COR_10_16D_STK",
            "version": 1,
            "fullname": "S2-SEN2COR_10_16D_STK-1",
            "description": "This datacube was generated with all available surface reflectance images processed using Sen2cor (ilumination corrections on). The data is provided with 10 meters of spatial resolution, reprojected and cropped to BDC_SM grid, considering a temporal compositing function of 16 days using the best pixel approach (Stack).",
            "title": "Sentinel-2 - MSI - Sen2cor - Cube Stack 16 days - v001",
            "timeline": [
                "2020-12-18",
                "2020-12-02",
                "2020-11-16"
            ],
            "bands": [
                {
                    "name": "B01",
                    "common_name": "coastal",
                    "scale": 0.0001,
                    "nodata": -9999.0,
                    "data_type": "int16",
                    "resolution_x": 10.0,
                    "resolution_y": 10.0,
                    "min_value": 0.0,
                    "max_value": 10000.0
                },
                {
                    "name": "B02",
                    "common_name": "blue",
                    "scale": 0.0001,
                    "nodata": -9999.0,
                    "data_type": "int16",
                    "resolution_x": 10.0,
                    "resolution_y": 10.0,
                    "min_value": 0.0,
                    "max_value": 10000.0
                },
                {
                    "name": "B03",
                    "common_name": "green",
                    "scale": 0.0001,
                    "nodata": -9999.0,
                    "data_type": "int16",
                    "resolution_x": 10.0,
                    "resolution_y": 10.0,
                    "min_value": 0.0,
                    "max_value": 10000.0
                }
            ],
            "extent": {"type": "Polygon", "coordinates": []},
            "bdc:crs": "+proj=aea +lat_0=-12 +lon_0=-54 +lat_1=-2 +lat_2=-22 +x_0=5000000 +y_0=10000000 +ellps=GRS80 +units=m +no_defs ",
            "grs_name": "BDC_SM",
            "raster_size": {
                "x": 16806,
                "y": 10986
            }
        }


``time_series``
~~~~~~~~~~~~~~~

You can retrieve the time series for a given location through the  ``time_series``::

    http://myserver/S2-SEN2COR_10_16D_STK-1/timeseries

With body:

    {
        "attributes": [
            "NDVI"
        ],
        "start_datetime": "2017-01-01T00:00:00Z",
        "end_datetime": "2017-01-16T23:00:00Z",
        "geom": {
            "type": "Polygon",
            "coordinates": [
                [
                    [
                        -54.0,
                        -12.0
                    ],
                    [
                        -54.0,
                        -11.99
                    ],
                    [
                        -53.99,
                        -11.99
                    ],
                    [
                        -53.99,
                        -11.99
                    ],
                    [
                        -54.0,
                        -12.0
                    ]
                ]
            ]
        }
    }


The result of ``time_series`` is a JSON document such as:

.. code-block:: json

        {
            "result": {
                [
                    {
                        "pixel_center": {
                            "type": "Point",
                            "coordinates": [
                                -53.99998107263968,
                                -11.989973727535231
                            ]
                        },
                        "time_series": {
                            "timeline": [
                                "2017-01-01",
                                "2017-01-17"
                            ],
                            "values": {
                                "NDVI": [
                                    7919,
                                    8457
                                ]
                            }
                        }
                    }
                ]
            },
            "query": {
                "collectionId": "S2-SEN2COR_10_16D_STK-1",
                "attributes": [ "red", "nir" ],
                "geom": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -54.0,
                                -12.0
                            ],
                            [
                                -54.0,
                                -11.99
                            ],
                            [
                                -53.99,
                                -11.99
                            ],
                            [
                                -53.99,
                                -11.99
                            ],
                            [
                                -54.0,
                                -12.0
                            ]
                        ]
                    ]
                },
                "start_datetime": "2017-01-01T00:00:00Z",
                "end_datetime": "2017-01-16T23:00:00Z",
            }
        }


Building the Documentation
==========================

Requirements
------------

The build system for the REST API documentation relies on the Node.js run-time environment:


  - `Node.js <https://nodejs.org/en/>`_ (Version 8+).
  - `ReDoc <https://github.com/Redocly/redoc>`_: generates HTML reference documentation from an OpenAPI specification file.


Build
-----

If you have Node.js installed, please, execute the following command to install the ReDoc dependency:

.. code-block:: shell

    $ npm install

After that, generate the documentation:

.. code-block:: shell

    $ npm run build

The above command will create a folder named ``dist`` with the bundled file index.html. You may open it in your web browser or may serve it with an HTTP Server.

For Python developers, you can serve the HTMl with:

.. code-block:: shell

        python3 -m http.server 8080 --directory dist


License
=======

.. admonition::
    Copyright (C) 2022 INPE.

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.
