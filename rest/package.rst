R Package
=========

This resource applies to a single R package installed in the R server.

Description
-----------

.. http:get:: /rserver/package/(string: name)

   Get the description of the R package, i.e. all the entries from the ``DESCRIPTION`` file.

   This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password https://rock-demo.obiba.org/rserver/package/rlang

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.package(conn, "rlang")

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "name": "rlang",
        "Description": "A toolbox for working with base types, core R features\n  like the condition system, and core 'Tidyverse' features like tidy\n  evaluation.",
        "Enhances": "winch",
        "Built": "R 4.0.3; x86_64-pc-linux-gnu; 2021-02-22 08:25:16 UTC; unix",
        "License": "MIT + file LICENSE",
        "Imports": "utils",
        "ByteCompile": "true",
        "URL": "https://rlang.r-lib.org, https://github.com/r-lib/rlang",
        "RoxygenNote": "7.1.1",
        "BugReports": "https://github.com/r-lib/rlang/issues",
        "LazyData": "true",
        "Maintainer": "Lionel Henry <lionel@rstudio.com>",
        "Config/testthat/edition": "3",
        "Version": "0.4.10",
        "Suggests": "cli, covr, crayon, glue, magrittr, methods, pak, pillar,\nrmarkdown, testthat (>= 3.0.0), vctrs (>= 0.2.3), withr",
        "NeedsCompilation": "yes",
        "Authors@R": "c(\n    person(\"Lionel\", \"Henry\", ,\"lionel@rstudio.com\", c(\"aut\", \"cre\")),\n    person(\"Hadley\", \"Wickham\", ,\"hadley@rstudio.com\", \"aut\"),\n    person(given = \"mikefc\",\n           email = \"mikefc@coolbutuseless.com\", \n           role = \"cph\", \n           comment = \"Hash implementation based on Mike's xxhashlite\"),\n    person(given = \"Yann\",\n           family = \"Collet\",\n           role = \"cph\", \n           comment = \"Author of the embedded xxHash library\"),\n    person(\"RStudio\", role = \"cph\")\n    )",
        "Date/Publication": "2020-12-30 15:00:02 UTC",
        "Packaged": "2020-12-18 09:35:30 UTC; lionel",
        "Biarch": "true",
        "Title": "Functions for Base Types and Core R and 'Tidyverse' Features",
        "Encoding": "UTF-8",
        "Repository": "CRAN",
        "Author": "Lionel Henry [aut, cre],\n  Hadley Wickham [aut],\n  mikefc [cph] (Hash implementation based on Mike's xxhashlite),\n  Yann Collet [cph] (Author of the embedded xxHash library),\n  RStudio [cph]",
        "Package": "rlang",
        "Depends": "R (>= 3.3.0)"
      }

   :>json string name: R package name.
   :>json string <field>: Field value from the ``DESCRIPTION`` file.

   :reqheader Authorization: As described in the :ref:`rest-auth` section
   :reqheader Accept: ``*/*``
   :resheader Content-Type: ``application/json``
   :statuscode 200: Server is alive and functional.
   :statuscode 401: User is not authenticated.
   :statuscode 403: User does not have the appropriate role for this operation.
   :statuscode 404: Package with provided name could not be found.
   :statuscode 500: Package description could not be retrieved, when R server is not running for instance.

Remove
------

.. http:delete:: /rserver/package/(string:name)

   Remove the R package.

   This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password -X DELETE https://rock-demo.obiba.org/rserver/package/rlang

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.package_rm(conn, "rlang")

   :reqheader Authorization: As described in the :ref:`rest-auth` section
   :statuscode 204: Operation was completed. It could have failed silently.
   :statuscode 401: User is not authenticated.
   :statuscode 403: User does not have the appropriate role for this operation.
   :statuscode 500: An error occurred, when R server is not running for instance.
