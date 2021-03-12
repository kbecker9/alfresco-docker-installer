# generator-alfresco-docker-installer
> Alfresco Docker Installer

## DISCLAMER
**IMPORTANT** This project is not supported by Alfresco in any way. Despite deployments using Docker Compose are considered a valid approach for ACS deployment (Community and Enterprise), this `alfresco-docker-installer` tool is **not** the official Alfresco recommendation. Please, check [https://github.com/Alfresco/acs-deployment/tree/master/docker-compose](https://github.com/Alfresco/acs-deployment/tree/master/docker-compose) in order to understand official recommendations from Alfresco.

## Description

Since Alfresco Installer was discontinued from Alfresco 5.2, this project provides a command line installer for Alfresco Community 6.1, 6.2 and 7.0 to be used in Docker Compose installations.

This project generates a Docker Compose template ready to be used including following features:

* RAM limits for every service according to global memory available for Docker
* PostgreSQL or MariaDB as database (no other option but MySQL is supported for Community)
* Search Services configured for environments using several languages for contents or from operative systems / browsers
* Outbound Email service (smtp)
* LDAP service for identification (based in OpenLDAP)
* Several addons available
* Wrapper Script for waiting the alfresco boot to finish

>> This generator creates a base Docker Template with the configuration selected, but you should review volumes, configuration, modules & tuning parameters before using this composition in Production environments.

**WARNING** Depending on the OS used for hosting Docker, some adjustments must be made in default **volumes** configuration:
* For *Mac OS*, produced Docker Compose template should work as is
* For *Windows*, safer approach is to use standard [Docker Volumes](https://docs.docker.com/storage/volumes/) are used instead of [Bind Docker Volumes](https://docs.docker.com/storage/bind-mounts/). This alternative is enabled when choosing "Windows host" option in the generator.
* For *Linux*, some local folder permission must be adjusted if you are not using 'root' to run Docker. Review the [Docker Volumes](https://github.com/Alfresco/alfresco-docker-installer#docker-volumes) section before running the produced Docker Compose template. Also checkout this tutorial with video recording on how to use this tool in Ubuntu 20.04 LTS: [Installing Alfresco 6 Community Edition in Ubuntu](https://hub.alfresco.com/t5/alfresco-content-services-blog/installing-alfresco-6-community-edition-in-ubuntu-using-docker/ba-p/303840)

## Installation

This program has following dependencies:

* Node.js
* Yeoman

You can download and install `Node.js` from official web page:

https://nodejs.org/en/download/

Or you can use any of the package managers provided by the product:

https://nodejs.org/en/download/package-manager/

Once Node.js is installed, you can install [Yeoman](http://yeoman.io) as a module:

```bash
$ npm install -g yo
```

And finally, you can install this generator:

```bash
$ npm install --global generator-alfresco-docker-installer
```

Deployment is provided for Docker Compose, so following dependencies must be satisfied by the server used to run the generated configuration:

* Docker
* Docker Compose

You can install *Docker Desktop* for Windows or Mac and *Docker Server* for Linux.

https://docs.docker.com/install/

You need also to add *Docker Compose* program to your installation.

https://docs.docker.com/compose/install/


## Running

Create a folder where Docker Compose template files are going to be produced and run the generator.

>>> If you downloaded this project, **don't** reuse source code folder. Create an empty folder to generate Docker Compose template anywhere.

```
$ mkdir docker-compose
$ cd docker-compose

$ yo alfresco-docker-installer
```

Several options are provided in order to build the configuration.

```
? Which ACS version do you want to use? 7.0
```

You can use Alfresco 6.1, 6.2 or 7.0

```
? How may GB RAM are available for Alfresco (12 is minimum required)? 12
```

Alfresco platform could work with less than 12 GB RAM, but it's recommended to provide at least 16 GB in your Docker server. This generator will limit the amount of memory for every service in order to match your resources.

```
? Do you want to use HTTPs for Web Proxy?
```

This option enables HTTPs for every service. Default SSL certificates (public and private) are provided in `config/cert` folder. These certificates are not recommended for prod environments, so it's required to replace these files with your own certificates.

```
? What is the name of your server?
```

If you are deploying on a server different than `localhost`, include in this option the name of your server. For instance: `alfresco.com`

```
? What HTTP port do you want to use (all the services are using the same port)? 80 or 443
```

HTTP port to be used by every service. If you are running on a Linux computer, you'll need to specify a port greater than 1024 when not starting as `root` user.

```
? Do you want to use FTP (port 2121)? No
```

Enable configuration for FTP, using by default port 2121.

```
? Do you want to use MariaDB instead of PostgreSQL? No
```

Alfresco uses PostgreSQL by default, but alternatively `MariaDB` can be used as database.

```
? Are you using different languages (this is the most common scenario)? Yes
```

By default, many organisations are storing document in different languages or the users are accessing the platform with browser configured in different languages. If this is your case, enable this configuration.

```
? Do you want to create an internal SMTP server? No
```

This service provides an internal SMTP server (for outgoing emails) based in a Postfix Relay. If you want to use your own mail server, you can configure it manually after the generation of the Docker Compose template.

```
? Do you want to create an internal LDAP server? No
```

This service provides an internal OpenLDAP server (for authentication). If you want to use your own LDAP or AD server, you can configure it manually after the generation of the Docker Compose template.

```
? Select the addons to be installed:
  Google Docs 3.1.0                      : https://github.com/Alfresco/google-docs/tree/V3.0.3
  JavaScript Console 0.6                 : https://github.com/share-extras/js-console
  Order of the Bee Support Tools 1.1.0.0 : https://github.com/OrderOfTheBee/ootbee-support-tools
  Share Site Creators 0.0.7              : https://github.com/jpotts/share-site-creators
  Simple OCR 2.3.1                       : https://github.com/keensoft/alfresco-simple-ocr
  ESign Cert 1.8.2                       : https://github.com/keensoft/alfresco-esign-cert
```

A small catalog of trusted *addons* is provided by default, but you can install any other using the deployment folders.

```
? Are you using a Windows host to run Docker?
```

When using a Windows host to run Docker, standard [Docker Volumes](https://docs.docker.com/storage/volumes/) are used instead of [Bind Docker Volumes](https://docs.docker.com/storage/bind-mounts/). This options is easier to run in Docker Windows environments.

```
? Do you want to use a start script? Yes
```

The wrapper script for the docker-compose file allows nice features as a wait for alfresco to finish the boot and much more. Use "./start.sh -h" for more information.

## Passing parameters from command line

Default values for options can be specified in the command line, using a `--name=value` pattern. When an options is specified in the command line, the question is not prompted to the user, so you can generate a Docker Compose template with no user interaction.

```
$ yo alfresco-docker-installer --acsVersion=6.1
```

**Parameter names reference**

* `--acsVersion`: 6.1, 6.2 or 7.0
* `--ram`: number of GB available for Docker
* `--mariadb`: true or false
* `--crossLocale`: true or false
* `--smtp`: true or false
* `--ldap`: true or false
* `--addons`: list of addons to be installed: js-console, ootbee-support-tools, share-site-creators, simple-ocr, esign-cert
* `--startscript`: true or false
* `--https`: true or false
* `--serverName`: localhost default
* `--port`: 80 default
* `--ftp`: true or false

## Deploying additional addons

If you want to deploy additional addons, use deployment folders for Alfresco and Share services.

**Alfresco**

```
├── alfresco
│   ├── modules             > Deployment directory for addons
│   │   ├── amps            > Repository addons with AMP format
│   │   └── jars            > Repository addons with JAR format
```

**Share**

```
└── share                   
    └── modules             > Deployment directory for addons
        ├── amps            > Share addons with AMP format
        └── jars            > Share addons with JAR format
```

## Using Docker Compose

Once the files have been generated, review that configuration is what you expected and add or modify any other settings. After that, just start Docker Compose.

```
$ docker-compose up --build --force-recreate -d
```

You can shutdown it at any moment using following command.

```
$ docker-compose down
```

Alternatively if you choose to apply the start script, you can start the deployment with

```
./start.sh
```

It will wait until alfresco is reachable and shutdown with

```
./start.sh -d
```

More options are available with.

```
./start.sh -h
```

Following folder structure is generated when Docker Compose is running. Depending on the configuration selected, some folders cannot be available in your server.

```
├── alfresco                > DOCKER
│   ├── Dockerfile          > Docker Image for Alfresco Repository
│   ├── bin                 > [OCR] Shell script to communicate with OCR Service
│   ├── modules             > Deployment directory for addons
│   │   ├── amps            > Repository addons with AMP format
│   │   └── jars            > Repository addons with JAR format
│   └── ssh                 > [OCR] Shared key to communicate with OCR Service

├── config                  > CONFIGURATION
│   └── nginx.htpasswd      > Password to protect the access to Solr Web Console

├── config/cert             > SSL Certificates (only when using HTTPs)
│   ├── localhost.cer       > Public part for the SSL Certificate
│   └── localhost.key       > Private part for the SSL Certificate

├── data                    > DATA STORAGE (it's recommend to perform a backup of this folder)
│   ├── alf-repo-data       > Content Store for Alfresco Repository
│   ├── ldap                > [LDAP] Internal database
│   ├── ocr                 > [OCR] Temporal folder shared between Alfresco Repository and OCR
│   ├── postgres-data       > Internal storage for database
│   ├── slap.d              > [LDAP] Control folder
│   └── solr-data           > Internal storage for SOLR

├── docker-compose.yml      > Main Docker Compose template

├── logs                    > LOGS
│   ├── alfresco            > Alfresco Repository logs
│   ├── postgres            > PostgreSQL database logs
│   └── share               > Share Web Application logs

├── ocrmypdf                > OCR
│   ├── Dockerfile          > Docker Image for ocrmypdf program
│   └── assets              > Additional configuration to communicate with Alfresco Repository

├── search                  > SOLR
│   └── Dockerfile          > Docker Image for SOLR

└── share                   > SHARE
    ├── Dockerfile          > Docker Image for Share
    └── modules             > Deployment directory for addons
        ├── amps            > Share addons with AMP format
        └── jars            > Share addons with JAR format
```

## Docker Volumes

In order to enable persistent storage, several Docker Volumes are configured by default. When using from Linux, some permissions on your local folders need to be set.

Identifying the right UID for every folder can be obtained by starting Docker Compose without the volumes declaration. Following lines should be commented in `docker-compose.yml` file.

```
    postgres:
#        volumes:
#            - ./data/postgres-data:/var/lib/postgresql/data
#            - ./logs/postgres:/var/log/postgresql

    solr6:
#        volumes:
#            - ./data/solr-data:/opt/alfresco-search-services/data

```

Once the volumes have been commented, start Docker Compose.

```
$ docker-compose up --build --force-recreate
```

After that, you can find the SOLR Docker Image and the UID of the user owning data folders (`solr` by default).

```
$ docker ps
ded1748f961f    nginx:stable-alpine   tmp_proxy_1
b01e0abb3c0e    tmp_alfresco          tmp_alfresco_1
4fef719112ad    postgres:10.1         tmp_postgres_1
99a4bd6ede52    tmp_share             tmp_share_1
554236b9bedf    tmp_solr6             tmp_solr6_1

$ docker exec -it tmp_solr6_1 sh

$ cd /opt/alfresco-search-services/

$ ls -la
drwxr-xr-x 5 solr solr 4096 Nov 21 13:07 data

$ id -u solr
33007
```

Stop Docker Container and set the right permissions on your host folder.

```
$ docker-compose down

$ sudo chown -R 33007 data/solr-data
```

You could need to adjust also the permissions for `postgres` user inside PostgreSQL Docker Image. By default the UID is 999, but you can perform similar operations as above to guess this number.

```
$ sudo chown -R 999 logs
```

Uncomment the lines in your `docker-compose.yml` for the volumes declaration and your Docker Compose should be ready to use.

**Windows volumes**

When using Windows, standard [Docker Volumes](https://docs.docker.com/storage/volumes/) can be used instead of [Bind Docker Volumes](https://docs.docker.com/storage/bind-mounts/).

For instance, for an installation in a folder named `tmp`, following volumes are created.

```
$ docker volume ls --filter name=tmp_
DRIVER              VOLUME NAME
local               tmp_alf-repo-data
local               tmp_alf-repo-logs
local               tmp_alf-share-logs
local               tmp_postgres-data
local               tmp_postgres-logs
local               tmp_solr-data
```

You need to take care of this volumes for backup and other operations.

In case you want to clean your environment (loosing all the data inside), you can remove all this Docker containers to start from scratch.

For instance, for an installation in a folder name `tmp`, following command will **remove** all the information persisted.

```
$ docker volume rm $(docker volume ls -q --filter name=tmp_)
```

## Docker Images

* [alfresco-content-repository-community:7.0.0](https://hub.docker.com/r/alfresco/alfresco-content-repository-community)
* [alfresco-share:7.0.0](https://hub.docker.com/r/alfresco/alfresco-share)
* [alfresco-search-services:2.0.1](https://hub.docker.com/r/alfresco/alfresco-search-services)
* [postgres:13.1](https://hub.docker.com/_/postgres)
* [nginx:stable-alpine](https://hub.docker.com/_/nginx)
* [mwader/postfix-relay](https://hub.docker.com/r/mwader/postfix-relay)
* [osixia/openldap](https://hub.docker.com/r/osixia/openldap)
* [osixia/phpldapadmin](https://hub.docker.com/r/osixia/phpldapadmin)
* [jbarlow83/ocrmypdf:latest](https://hub.docker.com/r/jbarlow83/ocrmypdf)

## Service URLs

These are default URLs, selecting HTTP port 80.

* If you selected a different port (for instance 8080), the services will be available in http://localhost:8080.

* If you selected `https`, the services will be available in https://localhost

* If you included a different server name from `localhost` (for instance `alfresco.com`), the services will be available in http://alfresco.com or https://alfresco.com

*Default URLs*

http://localhost

Default credentials
* user: admin
* password: admin

http://localhost/share

Default credentials
* user: admin
* password: admin

http://localhost/alfresco

Default credentials
* user: admin
* password: admin

http://localhost/solr

Default credentials
* user: admin
* password: admin

http://localhost/api-explorer

Default credentials
* user: admin
* password: admin

http://localhost:8088

Default credentials
* user: cn=admin,dc=alfresco,dc=org
* password: admin

## Building

It's not required to build or download this project in order to use it. But this can be done using default *npm* tools.

The module is available at **npm**:

https://www.npmjs.com/package/generator-alfresco-docker-installer

If you want to build it locally, you need an environment with Node.js and Yeoman. And from the root folder of the project, just type:

```bash
$ npm link
```
