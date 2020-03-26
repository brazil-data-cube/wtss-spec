..
    This file is part of Web Time Series Service Specification.
    Copyright (C) 2019 INPE.

    Web Time Series Service Specification is free software; you can redistribute it and/or modify it
    under the terms of the MIT License; see LICENSE file for more details.


=======================================
Web Time Series Service - Specification
=======================================

.. image:: https://img.shields.io/badge/license-MIT-green
        :target: https://github.com/brazil-data-cube/wtss-spec/blob/master/LICENSE
        :alt: Software License

.. image:: https://img.shields.io/badge/lifecycle-experimental-orange.svg
        :target: https://www.tidyverse.org/lifecycle/#experimental
        :alt: Software Life Cycle

.. image:: https://img.shields.io/github/tag/brazil-data-cube/wtss-spec.svg
        :target: https://github.com/brazil-data-cube/wtss-spec/releases
        :alt: Release

.. image:: https://badges.gitter.im/brazil-data-cube/community.png
        :target: https://gitter.im/brazil-data-cube/community#
        :alt: Join the chat


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

    http://myserver/wtss/list_coverages


It will return a JSON document such as:

.. code-block:: json

        {
            "coverages": [ "mod09q1", "mod13q1" ]
        }


``describe_coverage``
~~~~~~~~~~~~~~~~~~~~~

If you need the metadata of a given coverage you can use the ``describe_coverage`` operation as follow::

    http://myserver/wtss/describe_coverage?name=mod13q1


The result of ``describe_coverage`` operation is a JSON document such as:

.. code-block:: json

        {
            "name": "mod13q1",
            "description": "Surface Reflectance 8-Day L3 Global 250m",
            "detail": "https://lpdaac.usgs.gov/dataset_discovery/modis/modis_products_table/mod09q1",
            "dimensions": {
                "x": {
                    "name": "col_id",
                    "min_idx": 0,
                    "max_idx": 172799
                },
                "y": {
                    "name": "row_id",
                    "min_idx": 0,
                    "max_idx": 86399
                },
                "t": {
                    "name": "time_id",
                    "min_idx": 0,
                    "max_idx": 3
                }
            },
            "attributes": [{
                "name": "red",
                "description": "250m Surface Reflectance Band 1 (620–670 nm)",
                "datatype": "int16",
                "valid_range": {
                    "min": -100,
                    "max": 16000
                },
                "scale_factor": 0.0001,
                "missing_value": -28672
            }, {
                "name": "nir",
                "description": "250m Surface Reflectance Band 2 (841–876 nm)",
                "datatype": "int16",
                "valid_range": {
                    "min": -100,
                    "max": 16000
                },
                "scale_factor": 0.0001,
                "missing_value": -28672
            }, {
                "name": "quality",
                "description": "250m Reflectance Band Quality",
                "datatype": "uint16",
                "valid_range": {
                    "min": 0,
                    "max": 32767
                },
                "scale_factor": 1,
                "missing_value": 65535
            }],
            "spatial_extent": {
                "xmin": -180.0,
                "ymin": -90.0,
                "xmax": 180.0,
                "ymax": 90.0
            },
            "spatial_resolution": {
                "x": 0.00208334,
                "y": 0.00208334
            },
            "crs": {
                "proj4": "+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs",
                "wkt": "GEOGCS[\"WGS 84\",DATUM[\"WGS_1984\",SPHEROID[\"WGS 84\",6378137,298.257223563,AUTHORITY[\"EPSG\",\"7030\"]],AUTHORITY[\"EPSG\",\"6326\"]],PRIMEM[\"Greenwich\",0,AUTHORITY[\"EPSG\",\"8901\"]],UNIT[\"degree\",0.01745329251994328,AUTHORITY[\"EPSG\",\"9122\"]],AUTHORITY[\"EPSG\",\"4326\"]]"
            },
            "timeline": [ "2000-02-18", "2000-03-05", "2000-03-21" ]
        }


``time_series``
~~~~~~~~~~~~~~~

You can retrieve the time series for a given location through the  ``time_series``::

    http://myserver/wtss/time_series?coverage=mod13q1&attributes=red,nir&longitude=-54.0&latitude=-5.0&start_date=2000-02-18&end_date=2000-03-21


The result of ``time_series`` is a JSON document such as:

.. code-block:: json

        {
            "result": {
                "attributes":  [
                    {
                        "attribute": "red",
                        "values": [ 1243, 2222, 722 ]
                    },
                    {
                        "attribute": "nir",
                        "values": [ 3040, 3621, 1949 ]
                    }
                ],
                "timeline": [ "2000-02-18", "2000-03-05", "2000-03-21" ],
                "coordinates": {
                    "longitude": -53.998273633285685,
                    "latitude": -5.001041666214564,
                    "col": 60579,
                    "row": 45600
                }

            },
            "query": {
                "coverage": "mod13q1",
                "attributes": [ "red", "nir" ],
                "longitude": -54,
                "latitude": -5
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

        python3.7 -m http.server 8080 --directory dist


License
=======

.. admonition::
    Copyright (C) 2019 INPE.

    Web Time Series Service is free software; you can redistribute it and/or modify it
    under the terms of the MIT License; see LICENSE file for more details.
