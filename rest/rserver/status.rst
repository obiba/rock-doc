R Server Status
===============

.. http:get:: /rserver

   Get the server detailed status, including R server and host's system information.

   This entry point requires authentication of a user with ``administrator`` or ``manager`` role.

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
        "rSessions": {
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
   :>json integer rSessions.total: Number of R sessions.
   :>json integer rSessions.busy: Number of active R sessions (i.e. a R expression is being executed).
   :>json integer system.cores: Number of host's system cores.
   :>json integer system.freeMemory: Available host's system memory.

   :reqheader Accept: ``*/*``
   :resheader Content-Type: ``application/json``
   :statuscode 200: Server is alive and functional
   :statuscode 500: Server is alive but not functional
