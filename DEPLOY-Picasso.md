AppPulse Trace deployment on Picasso - Java
===========================================

This document describes the recommended process for adding AppPulse
Trace monitoring to a Picasso PaaS
([Stackato](https://docs.stackato.com/ "Stackato")) Java application.  There are
multiple ways, and possibly "better" ways, to add AppPulse; this is
the process the Picasso M&A team recommends the prototype team follow.

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
5. Add *post-staging* hooks to the Stackato
   [*manifest.yml*](https://docs.stackato.com/user/deploy/manifestyml.html
   "manifest.yml") file.  This adds and configures the agent inside
   the application DEA
   ([Droplet Execution Engine](http://docs.stackato.com/admin/reference/architecture.html#droplet-execution-agents
   "DEA")).
6. Build (*package*) the WAR and push to the Stackato server.


Detailed Instructions
---------------------

asdfg xyzzy lorium ipsum sum.

