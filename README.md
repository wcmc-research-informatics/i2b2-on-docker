# I2B2 on Docker

This is a program which supports deploying the i2b2 1.7.12 platform using Docker containers. This includes containers for php, apache, and wildfly but no containers for a database. This is intended for use by institutions with a mature i2b2 installation that want to migrate their web client installation to the cloud without disturbing their database. Thus, it is assumed that there exists an i2b2 database connected to another i2b2 web client.

This program does not come packaged with the wildfly or web client components. Grab these from the i2b2 website. Add two folders called public_html/ to the apache and php folders and copy the web client components into that folder. The web client code has to be in both folders for now. The i2b2 WAR file as well as the connector JARs should be placed under wildfly/deployments.

This program was tested on an Amazon Linux 2 EC2 t2.micro instance connecting to an MSSQL instance.

## Versions

These are the versions of the software last tested with the stable release.

* PHP 7.2
* Apache 2.4
* Wildfly 17

## Setup

The top level design of this project should contain the following folders and files:

### apache
apache contains one Dockerfile, a public_html/ directory to hold the web client code, and two apache config files. Web client code is not included, but the i2b2_config_data.js and the index.php file may need configuring depending on your site setup. apache.conf contains the virtualhost information for port 80. Apache listens on ports 80ana.

### php
php contains one Dockerfile and a directory called public_html/ to hold the web client code. This container is proxied requests from the apache container on port 9000, but still requires a copy of the web client code to run properly. Web client code is not included, but the i2b2_config_data.js and the index.php file may need configuring depending on your site setup.

### wildfly
wildfly comes with a Dockerfile, a deployments/ directory, and a custom standalone-i2b2.xml file. The deployments directory should contain the i2b2.war file, the *-ds.xml connector files, and the ODBC connector of your choice. The "custom" standalone-i2b2.xml file is exactly the same as the one that comes packaged with Wildfly 17, but is provided for debugging purposes. If you are researching an error caused by the package or i2b2, and you expect the errors to be trapped by Wildfly but they are not, change the file and root loggers in the XML file to DEBUG level and rebuild the image. Wildfly listens on port 9090.

### docker-compose.yml
the file which configures everything. Requires no adjustments.

### Installation

Install and configure the dependencies in the deployments/*-ds.xml files, BOTH instances of public_html/webclient/i2b2_config_data.js, and BOTH instances of public_html/webclient/index.html. If you are planning on enabling HTTPS (as you should), adjust the index.html file(s) to add the HTTP version of the web client to the white list (or you will receive errors upon logging in). If you are planning on enabling HTTPS, then adjust the Apache configuration files in the apache folder to suit your sites needs. The changes here are just adjusting ServerName and any Apache redirect protocols if the defaults do not suit your needs.

Next, adjust the column URL in the table [i2b2pm].[dbo].[PM_CELL_DATA] to point to the HTTP versions of the various i2b2 web services (even if using HTTPS). This will unhook the database from the old web client and point it to this web client.

Perform any other database maintenance operations you desire at this point. If you are running an older version of i2b2 (say v. 1.7.11), you will need to run the update scripts against the database that are provided with the i2b2 download. If you neglect to do this, you will experience errors upon logging in that sometimes require you to drop to the Wildfly DEBUG level to resolve. 

Then issue docker-compose up.

```sh
$ docker-compose up --build
```

### Development

Want to contribute? Great! Shoot me a message at mzd2016@med.cornell.edu and we'll see what we can do.

### Todos

 - Better and more thorough Apache redirects
 - Support for trusted certificates
 - Test against PostgreSQL, Oracle databases



