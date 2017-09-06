Thermostat Agent Builder Docker image
=============================

This repository contains Dockerfiles for a Thermostat Agent Builder image, which in turn
can be used to build a Thermostat Agent base image, call this image `icedtea/thermostat-ng-agent`.

That image, in turn, can be used for Java application images or builder images to be based on it.
By basing your image on `icedtea/thermostat-ng-agent`, a Thermostat agent can get enabled on demand in
order to monitor your Java app.

Environment variables
---------------------------------

The icedtea/thermostat-ng-agent image recognizes the following environment
variables that you can set during initialization by passing `-e VAR=VALUE` to
the Docker run command.

|    Variable name              |    Description                              |
| :---------------------------- | -----------------------------------------   |
|  `THERMOSTAT_AGENT_USERNAME`  | User name for the Thermostat agent to use connecting to the web gateway |
|  `THERMOSTAT_AGENT_PASSWORD`  | Password for connecting to the web gateway          |
|  `THERMOSTAT_<plugin>_URL`    | The URL for the `<plugin>` microservice provided by the web gateway     |
|  `THERMOSTAT_TLS`             | Whether to encrypt communication with the web gateway using TLS (default value: "true") |
|  `THERMOSTAT_TLS_CERT_FILE`   | Location of trusted certificate file used for TLS communication (required if `THERMOSTAT_TLS` is enabled) |
|  `THERMOSTAT_KEYSTORE_PASS`   | Password to use for the keystore containing the certificate specified in `THERMOSTAT_TLS_CERT_FILE` (required if `THERMOSTAT_TLS` is enabled) |
|  `THERMOSTAT_DISABLE_HOSTNAME_VERIFICATION` | Whether host name checking should be disabled during TLS handshakes (default value: "false") |
|  `APP_USER`                   | The application user the Java app Thermostat shall monitor runs as (default value: "default") |

Substitute `<plugin>` for the following:
`JVM_OVERVIEW`, `VM_GC`, `VM_MEMORY`, `VM_IO`, `HOST_OVERVIEW`, `HOST_CPU`, `HOST_MEMORY`, `HOST_NETWORK`, `COMMANDS`

Usage
---------------------------------
First, you need to build this image, let's call it `icedtea/thermostat-agent-builder`:

    $ sudo docker build -t icedtea/thermostat-agent-builder .

Next, build a Thermostat Agent version into `icedtea/thermostat-ng-agent` using the builder
image:

    $ sudo s2i build http://icedtea.classpath.org/mirror/git/thermostat-ng-agent icedtea/thermostat-agent-builder icedtea/thermostat-ng-agent

Then, image `icedtea/thermostat-ng-agent` is intended to be used as a base image for builder/runtime images in your
Dockerfile via:

    FROM icedtea/thermostat-ng-agent

When securing communication with the web gateway using TLS, a trusted certificate for the web gateway must be provided as a file in the container.
This can be done by mounting the certificate as a volume in your `docker run` command:

    $ docker run -e THERMOSTAT_TLS_CERT_FILE="/path/in/container/gateway.crt" -v /local/machine/path/gateway.crt:/path/in/container/gateway.crt:ro,Z [...]
