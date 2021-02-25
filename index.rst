.. Rock documentation master file, created by
   sphinx-quickstart on Mon Apr  9 11:43:58 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

OBiBa Rock Documentation
=========================

Rock is the `OBiBa <http://obiba.org/>`_'s R server which intends to be easy to install and to use. Rock exposes a REST API which can be used by any client application with HTTP communication capability. The R server engine is based on `Rserve <http://www.rforge.net/Rserve/>`_ that Rock uses to manage stateful R sessions.

Targeted at individual studies and study consortia, `OBiBa <http://obiba.org/>`_ software stack (Opal, Mica etc.) provides a software solution for epidemiological data management, analysis and publication. While `Opal <http://www.obiba.org/pages/products/opal/>`_, the core data warehouse application, provides all the necessary tools to import, transform, describe and analyze data, `Mica <http://www.obiba.org/pages/products/mica/>`_ provides everything needed to build personalized web data portals and publish content of research activities of both studies and consortia.

`Opal <http://www.obiba.org/pages/products/opal/>`_ uses Rock to constitute clusters of R servers and to dispatch the analysis requests on multiple hosts.

.. toctree::
   :maxdepth: 2

   introduction

.. toctree::
   :maxdepth: 2
   :caption: Administrator Guide

   admin/installation
   admin/configuration


.. toctree::
   :maxdepth: 2
   :caption: REST API Guide

   rest/index
   rest/service
   rest/rserver
   rest/packages
   rest/package
   rest/sessions
   rest/session

Partners and Funders
====================

The development of this application was made possible thanks to the support of our partners and funders:

.. |mr-logo| image:: https://www.obiba.org/assets/themes/bootstrap/img/logo-maelstrom.png
 :height: 60px
 :target: https://www.maelstrom-research.org/

.. |ep-logo| image:: https://www.obiba.org/assets/themes/bootstrap/img/epigeny.png
 :height: 40px
 :target: https://www.epigeny.io/

.. |ecc-logo| image:: https://www.obiba.org/assets/themes/bootstrap/img/eucanconnect.png
 :height: 50px
 :target: https://www.eucanconnect.eu/

+------------+------------+------------+
| |mr-logo|  + |ep-logo|  + |ecc-logo| +
+------------+------------+------------+

Support
=======

Please visit `OBiBa support <https://www.obiba.org/pages/support/>`_ page.
