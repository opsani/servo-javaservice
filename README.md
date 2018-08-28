# servo-javaservice - Optune servo driver for Java Tomcat services on Linux

This project is an adjust driver for Optune servos. It supports adjusting the Java VM configuration parameters for a Linux service that runs under Java VM.

Assumptions:

* running on a recent Linux distribution (e.g., Ubuntu 16.04)
* a single service under systemd, that can be controlled (started/stopped/statused) using the `systemctl` utility
* a recent Java VM (tested with 1.8.0_171 on x86_64)
* the service configuration is performed by a startup bash script that sets the CATALINA_OPTS environment variable prior to starting the service

Note that the driver does not assume Tomcat and can be used for other types of Java services (e.g., by changing the name of the environment variable)

The driver modifies the config script by inserting its own configuration line, e.g.:

```
CATALINA_OPTS="${CATALINA_OPTS} -XX:GCTimeRatio=99 -XX:+UseStringDeduplication" # __OPTUNE_DRIVER_ANCHOR__
```



## Configuration file example

Here is an example of the `config.yaml` configuration file:

```
javaservice:
    name: myjavaservice                     # name of service (used for systemctl and as Optune component)
    env_file: /path/to/config/script.sh     # path to script with env vars to modify

    readiness_probe:                        # readiness probe configuration, omit object to disable
        url: 'https://localhost/healthz'    # URL to check, must be a valid URL
        expect: 'OK'                        # response expect string; omit to skip checking, consider 2xx response a success
        timeout: 300                        # how many seconds to wait for readiness, default 300

    settings:
        InitialHeapSize: {}
        MaxHeapSize: {}
        MaxGCPauseMillis: {}
        MaxGCMinorPauseMillis: {}
        GCTimeRatio: {}
```

Limitations:
