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

Evaluate
--------

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
