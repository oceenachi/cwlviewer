# CWL Viewer

This is a [Spring Boot](http://projects.spring.io/spring-boot/) MVC application which fetches [Common Workflow Language](http://www.commonwl.org/) files from a Github repository and creates a page for it detailing the main workflow and its inputs, outputs and steps.

[![Build Status](https://travis-ci.org/common-workflow-language/cwlviewer.svg?branch=master)](https://travis-ci.org/common-workflow-language/cwlviewer) [![Coverage Status](https://coveralls.io/repos/github/common-workflow-language/cwlviewer/badge.svg)](https://coveralls.io/github/common-workflow-language/cwlviewer) [![Gitter](https://img.shields.io/gitter/room/gitterHQ/gitter.svg)](https://gitter.im/common-workflow-language/cwlviewer?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) [![](https://images.microbadger.com/badges/image/commonworkflowlanguage/cwlviewer.svg)](https://microbadger.com/images/commonworkflowlanguage/cwlviewer "MicroBadger commonworkflowlanguage/cwlviewer") [![Docker image commonworkflowlanguage/cwlviewer](https://images.microbadger.com/badges/version/commonworkflowlanguage/cwlviewer.svg)](https://hub.docker.com/r/commonworkflowlanguage/cwlviewer/ "Docker Hub commonworkflowlanguage/cwlviewer")
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.823534.svg)](https://doi.org/10.5281/zenodo.823534)



## License

Distributed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).
See the file [LICENSE.md](LICENSE.md) for details, and
[NOTICE.md](NOTICE.md) for required attribution notices.

## Contribute

Feel free to contribute! You may [raise an issue](https://github.com/common-workflow-language/cwlviewer/issues),
provide a [pull request](https://github.com/common-workflow-language/cwlviewer/pulls)
or join the [gitter chat for cwlviewer](https://gitter.im/common-workflow-language/cwlviewer)!


# Using CWL Viewer

You are recommended to use the **production instance** of CWL Viewer at https://view.commonwl.org/ which runs the latest [release](https://github.com/common-workflow-language/cwlviewer/releases). Any downtime should be reported on the [gitter chat for cwlviewer](https://gitter.im/common-workflow-language/cwlviewer).

<!--- I don't think this is a thing any more.
The **dev instance** at http://view.commonwl.org:8082/ corresponds to the current `master` branch, and is updated every 6 minutes to run the latest [commonworkflowlanguage/cwlviewer docker image](https://hub.docker.com/r/commonworkflowlanguage/cwlviewer/builds/). Note that this instance is NOT secured and might break at any time.
-->

# Running

If you are a developer, or you want to use the CWL Viewer in a closed environment, then you can run your own instance.

## Recommended - Running with Docker

This application can be started with [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/install/).

To start CWLViewer exposed on port `8080`, run:

    docker-compose up

To stop and remove:

    docker-compose down

If you change the source code, then use this `docker-compose.override.yml` and
re-build with `docker-compose build`:

```yaml
version: '3.2'
services:
  spring:
    build: .
```

See the [docker-compose.yml](docker-compose.yml) file for details.

If you have modified the source code, then you may want to build the docker image locally first:

    docker build -t commonworkflowlanguage/cwlviewer .

## Running without Docker

### Requirements

#### MongoDB

You will need to have [MongoDB](https://www.mongodb.com/) running,
by default on `localhost:27017`

If you are running from the command line, you can override this by supplying
system properties like `-Dspring.data.mongodb.host=mongo.example.org` and
`-Dspring.data.mongodb.port=1337`

#### Apache Jena Fuseki (or alternative SPARQL server)

You will also need to have a SPARQL server such as [Apache Jena Fuseki](https://jena.apache.org/documentation/fuseki2/) running,
by default on `localhost:3030`

## Compiling and Running

To compile you will need [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or OpenJDK 8 (`apt install openjdk-8-jdk`),
as well as [Apache Maven 3](https://maven.apache.org/download.cgi) (`apt install maven`).

Spring Boot uses an embedded HTTP server. The Spring Boot Maven plugin includes a run goal which can be used to quickly compile and run it:

```
$ mvn spring-boot:run
```


Alternatively, you can run the application from your IDE as a simple Java application by importing the Maven project.

You need to install [Graphviz](http://www.graphviz.org/) for all unit tests to pass.

You can create an executable JAR file by using:

    mvn clean install

Afterwards, run:

    java -jar target/cwlviewer*.jar

(The exact filename will vary per version)

Once CWL Viewer is running, you should see log output somewhat like:

```
()..)
s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
org.researchobject.CwlViewerApplication  : Started CwlViewerApplication in 28.604 seconds
```

Now check out http://localhost:8080/ to access CWL Viewer.

## Configuration

There are a variety of configuration options detailed in the [application configuration file](https://github.com/common-workflow-language/cwlviewer/blob/master/src/main/resources/application.properties) which can be adjusted.

When deploying with docker, these can be overriden externally by creating/modifying `docker-compose.override.yml` as follows:

```yaml
version: '3.2'
services:
  spring:
    environment:
            applicationName: Common Workflow Language Viewer
            applicationURL: https://view.commonwl.org
            cacheDays: 1
```

The properties can alternatively be provided as system properties on the
command line, e.g. `-DcacheDays=1` or via a [variety of other methods supported by Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

# Dump/restore

While you can perform backup of the Docker volumes,
for larger upgrades of CWL Viewer it is recommended instead to do a JSON dump
and re-load, which will force CWL Viewer to fetch and parse again.

The script `dump.sh` can be used for regular backups, it will store the full
output of /workflows as a timestamped gzip-compressed JSON file:

    $ ./dump.sh https://view.commonwl.org/ /var/backups/cwl
    /var/backups/cwl/2018-06-06T135133+0000.json.gz

The script `load.py` (requires Python 3) can be used to restore from such JSON dumps:

    ./load.py /var/backups/cwl/2018-06-06T135133+0000.json.gz https://view.commonwl.org/

The optional parameter `--no-commits` can be added to skip those entries that
look like a commit ID. Note that this might break previous permalinks.


# Thanks

Developers and [contributors](https://github.com/common-workflow-language/cwlviewer/graphs/contributors) include:

* **Mark Robinson** http://orcid.org/0000-0002-8184-7507
* Stian Soiland-Reyes http://orcid.org/0000-0001-9842-9718
* Michael Crusoe http://orcid.org/0000-0002-2961-9670
* Carole Goble http://orcid.org/0000-0003-1219-2137
* Charles Overbeck https://github.com/coverbeck
* Finn Bacall http://orcid.org/0000-0002-0048-3300

Thanks to:

* [eScience Lab](http://www.esciencelab.org.uk/) at [The University of Manchester](http://www.cs.manchester.ac.uk/)
* [BioExcel Center of Excellence for Computational Biomolecular Research](http://bioexcel.eu/)
* * European Commission's [H2020 grant 675728](http://cordis.europa.eu/projects/675728)
* [Common Workflow Language](http://www.commonwl.org/) community and the [CWL Gitter room](https://gitter.im/common-workflow-language/common-workflow-language)
