R Packages Resources
====================

These resources are for managing the R packages installed in the R server.

List
----

.. http:get:: /rserver/packages

   List the installed packages. The returned data structure is a matrix.

   This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password https://rock-demo.obiba.org/rserver/packages

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.packages(conn)

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "rowNames": [
          "annotate",
          "AnnotationDbi",
          "AnnotationFilter",
          "backports",
          "base64",
          "..."
        ],
        "columnNames": [
          "Package",
          "LibPath",
          "Version",
          "Priority",
          "Depends",
          "Imports",
          "..."
        ],
        "rows": [
          [
            "annotate",
            "/home/yannick/R/library",
            "1.68.0",
            null,
            "R (>= 2.10), AnnotationDbi (>= 1.27.5), XML",
            "Biobase, DBI, xtable, graphics, utils, stats, methods,\nBiocGenerics (>= 0.13.8), httr",
            null,
            "hgu95av2.db, genefilter, Biostrings (>= 2.25.10), IRanges,\nrae230a.db, rae230aprobe, tkWidgets, GO.db, org.Hs.eg.db,\norg.Mm.eg.db, hom.Hs.inp.db, humanCHRLOC, Rgraphviz, RUnit,",
            null,
            "Artistic-2.0",
            null,
            null,
            null,
            null,
            "no",
            "4.0.3",
            "Annotation for microarrays",
            "Using R enviroments for annotation.",
            "R. Gentleman",
            "Bioconductor Package Maintainer <maintainer@bioconductor.org>",
            "2020-10-27",
            null,
            null,
            null
          ],
          [
            "..."
          ]
        ]
      }

   :>json strings rowNames: Array of R package names.
   :>json strings columnNames: Array of field names from the ``DESCRIPTION`` files.
   :>json arrays rows: Array of arrays of strings, each value corresponds to the row (=package) name and column (=field) name.

   :reqheader Authorization: As described in the :ref:`rest-auth` section
   :reqheader Accept: ``*/*``
   :resheader Content-Type: ``application/json``
   :statuscode 200: Server is alive and functional.
   :statuscode 500: Package list could not be retrieved, when R server is not running for instance.

Update
------

.. http:put:: /rserver/packages

 Update all CRAN R packages.

 This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

 **Example requests**

 Using cURL

 .. sourcecode:: shell

    curl --user administrator:password -X PUT https://rock-demo.obiba.org/rserver/packages

 Using R (`rockr <https://github.com/obiba/rockr>`_)

 .. sourcecode:: r

    library(rockr)
    conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
    rockr.packages_update(conn)

 :reqheader Authorization: As described in the :ref:`rest-auth` section
 :statuscode 200: Operation was successful.
 :statuscode 500: An error occurred, when R server is not running for instance.

Remove
------

.. http:delete:: /rserver/packages?name=(package_name)

  Remove specified R packages.

  This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user administrator:password -X PUT https://rock-demo.obiba.org/rserver/packages?name=annotate&name=rlang

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
     rockr.packages_rm(conn, c("annotate", "rlang"))

  :query string name: One or more R package names to remove.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :statuscode 204: Operation was successful.
  :statuscode 500: An error occurred, when R server is not running for instance.

Install
-------

.. http:post:: /rserver/packages?name=(package_name)

  Install a R package from CRAN, `GitHub <https://github.com>`_ or `Bioconductor <https://bioconductor.org/>`_.

  This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user administrator:password -X POST https://rock-demo.obiba.org/rserver/packages?name=annotate&manager=cran

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
     rockr.package_install(conn, "annotate", manager = "cran")

  :query string name: The R package name to install.
  :query string ref: The Git reference: branch or tag name, commit number. Applies to GitHub repository only. Default is ``master``.
  :query string manager: The R package repository type: ``cran``, ``github``/``gh`` or ``bioconductor``/``bioc``. Default is ``cran``.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :statuscode 204: Operation was successful.
  :statuscode 500: An error occurred, when R server is not running for instance.


DataSHIELD
----------

.. http:get:: /rserver/packages/_datashield

   Get the installed `DataSHIELD <https://www.datashield.ac.uk/>`_ R packages with their settings. The returned data structure is an object with one entry per DataSHIELD package.

   This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password https://rock-demo.obiba.org/rserver/packages/_datashield

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.packages_datashield(conn)

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "dsBase": {
          "AggregateMethods": [
            "asFactorDS1",
            "asListDS",
            "boxPlotGGDS",
            "checkNegValueDS",
            "classDS",
            "corTestDS",
            "corDS",
            "covDS",
            "dataFrameSubsetDS1",
            "densityGridDS",
            "..."
          ],
          "AssignMethods": [
            "absDS",
            "asCharacterDS",
            "asDataMatrixDS",
            "asFactorDS",
            "asFactorDS2",
            "asIntegerDS",
            "asListDS",
            "asLogicalDS",
            "..."
          ],
          "Options": [
            "datashield.privacyLevel=5",
            "default.nfilter.glm=0.33",
            "default.nfilter.kNN=3",
            "default.nfilter.string=80",
            "default.nfilter.subset=3",
            "default.nfilter.stringShort=20",
            "default.nfilter.tab=3",
            "default.nfilter.noise=0.25",
            "default.nfilter.levels=0.33"
          ]
        },
        "resourcer": {
          "AssignMethods": [
            "as.resource.data.frame",
            "as.resource.object",
            "as.resource.tbl"
          ]
        }
      }


   :>json strings AggregateMethods: Array of aggregation function names.
   :>json strings AssignMethods: Array of assign function names.
   :>json strings Options: Array of R options name and value.

   :reqheader Authorization: As described in the :ref:`rest-auth` section
   :reqheader Accept: ``*/*``
   :resheader Content-Type: ``application/json``
   :statuscode 200: Server is alive and functional.
   :statuscode 500: Package list could not be retrieved, when R server is not running for instance.
