Installation
============

Rock is a stand-alone `Java <https://www.java.com>`_ server application. The R computation engine is handled by `Rserve <http://www.rforge.net/Rserve/>`_ and therefore requires a working R environment.

Requirements
------------

Server Hardware Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

============ ===============
Component    Requirement
============ ===============
CPU	         Recent server-grade or high-end consumer-grade processor
Disk space	 8GB or more.
Memory (RAM) Minimum: 4GB, Recommended: >4GB
============ ===============

Server Software Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

======== ================= ========================================================== ========================
Software Version           Download link                                              Usage
======== ================= ========================================================== ========================
Java     21                `OpenJDK downloads <https://jdk.java.net/>`_               Java runtime environment
R        any version       `R project <https://www.r-project.org/>`_                  R server engine
======== ================= ========================================================== ========================

Both Java and R must be installed on the same host.

Install
-------

Rock is distributed as a Debian/RPM package, as a zip file and as a Docker image. The resulting installation has default configuration that makes Rock ready to be used (as soon as R is available). Once installation is done, see :doc:`configuration` instructions.

Debian Package Installation
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Rock is available as a Debian package from OBiBa Debian repository. To proceed installation, do as follows:

* `Install Debian package <http://www.obiba.org/pages/pkg/>`_. Follow the instructions in the repository main page for installing Rock.
* Manage Rock Service: after package installation, Rock server is running: see how to manage the Service.

RPM Package Installation
~~~~~~~~~~~~~~~~~~~~~~~~

Rock is available as a RPM package from OBiBa RPM repository. To proceed installation, do as follows:

* `Install RPM package <http://www.obiba.org/pages/rpm/>`_. Follow the instructions in the RPM repository main page for installing Rock.
* Manage Rock Service: after package installation, Rock is running: see how to manage the Service.

Zip Distribution Installation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Rock is also available as a Zip file. To install Rock zip distribution, proceed as follows:

* `Download Rock distribution <https://github.com/obiba/rock/releases>`_
* Unzip the Rock distribution. Note that the zip file contains a root directory named **rock-x.y.z-dist** (where x, y and z are the major, minor and micro releases, respectively). You can copy it wherever you want. You can also rename it.
* Create an ``ROCK_HOME`` environment variable
* Separate Rock home from Rock distribution directories (recommended). This will facilitate subsequent upgrades.

Set-up example for Linux:

.. code-block:: bash

  mkdir rock-home
  cp -r rock-x-dist/conf rock-home
  export ROCK_HOME=`pwd`/rock-home
  ./rock-x-dist/bin/rock

Launch Rock. This step will create/update the database schema for Rock and will start Rock: see Regular Command.

For the administrator accounts, the credentials are "administrator" as username and "password" as password. See User Directories Configuration to change it.

Docker Image Installation
~~~~~~~~~~~~~~~~~~~~~~~~~

OBiBa is an early adopter of the `Docker <https://www.docker.com/>`_ technology, providing its own images from the `Docker Hub repository <https://hub.docker.com/orgs/obiba/repositories>`_.

See also the Rock docker images that are DataSHIELD ready: `datashield/rock-base <https://hub.docker.com/r/datashield/rock-base>`_

A typical `docker-compose <https://docs.docker.com/compose/>`_ file would be:

.. code-block:: yaml

  version: '3'
  services:
    rock:
      image: obiba/rock:latest
      ports:
        - ${PORT}:8085
      environment:
        - ROCK_ADMINISTRATOR_NAME=${ROCK_ADMINISTRATOR_NAME}
        - ROCK_ADMINISTRATOR_PASSWORD=${ROCK_ADMINISTRATOR_PASSWORD}
        - ROCK_MANAGER_NAME=${ROCK_MANAGER_NAME}
        - ROCK_MANAGER_PASSWORD=${ROCK_MANAGER_PASSWORD}
        - ROCK_USER_NAME=${ROCK_USER_NAME}
        - ROCK_USER_PASSWORD=${ROCK_USER_PASSWORD}
        - ROCK_ID=${ROCK_ID}
        - ROCK_CLUSTER=${ROCK_CLUSTER}
        - ROCK_TAGS=${ROCK_TAGS}
        # for self-registration
        #- ROCK_SERVER=${ROCK_SERVER}
        #- ROCK_OPAL_SERVER=${ROCK_OPAL_SERVER}
        #- ROCK_OPAL_TOKEN=${ROCK_OPAL_TOKEN}
      volumes:
        - ${PROJECT_HOME}/rock_home:/srv

