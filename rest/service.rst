Service Resources
=================

These resources are public and are mainly for service discovery operations (identification and availability checks).

Information
-----------

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

Check
-----

.. http:get:: /_check

  Get whether the server is functional, to be used for periodic service availability checks. No content is returned, only the response status is meaningful: ``200 OK`` is positive, whereas any other response indicates a problem.

  This entry point does not require any authentication.

  **Example request**:

  .. sourcecode:: shell

     curl https://rock-demo.obiba.org/_check

  **Example response**:

  .. sourcecode:: http

     HTTP/1.1 200 OK

  :statuscode 200: Server is alive and functional
  :statuscode 500: Server is alive but not functional
