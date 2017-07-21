Thermostat Agent Builder Docker image
=============================

This repository contains Dockerfiles for a Thermostat Agent Builder image, which in turn
can be used to build a Thermostat Agent base image, call this image `thermostat-project/thermostat-ng-agent`.

That image, in turn, can be used for Java application images or builder images to be based on it.
By basing your image on `thermostat-project/thermostat-ng-agent`, a Thermostat agent can get enabled on demand in
order to monitor your Java app.

Environment variables
---------------------------------

The thermostat-project/thermostat-ng-agent image recognizes the following environment
variables that you can set during initialization by passing `-e VAR=VALUE` to
the Docker run command.

|    Variable name              |    Description                              |
| :---------------------------- | -----------------------------------------   |
|  `THERMOSTAT_AGENT_USERNAME`  | User name for the Thermostat agent to use connecting to the web gateway |
|  `THERMOSTAT_AGENT_PASSWORD`  | Password for connecting to the web gateway          |
|  `THERMOSTAT_<plugin>_URL`    | The URL for the `<plugin>` microservice provided by the web gateway     |
|  `APP_USER`                   | The application user the Java app Thermostat shall monitor runs as |

Substitute `<plugin>` for the following:
`JVM_OVERVIEW`, `VM_GC`, `VM_MEMORY`, `HOST_OVERVIEW`, `HOST_CPU`, `HOST_MEMORY`, `HOST_NETWORK`, `COMMANDS`

Usage
---------------------------------
First you will need the base image that this image uses. You will need to clone and build it yourself at this time:

    $ git clone https://github.com/jerboaa/openjdk-8-maven-docker.git
    $ cd openjdk-8-maven-docker
    $ sudo docker build -t openjdk-8-64bit-maven .

Once the base image is built, you need to build this image, let's call it `thermostat-project/thermostat-ng-agent-builder`:

    $ sudo docker build -t thermostat-project/thermostat-ng-agent-builder .

Next, build a Thermostat Agent version into `thermostat-project/thermostat-ng-agent` using the builder
image:

    $ sudo s2i build https://github.com/thermostat-project/thermostat-ng-agent thermostat-project/thermostat-ng-agent-builder thermostat-project/thermostat-ng-agent

Then, image `thermostat-project/thermostat-ng-agent` is intended to be used as a base image for builder/runtime images in your
Dockerfile via:

    FROM thermostat-project/thermostat-ng-agent
