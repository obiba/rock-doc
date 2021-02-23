R Server Start
==============

.. http:put:: /rserver

   Start the R server. Ignored if the R server is already running.

   This entry point requires authentication of a user with ``administrator`` or ``manager`` role.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password -X PUT https://rock-demo.obiba.org/rserver

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.start(conn)
