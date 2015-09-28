AppPulse Trace deployment on Picasso - Java
===========================================

This document describes the recommended process for adding AppPulse
Trace monitoring to a Picasso PaaS
([Stackato](https://docs.stackato.com/ "Stackato")) Java application.  There are
multiple ways, and possibly "better" ways, to add AppPulse; this is
the process the Picasso M&A team recommends the prototype team follow.

> Note:  this document presumes the Java project being deployed is
> managed and built by [Maven](https://maven.apache.org/ "mvn").

Overview
--------

Instrumentation of a Stackato Java application with AppPulse Trace is
done by taking a specialized bundle (*.zip*), provided when the application is
registered with AppPulse, and adding it to the deployment bundle sent
to Stackato (*.war*).  The six step process is summarized as follows:

1. Log into AppPulse Trace in the appropriate environment.
2. Register a new application in AppPulse Trace.
3. Download the agent bundle (*.zip*) provided by AppPulse Trace
   during application registration.
4. Place the expanded agent bundle (unzipped) in the application's
   project directory.  Ensure the agent files are included in the
   final WAR during the projects build process.  (*mvn package*)
5. Modify the Stackato deployment descriptor,
   ([*manifest.yml*](https://docs.stackato.com/user/deploy/manifestyml.html
   "manifest.yml")) to configure AppPulse in the application DEA
   ([Droplet Execution Engine](http://docs.stackato.com/admin/reference/architecture.html#droplet-execution-agents
   "DEA")).
6. Build (*package*) the WAR and push to the Stackato server.


Detailed Instructions
---------------------

### I. Log into AppPulse Trace

* sCloud:
  http://opsafcvm090.hpswlabs.adapps.hp.com:8080/apmappsSaasMock/login.html
  (user: apm@hp.com)
* Picasso Rack:  TBD

### II. Register a new application

After logging in you will be presented with a screen full of tiles.
These tiles represent each registered application being monitored by
AppPulse.  In the lower right should be a placeholder with a large
plus symbol ("+").  Press the plus symbol and complete the dialog
that is displayed.

### III. Download the bundled agent.

In the dialog for "Add New Application", displayed from II, press the
"Download Agent" button and store the downloaded zip file where it can
be unzipped into your application project directory.

### IV. Expand the bundled agent into the project

Unzip the downloaded zip file from step III and place the contents at
the top level of your project's directory.  The directory from the
agent bundle should be named `AppPulseJavaAgent`, and if placed
properly it will be in the same directory as your `pom.xml`
([POM](https://maven.apache.org/pom.html "POM")) file.
  
The expanded directory must now be added to your final WAR file.  The
following snipped from a POM file should be sufficient.  The
`AppPulseJavaAgent` directory must be at the top level of the POM file
and a pier of the `META-INF` and `WEB-INF` directories.

    ...
    <build>
      <plugins>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <configuration>
            <webResources>
              <resource>
                <directory>.</directory>
                <includes>
                  <include>AppPulseJavaAgent/**</include>
                </includes>
              </resource>
            </webResources>
          </configuration>
        </plugin>
    ....


### V. Modify the Stackato deployment descriptor to configure AppPulse
    in the DEA.

Edit the
[*manifest.yml*](https://docs.stackato.com/user/deploy/manifestyml.html
"manifest.yml") file to include the following *post-staging* hooks.
This adds and configures the agent inside the application DEA
([Droplet Execution Engine](http://docs.stackato.com/admin/reference/architecture.html#droplet-execution-agents
"DEA")).

    ...
    stackato:
      hooks:
        post-staging:
          - mv AppPulseJavaAgent $STACKATO_APP_ROOT/
          - java -jar -$STACKATO_APP_ROOT/AppPulseJavaAgent/lib/AppPulseAgent.jar -setup


### VI. Build the WAR and push it to the Stackato server

    > mvn clean package
    > stackato -n push
    
Note:  An overview of "General Deployment" using the *stackato*
command line tool can be found here:
https://docs.stackato.com/user/deploy/index.html#pushing-application-code 
