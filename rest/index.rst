REST API Introduction
=====================

The Rock REST API has different scopes:

* Service discovery

* R server management

  * Status/start/stop

  * R packages management

* R sessions management and usage

  * R operations

  * File management

  * R commands management




.. _rest-auth:

Authentication
--------------

Rock supports only `Basic authentication <https://tools.ietf.org/html/rfc7617>`_, which consists of a HTTP request's header field in the form of ``Authorization: Basic <credentials>``, where credentials is the Base64 encoding of user's (or application's) ID and password joined by a single colon ``:``.

Using cURL, it is as simple as providing the `--user <https://curl.se/docs/manpage.html#-u>`_ option:

.. sourcecode:: shell

   curl --user <id>:<password> [...]

Authorization
-------------

Authorizations are role based. The built-in roles are:

================== ===============
Role               Description
================== ===============
``administrator``  Can do anything.
``manager``        Can only manage the R server: R process and R packages. Creating and using R sessions is not permitted.
``user``           Can only create and use its own R sessions. R operations may be limited by the :ref:`config-apparmor`.
================== ===============
