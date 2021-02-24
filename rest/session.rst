R Session
=========

These resources apply to a single R session living in the R server. Only R session owner or a user with ``administrator`` role can operate.

Status
------

.. http:get:: /r/session/(string:id)

   Get the R session status.

   This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` or ``manager`` role will be able to get other users session. Regular users can only get own R session.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user user:password https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "810cfda6-d0f5-472e-8796-0ce6905499d8",
        "subject": "user",
        "busy": false,
        "createdDate": "2021-02-24 09:11:08",
        "lastAccessDate": "2021-02-24 09:11:15"
      }

   :>json string id: R session unique ID.
   :>json string subject: User name owning the R session.
   :>json boolean busy: Whether an R operation is being executed.
   :>json date createdDate: Date of creation.
   :>json date lastAccessDate: Last time the R session was accessed, used to garbage collect sessions after some timeout.

   :reqheader Authorization: As described in the :ref:`rest-auth` section
   :reqheader Accept: ``*/*``
   :resheader Content-Type: ``application/json``
   :statuscode 200: Server is alive and functional.
   :statuscode 401: User is not authenticated.
   :statuscode 403: User does not have the appropriate role or permission for this operation.
   :statuscode 404: Session could not be found.
   :statuscode 500: Session could not be accessed.

Remove
------

.. http:delete:: /r/session/(string:id)

   Terminate the R session.

   This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` or ``manager`` role will be able to remove other users session. Regular users can only remove own R session.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password -X DELETE https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.open(conn)
      rockr.close(conn)

   :reqheader Authorization: As described in the :ref:`rest-auth` section
   :statuscode 204: Operation was completed. It could have failed silently.
   :statuscode 401: User is not authenticated.
   :statuscode 403: User does not have the appropriate role or permission for this operation.
   :statuscode 404: Session could not be found.
   :statuscode 500: An error occurred.

Assign
------

.. http:post:: /r/session/(string:id)/_assign?s=(string:symbol)

  Assign an R expression to `symbol`. The R expression is the body of the request. The R expression can be the string representation of the data or a function call.

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user user:password -H "Content-Type: application/x-rscript" --data "getwd()" https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/_assign?s=x

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
     rockr.open(conn)
     rockr.assign(conn, "x", quote(getwd()))
     rockr.assign(conn, "n", 123)
     rockr.assign(conn, "str", "abc")

  :>json string s: The R symbol name to assign.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Content-Type: ``application/x-rscript``
  :statuscode 200: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session could not be found.
  :statuscode 500: An error occurred.

Evaluate
--------

.. http:post:: /r/session/(string:id)/_eval

  Evaluate an R expression. The R expression is the body of the request. The R expression can be the string representation of the data or a function call. The returned value can be a primitive type or JSON array/object. In the latter case, make sure that the R expression call returns a value that can be serialized using `jsonlite::toJSON() <https://www.rdocumentation.org/packages/jsonlite/versions/1.7.2/topics/toJSON%2C%20fromJSON>`_. If `toJSON()` fails, instead of raising an error, Rock will fallback to `jsonlite::serializeJSON() <https://www.rdocumentation.org/packages/jsonlite/versions/1.7.2/topics/serializeJSON>`_ which is more robust (and also quite verbose).

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user user:password -H "Content-Type: application/x-rscript" --data "getwd()" https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/_eval
     # note: R.version value cannot be stringified in JSON as-is
     curl --user user:password -H "Content-Type: application/x-rscript" --data "as.list(unlist(R.version))" https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/_eval

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
     rockr.open(conn)
     rockr.eval(conn, quote(R.version))

  **Example response**

  .. sourcecode:: http

     HTTP/1.1 200 OK
     Content-Type: application/json

     {
       "platform": "x86_64-pc-linux-gnu",
       "arch": "x86_64",
       "os": "linux-gnu",
       "system": "x86_64, linux-gnu",
       "status": "",
       "major": "4",
       "minor": "0.4",
       "year": "2021",
       "month": "02",
       "day": "15",
       "svn rev": "80002",
       "language": "R",
       "version.string": "R version 4.0.4 (2021-02-15)",
       "nickname": "Lost Library Book"
     }

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Content-Type: ``application/x-rscript``
  :reqheader Accept: ``*/*``
  :resheader Content-Type: ``application/json``
  :statuscode 200: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session could not be found.
  :statuscode 500: An error occurred.


Files
-----

These resources are for exchanging files between the client and an R session's workspace.

Upload
~~~~~~

Download
~~~~~~~~

Commands
--------

These resources are for managing the R operations that are executed asynchronously in an R session.

List
~~~~

.. _remove-cmd:

Remove
~~~~~~

Result
~~~~~~
