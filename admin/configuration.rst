Configuration
=============

The file **ROCK_HOME/conf/application.yml** is to be edited to match your server needs. This file is written in YAML format allowing to specify a hierarchy within the configuration keys. The YAML format uses indentations to express the different levels of this hierarchy. The file is already pre-filled with default values (to be modified to match your configuration), just be aware that you should not modify the indentations. In the following documentation, the configuration keys will be presented using the dot-notation (levels are separated by dots) for readability.

After this configuration file has been updated, restart the application so that new settings are effective.

HTTP Server Configuration
-------------------------

Rock server is a web application and as such, you need to specify on which ports the web server should listen to incoming requests.

========================= ==================
Property                  Description
========================= ==================
``server.port``           HTTP port number. Generally speaking this port should not be exposed to the web. Use proxy instead (Apache2, Nginx, etc.).
========================= ==================

Details about the ``server.*`` properties can be found in the official `Spring Boot - Server Properties <https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-application-properties.html#common-application-properties-server>`_ documentation. More specifically, the ``server.ssl.*`` properties allows to setup a HTTPS communication channel (default is HTTP). Note also that Rock uses Jetty as the internal web server.

Cluster Node Configuration
--------------------------

Rock server identification, required when clusters are built.

========================= ==================
Property                  Description
========================= ==================
``node.id``               Rock server unique identifier, unique in the cluster.
``node.cluster``          Name of the cluster to which the R server belongs. Default is "default".
``node.tags``             Comma separated tag names, for informative purpose.
``node.server``           Public URL of this R server, required for self-registration only.
========================= ==================

.. _consul-config:

Consul Configuration
~~~~~~~~~~~~~~~~~~~~

`Consul <https://www.consul.io/>`_ provides a service discovery and health checking infrastructure. It can be used to make Rock servers available to thrid-party applications. These settings allow Rock to self-register itself against a Consul server.

========================= ==================
Property                  Description
========================= ==================
``consul.server``         Consul server URL.
``consul.token``          Consul registration token (highly recommended in production)
``consul.interval``       Rock service interval check in seconds.
========================= ==================

.. _opal-config:

Opal Configuration
~~~~~~~~~~~~~~~~~~

`Opal <http://www.obiba.org/pages/products/opal/>`_ has an internal App registry to which Rock can self-register itself.

========================= ==================
Property                  Description
========================= ==================
``opal.server``           Opal server URL.
``opal.token``            Opal app registration token (required).
========================= ==================

R Server Configuration
----------------------

R environment properties.

========================= ==================
Property                  Description
========================= ==================
``r.exec``                File path to the R executable.
``r.repos``               Comma separated list of URLs to R CRAN repositories, to install packages.
``r.sessionTimeout``      Managed R sessions timeout, in minutes. Any R sessions without activity during this laps of time will be removed automatically. Default is ``240`` (4 hours). If the value is <0 no timeout action is applied.
========================= ==================

Users Configuration
-------------------

========================= ==================
Property                  Description
========================= ==================
``security.users``        List of users, with attributes ``id`` (user name), ``secret`` (user password) and ``roles`` (comma separated list of role names: ``administrator``, ``manager`` or ``user``). See `default users for syntax example <https://github.com/obiba/rock/blob/master/src/dist/conf/application.yml#L36-L49>`_.
========================= ==================

.. note::

  When no valid user is defined, the default user setup is applied: user ``administrator`` with password ``password`` (with administrator role).

The user passwords can be hashed, using the `Bcrypt <https://en.wikipedia.org/wiki/Bcrypt>`_ algorithm. In this case, the hashed password will be preceded by the prefix **{bcrypt}**. Make sure also to quote the **secret** value so that the $ signs do not get interpreted. The are many bcrypt hashers available online, for instance `bcrypt.online <https://bcrypt.online/>`_. As an example:

.. code-block:: yaml

  security:
    users:
      # administrator, can do all
      - id: administrator
        secret: "{bcrypt}$2y$10$Ds/CB6jlY5a4/NU4.RvRI.9oZ16Bp6hx/Xcct1c2XFwYRdMbTHJVu"
        roles: administrator


.. _config-apparmor:

AppArmor Configuration
----------------------

Rock can apply an `RAppArmor <https://cran.r-project.org/package=RAppArmor>`_ profile on R session creation. Requires RAppArmor to be properly installed and configured.

=============================== ==================
Property                        Description
=============================== ==================
``security.apparmor.enabled``   Enable/disable the RAppArmor functionality.
``security.apparmor.profile``   The name the AppArmor profile to apply on R session creation.
``security.apparmor.strict``    Whether the AppArmor profile is to be applied to the administrator as well. Default is ``true``.
=============================== ==================
