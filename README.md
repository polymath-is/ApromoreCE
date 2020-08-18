![apromore](http://apromore.org/wp-content/uploads/2019/11/Apromore-banner_narrow.png "apromore")

# Apromore Community Edition

This repository contains the source code of [Apromore Community Edition (CE)](https://apromore.org/platform/editions/). Apromore CE is the free edition of Apromore. It includes [Apromore Core](https://github.com/apromore/ApromoreCore) — the kernel of the Apromore platform — as well as experimental plugins developed by the open-source community. By default, Apromore uses the H2 database for storing process models and the workspace metadata (folder structure, user accounts and access rights). Instructions are provided below to use a MySQL database instead of H2.

The instructions below are for installation of Apromore CE from the source code. For convenience, we also make available a containerized image in [Docker](https://github.com/apromore/ApromoreDocker/releases). Note: the Docker image is currently available for an older version of Apromore CE (version 7.12). An updated Docker image with Apromore CE version 7.15 is expected end of August.

If you simply wish to try Apromore without going through the installation procedure, you can create an account in this [public demo node](http://apromore-ce.cloud.ut.ee). The public node is only for demonstration and trials. It is not actively supported and may be shut down at any time.

If you are looking for the commercial edition (Apromore Enterprise Edition), check the [Apromore web site](http://apromore.com)

## System Requirements
* Linux Ubuntu 18.04 (We do not support newer versions as it may lead to dependency issues), Windows 10/WS2016/WS2019, Mac OSX 10.8 or newer
* Java SE 8 ["Server JRE"](https://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) or ["JDK"](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Edition 1.8. Note that newer versions, including Java SE 11, are currently not supported
* [Apache Maven](https://maven.apache.org/download.cgi) 3.5.2 or newer
* [Apache Ant](https://ant.apache.org/bindownload.cgi) 1.10.1 or newer
* [Lessc](http://lesscss.org/usage/) 3.9.0 or newer
* (optional) [MySQL server](https://dev.mysql.com/downloads/mysql/5.7.html) 5.6 or 5.7. Note that version 8.0 is currently not supported.


## Installation instructions
* Check out the source code using git: `git clone https://github.com/apromore/ApromoreCE.git`
* Open command prompt/terminal and change to the root of the project `cd ApromoreCE`
* Execute the following commands: `git submodule init` and `git submodule update`.  This populates the ApromoreCore subdirectory.
* Given that currently you are on the 'ApromoreCE' directory, go to the 'ApromoreCore' directory 'cd ApromoreCore'
* Checkout and pull the v7.15 branch of ApromoreCore 'git checkout v7.15' and 'git pull'. 
* Run the maven command `mvn clean install`.  This will build the Apromore manager, portal and editor and all the extra plugins.
* Create an empty H2 database `ant create-h2`.  Only do this once, unless you just want to reset to a blank database later on.
* Run the ant command `ant start-virgo-community`.  This will install, configure and start Eclipse Virgo, and deploy Apromore. Only do this once. Later, You can start the server by running the `startup.sh` script from the '/ApromoreCE/ApromoreCore/Apromore-Assembly/virgo-tomcat-server-3.6.4.RELEASE/bin/' directory.
* Open a web browser to [http://localhost:9000](http://localhost:9000). Use "admin”/“password” to access as administrator, or create a new account.
* Keep the prompt/terminal window open.  Ctrl-C on the window will shut the server down.


## Configuration

### Share file to all users (optional)

* By default Apromore does not allow you to share a file with all users (i.e. the "public" group is not supported by default). You can change this by editing the site.properties file present in the '/ApromoreCE/ApromoreCore/Apromore-Assembly/virgo-tomcat-server-3.6.4.RELEASE/repository/usr/' directory. Specifically, to enable the option to share files and folders with the “public” group, you should set “security.publish.enable = true” in the site.properties file.

### Predictive monitoring setup (optional)

* Predictive monitoring requires the use of MySQL; see the [Apromore Core README](https://github.com/apromore/ApromoreCore) for MySQL setup instructions.
  Once that is done, populate the database with additional tables as follows:
```bash
mysql -u root -p apromore < /$HOME/ApromoreCE/ApromoreCore/Supplements/database/Nirdizati.MySQL-1.0.sql
```
* Check out predictive monitoring repository from GitHub:
```bash
git clone https://github.com/nirdizati/nirdizati-training-backend.git
```
* Set up additional servers (alongside the Apromore server), as directed in [Nirdizati README](https://github.com/nirdizati/nirdizati-training-backend/blob/master/apromore/README.md)
* Set the following properties in `site.properties` present in the following directory: '/ApromoreCE/ApromoreCore/Apromore-Assembly/virgo-tomcat-server-3.6.4.RELEASE/repository/usr'
  - `training.python` must be set to the location of a Python 3 executable
   - `training.backend` must be directory containing `nirdizati-training-backend`
   - `training.tmpDir` must be a writable directory for temporary files
   - `training.logFile` must be a writable file path for logging
The following properties may usually by left at their default values:
   - `kafka.host` can be left at the default `localhost:9092`, presuming Zookeeper and Kafka are running locally. Note: Make sure that port 9092 is open on the server because kafka.host runs on port 9092 by default.
   - the various `kafka.*.topic` properties should already match those used in the `nirdizati-training-backend` scripts
* Stop and restart the server so that it picks up the changes to `site.properties`.
* Ensure that the following bundles are present in the Virgo `pickup` directory (`ant start-virgo-community` copies them there on startup):
  - Predictive-Monitor-Logic/target/predictive-monitor-logic-1.0.jar
  - Predictive-Monitor-Portal-Plugin/target/predictive-monitor-portal-plugin-1.0.war
  - Predictor-Training-Portal-Plugin/target/predictor-training-portal-plugin-1.0.war
  - Note: You can run the `ant start-virgo-community` command only once i.e at the startup. Later, you can start the server by running the `startup.sh` script present in the '/ApromoreCE/ApromoreCore/Apromore-Assembly/virgo-tomcat-server-3.6.4.RELEASE/repository/usr/' directory.

## Virgo Admin Console (Security)
* For security purpose, it is advisable to deactivate the virgo admin console.
* Go to the directory
`cd $HOME/ApromoreCE/ApromoreCore/Apromore-Assembly/virgo-tomcat-server-3.6.4.RELEASE/pickup`
* And delete the ‘org.eclipse.virgo.management.console_3.6.4.RELEASE.jar’ file `sudo rm -rf org.eclipse.virgo.management.console_3.6.4.RELEASE.jar`


