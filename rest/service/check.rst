Service Check
=============

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
