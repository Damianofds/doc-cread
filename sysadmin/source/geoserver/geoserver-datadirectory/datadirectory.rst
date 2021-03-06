.. _datadirectory:

########################
GeoServer Data Directory
########################

The GeoServer data directory is the location in the file system where GeoServer stores its configuration information. The configuration defines things such as what data is served by GeoServer, where it is stored, and how services such as WFS and WMS interact with and serve the data. The data directory also contains a number of support files used by GeoServer for various purposes.

=============================
Creating a New Data Directory
=============================
The easiest way to create a new data directory is to copy one that comes with a standard GeoServer installation.

If GeoServer is running in Standalone mode the data directory is located at <installation root>/data_dir.
If GeoServer is running as Web Archive mode inside of a servlet container, the data directory is located at <web application root>/data. In a linux enviroment an example location is:

.. code:: bash

   /var/lib/tomcat7/webapps/geoserver/data

==========================
Setting the Data Directory
==========================
On Linux platforms the location of the GeoServer data directory is controlled by the GEOSERVER_DATA_DIR environment variable. Setting the variable can be achieved with the following command (in a bash shell):

.. code:: bash

         % export GEOSERVER_DATA_DIR=/var/lib/geoserver_data

Place the command in the .bash_profile or .bashrc file (again assuming a bash shell). Ensure that this done for the user running GeoServer.

===============================
Structure of the Data Directory
===============================

This section gives an overview of the structure and contents of the GeoServer data directory.
The directories that do contain user-modifiable content are: **logs**, **palettes**, **templates**, **user-projection**, and **www**.

The following figure shows the structure of the GeoServer data directory:

.. code::
 
    <data_directory>/

      global.xml
      logging.xml
      wms.xml
      wfs.xml
      wcs.xml

      data/
      demo/
      geosearch/
      gwc/
      layergroups/
      logs/
      palettes/
      plugIns/
      security/
      styles/
      templates/
      user_projections/
      workspaces/
        |
        +- workspace dirs...
           |
           +- datastore dirs...
              |
              +- layer dirs...
      www/

The .xml files
--------------

The top-level .xml files contain information about the services and various global options for the server instance.

=========== ================================================================================================================== 
File        Description
=========== ==================================================================================================================
global.xml  Contains settings common to all services, such as contact information, JAI settings, character sets and verbosity.
logging.xml Specifies logging parameters, such as logging level, logfile location, and whether to log to stdout.
wcs.xml	    Contains the service metadata and various settings for the WCS service.
wfs.xml	    Contains the service metadata and various settings for the WFS service.
wms.xml	    Contains the service metadata and various settings for the WMS service.
=========== ==================================================================================================================

workspaces
----------

The workspaces directory contain metadata about the layers published by GeoServer. It contains a directory for each defined workspace. Each workspace directory contains directories for the datastores defined in it. Each datastore directory contains directories for the layers defined for the datastore. Each layer directory contains a layer.xml file, and either a coverage.xml or a featuretype.xml file depending on whether the layer represents a raster or vector dataset.

data
----

The data directory can be used to store file-based geospatial datasets being served as layers. (This should not be confused with the main “GeoServer data directory”.) This directory is commonly used to store shapefiles and raster files, but can be used for any data that is file-based.

The main benefit of storing data files under the data directory is portability. Consider a shapefile stored external to the data directory at a location C:\gis_data\foo.shp. The datastore entry in catalog.xml for this shapefile would look like the following:

.. code:: xml

    <datastore id="foo_shapefile">
       <connectionParams>
	 <parameter name="url" value="file://C:/gis_data/foo.shp" />
       </connectionParams>
    </datastore>

Now consider trying to port this data directory to another host running GeoServer. The location `C:\gis_data\foo.shp` probably does not exist on the second host. So either the file must be copied to this location on the new host, or catalog.xml must be changed to reflect a new location.
This problem can be avoided by storing foo.shp in the data directory. In this case the datastore entry in catalog.xml becomes:

.. code:: xml

    <datastore id="foo_shapefile">
      <connectionParams>
	<parameter name="url" value="file:data/foo.shp"/>
      </connectionParams>
    </datastore>

The value attribute is rewritten to be relative to the data directory. This location independence allows the entire data directory to be copied to a new host and used directly with no additional changes.

demo
----

The demo directory contains files which define the sample requests available in the Sample Request Tool (http://localhost:8080/geoserver/demoRequest.do).

geosearch
---------

The geosearch directory contains information for regionation of KML files.

gwc
---

The gwc directory holds the cache created by the embedded GeoWebCache service.

layergroups
-----------

The layergroups directory contains configuration information for the defined layergroups.

logs
----

The logs directory contains configuration information for logging profiles, and the default geoserver.log log file.

palettes
--------

The palettes directory is used to store pre-computed Image Palettes. Image palettes are used by the GeoServer WMS as way to reduce the size of produced images while maintaining image quality.

security
--------

The security directory contains the files used to configure the GeoServer security subsystem. This includes a set of property files which define access roles, along with the services and data each role is authorized to access.

styles
------

The styles directory contains Styled Layer Descriptor (SLD) files which contain styling information used by the GeoServer WMS. For each file in this directory there is a corresponding entry in catalog.xml:

.. code:: xml

    <style id="point_style" file="default_point.sld"/>


templates
---------

The templates directory contains files used by the GeoServer templating subsystem. Templates are used to customize the output of various GeoServer operations. See also Freemarker Templates.

user_projections
----------------

The user_projections directory contains a file called epsg.properties which is used to define custom spatial reference systems that are not part of the official EPSG database. See also Custom CRS Definitions.

www
---

The www directory is used to allow GeoServer to serve files like a regular web server. The contents of this directory are served at http:/<host:port>/geoserver/www. While not a replacement for a full blown web server, this can be useful for serving client-side mapping applications. 
