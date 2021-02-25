R Sessions
==========

These resources manage the stateful R sessions living in the R server. Some operations may be limited depending on the user role.

List
----

.. http:get:: /r/sessions[?subject=(string:user_name)]

   List the R sessions.

   This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` or ``manager`` role will be able to list other users sessions. Regular users can only list own R sessions.

   **Example requests**

   Using cURL

   .. sourcecode:: shell

      curl --user administrator:password https://rock-demo.obiba.org/r/sessions

   Using R (`rockr <https://github.com/obiba/rockr>`_)

   .. sourcecode:: r

      library(rockr)
      conn <- rockr.connect(username="administrator", password="password", url = "https://rock-demo.obiba.org")
      rockr.sessions(conn)

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      [
        {
          "id": "810cfda6-d0f5-472e-8796-0ce6905499d8",
          "subject": "user",
          "busy": false,
          "createdDate": "2021-02-24 09:11:08",
          "lastAccessDate": "2021-02-24 09:11:15"
        }
      ]

   :query string subject: To filter sessions by their subject (owner of the session). Ignored when user does not have ``administrator`` or ``manager`` role.

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
   :statuscode 500: Sessions list could not be retrieved.

Remove
------

.. http:delete:: /r/sessions

  Terminate all the R sessions.

  This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``manager`` role.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user administrator:password -X DELETE https://rock-demo.obiba.org/r/sessions

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :statuscode 204: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role for this operation.
  :statuscode 500: An error occurred.

Create
------

.. http:post:: /r/sessions

  Create a R session, which will be associated to the requesting user.

  This entry point requires :ref:`rest-auth` of a user with ``administrator`` or ``user`` role.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user user:password -X POST https://rock-demo.obiba.org/r/sessions

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
     rockr.open(conn)

  **Example response**

  .. sourcecode:: http

     HTTP/1.1 201 Created
     Content-Type: application/json
     Location: http://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8

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
  :statuscode 204: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role for this operation.
  :statuscode 500: An error occurred.