Then environment variables that are exposed by this image are:

================================= =========================================================================
Environment Variable              Description
================================= =========================================================================
``JAVA_OPTS``                     JVM options.
``ROCK_ADMINISTRATOR_NAME``       Administrator user name, optional and set at first start.
``ROCK_ADMINISTRATOR_PASSWORD``   Administrator user password, optional and set at first start.
``ROCK_MANAGER_NAME``             Manager user name, optional and set at first start.
``ROCK_MANAGER_PASSWORD``         Manager user password, optional and set at first start.
``ROCK_USER_NAME``                Regular user name, optional and set at first start.
``ROCK_USER_PASSWORD``            Regular user password, optional and set at first start.
``ROCK_ID``                       Rock node ID. Make sure it is unique in the cluster.
``ROCK_CLUSTER``                  Cluster of R servers name. Default is “default”.
``ROCK_TAGS``                     Comma separated tag names, optional. Default tag list is empty.
``ROCK_SERVER``                   Self-registration: Rock server public address that will be sent to service registries (including Opal).
``ROCK_OPAL_SERVER``              Self-registration: Opal server address to register to.
``ROCK_OPAL_TOKEN``               Self-registration: `Opal's app self-registration token <https://opaldoc.obiba.org/en/latest/web-user-guide/administration/apps.html>`_.
``ROCK_SECURITY_ENABLED``         Enable or disable security features. Default is “false”.
================================= =========================================================================

.. note::

  When no valid user is defined, the default user setup is applied: user ``administrator`` with password ``password`` (with administrator role).

Upgrade
-------

The upgrade procedures are handled by the application itself.

Debian Package Upgrade
~~~~~~~~~~~~~~~~~~~~~~

If you installed Rock via the Debian package, you may update it using the command:

.. code-block:: bash

  apt-get install rock

RPM Package Upgrade
~~~~~~~~~~~~~~~~~~~

If you installed Rock via the RPM package, you may update it using the command:

.. code-block:: bash

  yum install rock

Zip Distribution Upgrade
~~~~~~~~~~~~~~~~~~~~~~~~

Follow the Installation of Rock Zip distribution above but make sure you don't overwrite your rock-home directory.

Execution
---------

Server launch
~~~~~~~~~~~~~

**Service**

When Rock is installed through a Debian/RPM package, Rock server can be managed as a service.

Options for the Java Virtual Machine can be modified if Rock service needs more memory. To do this, modify the value of the environment variable ``JAVA_ARGS`` in the file **/etc/default/rock**.

Main actions on Rock service are: ``start``, ``stop``, ``status``, ``restart``. For more information about available actions on Rock service, type:

.. code-block:: bash

  service rock help

The Rock service log files are located in **/var/log/rock** directory.

**Manually**

The Rock server can be launched from the command line. The environment variable ``ROCK_HOME`` needs to be setup before launching Rock manually.

==================== ======== ===========
Environment variable Required Description
==================== ======== ===========
``ROCK_HOME``        yes      Path to the Rock "home" directory.
``JAVA_OPTS``        no       Options for the Java Virtual Machine. For example: `-Xmx1024m -XX:MaxPermSize=256m`
==================== ======== ===========

To change the defaults update:  ``bin/rock`` or ``bin/rock.bat``

Make sure Command Environment is setup and execute the command line (bin directory is in your execution PATH)):

.. code-block:: bash

  rock

Executing this command upgrades the Rock server and then launches it.

The Rock server log files are located in **ROCK_HOME/logs** directory. If the logs directory does not exist, it will be created by Rock.

Usage
~~~~~

To access Rock with a web browser the following urls may be used (port numbers may be different depending on HTTP Server Configuration):

* http://localhost:8085 will provide a connection without encryption.

Troubleshooting
~~~~~~~~~~~~~~~

If you encounter an issue during the installation and you can't resolve it, please report it in our `Rock Issue Tracker <https://github.com/obiba/rock/issues>`_.

Rock logs can be found in **/var/log/rock**. If the installation fails, always refer to this log when reporting an error.
