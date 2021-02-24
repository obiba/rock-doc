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

.. _assign:

Assign
------

.. http:post:: /r/session/(string:id)/_assign?s=(string:symbol)[&async=(boolean:async)]

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

  **Example response**

  When `async` parameter is ``true``, a `Command` is created and put in the execution queue.

  .. sourcecode:: http

    HTTP/1.1 201 Created
    Location: https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/command/605fc0a4-4e41-40eb-bf40-89c2d3bb17fa-3

    {
      "id": "605fc0a4-4e41-40eb-bf40-89c2d3bb17fa-3",
      "status": "IN_PROGRESS",
      "finished": false,
      "createdDate": "2021-02-24T17:38:14.929+00:00",
      "startDate": "2021-02-24T17:38:14.929+00:00",
      "endDate": "2021-02-24T17:38:14.930+00:00",
      "withError": false,
      "withResult": false,
      "script": "base::assign('x', getwd())"
    }

  :query string s: The R symbol name to assign.
  :query boolean async: Whether the R operation is to be put in a command queue for latter execution, in which case a `Command` object will be returned (see :ref:`commands`). Default is ``false``.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Content-Type: ``application/x-rscript``
  :statuscode 200: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session could not be found.
  :statuscode 500: An error occurred.

.. _eval:

Evaluate
--------

.. http:post:: /r/session/(string:id)/_eval[?async=(boolean:async)]

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

  :query boolean async: Whether the R operation is to be put in a command queue for latter execution, in which case a `Command` object will be returned (see :ref:`commands`) in place of the evaluation result. Default is ``false``.

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

.. http:post:: /r/session/(string:id)/_upload?[path=(string:path)][&overwrite=(boolean:overwrite)][&temp=(boolean:temp)]

  Upload a file at `path`. If `path` is not specified, the uploaded file name will be used. Note that the `path` root folder is ever the R session original working directory or its temporary directory (if `temp` is ``true``). This means that any attempt to upload a file outside of the R session file scope will fail.

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

    curl --user user:password -F "file=@some/local/file.ext" https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/_upload?overwrite=true

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

     library(rockr)
     conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
     rockr.open(conn)
     rockr.file_upload(conn, source = "some/local/file.ext", overwrite = TRUE)

  :query string path: The destination path relative to the root directory (defined by the `temp` parameter). Any subfolders will be created automatically. If this parameter is missing, the uploaded file name will be used.
  :query boolean overwrite: Whether to overwrite the destination file if it already exists. Default is ``false``.
  :query boolean temp: Whether the root directory is the temporary folder of the R session, otherwise it will be the original working directory. Default is ``false``.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Content-Type: ``multipart/form-data``
  :statuscode 200: Operation was completed.
  :statuscode 400: If destination file is a folder, exists and cannot be overridden or more generally if it is not valid (attempt to write a file outside of the R session root directory).
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session could not be found.
  :statuscode 500: An error occurred.


Download
~~~~~~~~

.. http:get:: /r/session/(string:id)/_download?path=(string:path)[&temp=(boolean:temp)]

  Download a file located at `path` in the R session root directory. This root directory is ever the R session original working directory or its temporary directory (if `temp` is ``true``). This means that any attempt to download a file from outside of the R session file scope will fail.

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

    curl --user user:password https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/_upload?path=some%2Fremote%2Ffile.ext -o file.ext

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

    library(rockr)
    conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
    rockr.open(conn)
    rockr.file_download(conn, source = "some/remote/file.ext", destination = "file.ext")

  :query string path: The source path relative to the root directory (defined by the `temp` parameter).
  :query boolean temp: Whether the root directory is the temporary folder of the R session, otherwise it will be the original working directory. Default is ``false``.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Accept: ``*/*``
  :statuscode 200: Operation was completed.
  :statuscode 400: If file does not exists, is a folder or more generally if it is not valid (attempt to access a file outside of the R session root directory).
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session could not be found.
  :statuscode 500: An error occurred.

.. _commands:

Commands
--------

These resources are for managing the R operations that are executed asynchronously in an R session. See :ref:`assign` and :ref:`eval` requests that propose an `async` query parameter to put the R operation in the execution queue.

List
~~~~

.. http:get:: /r/session/(string:id)/commands

  List the R commands that are either in the processing queue or in the result list.

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

     curl --user user:password https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/commands

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

    library(rockr)
    conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
    rockr.open(conn)
    rockr.eval(conn, quote(R.version), async = TRUE)
    rockr.assign(conn, "x", quote(getwd()), async = TRUE)
    rockr.commands(conn)

  **Example response**

  .. sourcecode:: http

    HTTP/1.1 200 OK

    [
      {
        "id": "810cfda6-d0f5-472e-8796-0ce6905499d8-1",
        "status": "COMPLETED",
        "finished": true,
        "createdDate": "2021-02-24T17:55:59.133+00:00",
        "startDate": "2021-02-24T17:55:59.133+00:00",
        "endDate": "2021-02-24T17:55:59.164+00:00",
        "withError": false,
        "withResult": true,
        "script": "R.version"
      },
      {
        "id": "810cfda6-d0f5-472e-8796-0ce6905499d8-2",
        "status": "COMPLETED",
        "finished": true,
        "createdDate": "2021-02-24T17:57:03.129+00:00",
        "startDate": "2021-02-24T17:57:03.130+00:00",
        "endDate": "2021-02-24T17:57:03.131+00:00",
        "withError": false,
        "withResult": false,
        "script": "base::assign('x', getwd())"
      }
    ]

  :>jsonarr string id: Command unique ID.
  :>jsonarr string status: The status is one of: ``PENDING`` (command is in the execution queue), ``IN_PROGRESS`` (command execution is in progress), ``COMPLETED`` (completion with success) or ``FAILED`` (completion with failure).
  :>jsonarr boolean finished: Whether the command is completed (successfully or not).
  :>jsonarr string createdDate: Date of command submission.
  :>jsonarr string startDate: Date of the command execution start.
  :>jsonarr string endDate: Date of the command execution completion.
  :>jsonarr boolean withError: Whether failed completion has an error message.
  :>jsonarr string error: Error message.
  :>jsonarr boolean withResult: Whether completed command has a result. See :ref:`result-cmd`.
  :>jsonarr string script: R script associated to the command.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Accept: ``*/*``
  :resheader Content-Type: ``application/json``
  :statuscode 200: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session could not be found.
  :statuscode 500: An error occurred.

