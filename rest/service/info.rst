Service Information
===================

.. http:get:: /_info

   Get the server public information, mainly to allow service discovery.

   This entry point does not require any authentication.

   **Example request**:

   .. sourcecode:: shell

      curl https://rock-demo.obiba.org/_info

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "name": "rock-demo",
        "type": "rock",
        "tags": [
          "default"
        ]
      }

   :reqheader Accept: ``*/*``
   :resheader Content-Type: ``application/json``
   :statuscode 200: Server is alive and functional
   :statuscode 500: Server is alive but not functional
