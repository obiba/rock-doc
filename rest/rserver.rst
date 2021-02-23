R Server Resources
==================

These resources are for managing the R server process running in the background: status, start and stop.

Status
------

.. http:get:: /rserver

   Get the server detailed status, including R server and host's system information.

   This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password https://rock-demo.obiba.org/rserver

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.status(conn)

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "rock-demo",
        "version": "4.0.3",
        "encoding": "utf8",
        "tags": [
          "default"
        ],
        "running": true,
        "sessions": {
          "total": 0,
          "busy": 0
        },
        "system": {
          "cores": 16,
          "freeMemory": 8938140
        }
      }

   :>json string id: The server identifier.
   :>json string version: The R version.
   :>json string encoding: String encoding when communicating with the R server.
   :>json strings tags: Array of strings, used to identify to what is the R server cluster.
   :>json boolean running: Whether the R server is running or not.
   :>json integer sessions.total: Number of R sessions (active of waiting).
   :>json integer sessions.busy: Number of active R sessions (i.e. an R expression is being executed).
   :>json integer system.cores: Number of host's system cores. If the number of cores cannot be retrieved (when R server is stopped for instance), ``-1`` is returned.
   :>json integer system.freeMemory: Available host's system memory in KB. If the available memory cannot be retrieved (when R server is stopped for instance), ``-1`` is returned.

   :reqheader Accept: ``*/*``
   :resheader Content-Type: ``application/json``
   :statuscode 200: Server is alive and functional
   :statuscode 500: Server is alive but not functional

Start
-----

.. http:put:: /rserver

  Start the R server. Ignored if the R server is already running.

  This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user administrator:password -X PUT https://rock-demo.obiba.org/rserver

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
     rockr.start(conn)

Stop
----

.. http:delete:: /rserver

  Stop the R server. Ignored if the R server is not running.

  This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user administrator:password -X DELETE https://rock-demo.obiba.org/rserver

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
     rockr.stop(conn)