.. _status-cmd:

Status
~~~~~~

.. http:get:: /r/session/(string:id)/command/(string:cmd_id)

  Get the status of a command in its R session.

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

    curl --user user:password https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/command/810cfda6-d0f5-472e-8796-0ce6905499d8-1

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

    library(rockr)
    conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
    rockr.open(conn)
    cmd <- rockr.eval(conn, quote(R.version), async = TRUE)
    rockr.command(conn, cmd$id)

  **Example response**

  .. sourcecode:: http

    HTTP/1.1 200 OK

    {
      "id": "810cfda6-d0f5-472e-8796-0ce6905499d8-1",
      "status": "COMPLETED",
      "finished": true,
      "createdDate": "2021-02-24T17:55:59.133+00:00",
      "startDate": "2021-02-24T17:55:59.133+00:00",
      "endDate": "2021-02-24T17:55:59.164+00:00",
      "withError": false,
      "withResult": true,
      "script": "R.version"
    }

  :>json string id: Command unique ID.
  :>json string status: The status is one of: ``PENDING`` (command is in the execution queue), ``IN_PROGRESS`` (command execution is in progress), ``COMPLETED`` (completion with success) or ``FAILED`` (completion with failure).
  :>json boolean finished: Whether the command is completed (successfully or not).
  :>json string createdDate: Date of command submission.
  :>json string startDate: Date of the command execution start.
  :>json string endDate: Date of the command execution completion.
  :>json boolean withError: Whether failed completion has an error message.
  :>json string error: Error message.
  :>json boolean withResult: Whether completed command has a result. See :ref:`result-cmd`.
  :>json string script: R script associated to the command.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Accept: ``*/*``
  :resheader Content-Type: ``application/json``
  :statuscode 200: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session or command could not be found.
  :statuscode 500: An error occurred.

.. _remove-cmd:

Remove
~~~~~~

.. http:delete:: /r/session/(string:id)/command/(string:cmd_id)

  Remove a command, before or after it has been executed.

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

    curl --user user:password -X DELETE https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/command/810cfda6-d0f5-472e-8796-0ce6905499d8-1

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

    library(rockr)
    conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
    rockr.open(conn)
    cmd <- rockr.eval(conn, quote(R.version), async = TRUE)
    rockr.command_rm(conn, cmd$id)

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :statuscode 204: Operation was completed.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session or command could not be found.
  :statuscode 500: An error occurred.

.. _result-cmd:

Result
~~~~~~

.. http:get:: /r/session/(string:id)/command/(string:cmd_id)/result[?rm=(boolean:remove)][&wait=(boolean:wait)]

  Extract the result from a completed, successful, command having some result data.

  This entry point requires :ref:`rest-auth` of a user. Users with ``administrator`` role will be able to use other users session. Regular users can only use own R session.

  **Example requests**

  Using cURL

  .. sourcecode:: shell

    curl --user user:password https://rock-demo.obiba.org/r/session/810cfda6-d0f5-472e-8796-0ce6905499d8/command/810cfda6-d0f5-472e-8796-0ce6905499d8-1/result?wait=true

  Using R (`rockr <https://github.com/obiba/rockr>`_)

  .. sourcecode:: r

    library(rockr)
    conn <- rockr.connect(username="user", password="password", url = "https://rock-demo.obiba.org")
    rockr.open(conn)
    cmd <- rockr.eval(conn, quote(R.version), async = TRUE)
    rockr.command_result(conn, cmd$id, wait = TRUE)

  :query boolean rm: Remove the command from the result list after the result download. Default is ``true``.
  :query boolean wait: Whether the command completion should be waited in a blocking way. Default is ``false``.

  :reqheader Authorization: As described in the :ref:`rest-auth` section
  :reqheader Accept: ``*/*``
  :resheader Content-Type: ``application/json``
  :statuscode 200: Operation was completed.
  :statuscode 204: Empty response when command is not completed yet and `wait` parameter is ``false``.
  :statuscode 401: User is not authenticated.
  :statuscode 403: User does not have the appropriate role or permission for this operation.
  :statuscode 404: Session or command could not be found.
  :statuscode 500: An error occurred.
