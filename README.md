Docker Notes
============

Latest Revelation:
------------------

Discovered [Portainer.io](http://portainer.io/) which is a UI for
managing Docker Host and Swarm. Great if you are just learning about
Docker. Also check out [rancher](http://rancher.com/).

[ZerotoDocker
Netflix](http://techblog.netflix.com/2014/11/zerotodocker-easy-way-to-evaluate.html)

General
-------

-   Encapsulation of and application or process and all of
    its dependencies.  We do this now with WAR and zip files, just at a
    different level.

-   Docker containers have a small footprint, fast startup and are
    portable distributable

-   Local development can be the same as other environments. 

-   Shared Kernel and NOT a full Operating System unlike VMs

-   Many organization start with a Hybrid of Containers run on VMs

-   Containers Shipping Metaphor - At X we have a diversity of tech
    stack becauise we use right tool for the right job, containers would
    help support the management and deployment

-   Containers and Microservices could help facilitate Going to the
    Cloud<sup>tm</sup>

-   Tools

-   Networking 

    -   [Weave](https://www.weave.works/)

    -   [Project
        Calico](https://www.projectcalico.org/getting-started/docker/)

-   Orchestration 

    -   [Kubernetes](https://kubernetes.io/)

    -   [Marathon](https://mesosphere.github.io/marathon/)

    -   [Mesos](http://mesos.apache.org/)

    -   [Fleet](https://coreos.com/fleet/docs/latest/launching-containers-fleet.html)

    -   [Swarm](https://www.docker.com/products/docker-swarm)

-   Service Discovery 

    -   [Consul ](https://www.consul.io/)

    -   [Registrator ](http://gliderlabs.com/registrator/latest/)

    -   [SkyDNS ](https://github.com/skynetservices/skydns)

    -   [etcd](https://coreos.com/etcd/)

    -   [Eureka](https://github.com/Netflix/eureka/wiki)

Docker Logging
--------------

Strongly consider having your app log to STDOUT and STDERR.  The docker
logging mechanism works by reading from STDOUT and STDERR. Since
containers and their disks are ephemeral, persisting log files becomes
complex.  By writing logs to STDOUT and STDERR you can avoid the extra
complexity and use docker log drivers to send log data to some
persistent store.

By defaults you can use **docker logs** command which captures STDOUT
STDERR

Use the --log-driver flag to specify type for logs

            - json-file

            - syslog

            - journals

            - gelf

            - fluentd

            - none

Example docker logs -t container\_name

<https://docs.docker.com/engine/reference/commandline/logs/>

**docker events** command gives real time events for a given container

<https://docs.docker.com/engine/reference/commandline/events/>

 

**Recommended to send docker logs to ELK or syslog syslog**

Monitoring and Alerting
-----------------------

docker stats - live stream stats CPU% MEM USAGE, MEM % etc.

<https://docs.docker.com/engine/reference/commandline/stats/>

<https://docs.docker.com/engine/admin/runmetrics/>

For Storage
[InfluxDB](https://www.influxdata.com/time-series-platform/influxdb/)+[OpenTSDP](http://opentsdb.net/)

For Display:
[Graphite](https://graphiteapp.org/)+[Grafana](http://grafana.org/)

More tools:

[cAdvisor ](https://github.com/google/cadvisor)

[Heapster](https://github.com/kubernetes/heapster) if using Kubernetes

[Prometheus](https://prometheus.io/)

Example: <https://medium.com/@uschtwill/docker-container-and-host-monitoring-logging-in-a-box-e60c45aebcf8#.jaoum9xut>

 

Networking
----------

To connect containers across host use the concept of Ambassadors which
are proxy containers that forwardd traffic to actual service 

Example:

Host A would have an app container and an ambassador container
App+Ambassador -&gt; Host B would have an Ambassador  and DB Service
container

<https://docs.docker.com/articles/ambassador_pattern_linking/>

 

***Pro Tip: One process per container***

Continuous Integration & Continuous Delivery
--------------------------------------------

-   Include test code in images but thing carefully

    -   Run tests in containers because that is what will environment be
        running in production

-   Types of Tests

    -   Unit - small pieces of functionality

    -   Component - tests for external interfaces of an individual
        service - could need multiple containers  

    -   End to End - Functional Tests of an entire application

    -   Consumer Contract - For consumer of a service point of view -
        tests could be performance related or simple input output of an
        API

        -   Could be a separate 'contract' per consumer type

        -     Test failures would indicate breaking a contract with a
            consumer  

    -   Integration Tests - Tests Components are working together
        correctly

        -   If integration test fail you may not want to deploy or you
            may roll back a *green* deployment 

    -   Scheduled Runs - Run overnight regularly - nightly builds to
        ensure the current state of an app is valid because external
        issues like patching, network issues still affect the hosts 

-   Test In Production

    -   Blue Green Deployments - Deploy a new deployment (green)
        parallel to an existing (blue) deployment

        -   If the green deployment starts correctly traffic can start
            routing to it, if there are any errors flip back to the blue
            version (preferable automatically)

        -   As part of a green deployment we can possibly run
            integration tests to make sure our dependencies are what we
            expect   

-   Unit tests and others run as part of the pipeline before publishing
    to the registry

-   e2e tests would be run after publishing to the registry say in DEV

-   Jenkins Container 

    -   We could allow each project/team to use an approved Jenkins
        Docker Container and have CI/CD in a box that just works and not
        rely on a central server

-   A/B testing for UX changes

-   [Dockerize ](https://github.com/jwilder/dockerize)- automate docker
    compose from templates

-   [Docker-gen](https://github.com/jwilder/docker-gen) - Generate files
    from dockr container meta-data

-   Automatic restart using the --restart flag if containers crash

-   To perform a zero-downtime update of a container, you will need to
    have a load balancer or reverse proxy in front of the container and
    do something like: 

    -   Bring a up a new container with the updated image (it’s best to
        avoid trying to update images in place). 

    -   Point the load balancer at the new image, for some or all of the traffic. 

    -   Test the new container is working.

    -   Turn off the old container

<!-- -->

-   Instead of shell script or Docker restart flags - process
    manager/init system like systems or upstart to bring up the
    containers

-   Patching Containers w/ CM Tools

    -   Treat containers as VMs and use CM tools to update software OR

    -   **Preferred method - **CM tool to manage Docker host to ensure
        containers have the correct images and containers are just
        *black boxes* that can be replaced or modified 

        -   **Best Practice** - Containers are **IMMUTABLE**.  Don't try
            to update a running container.  Instead create a new image
            with desired updates and replace existing container with new
            container running new updated image.

<!-- -->

-   Ansible Docker Plugin

-   Host configuration - Use familiar OS for organization unless a very
    large cluster containers - use specialized OS and orchestration
    tools

-   Cloud and Containers

    -   EC2 Container Service

    -   GCE

-   Storage Drivers

-   Data with Containers

    -   Flocker to manage data migration 

    -   Volumes 

-   Sharing Secrets

    -   Never save secrets in theDocker image

    -   Key Value Store best approach 

        -   KeyWhiz

        -   Vault

        -   Crypt

        -   How does the container authenticate against store? 

            -   - one -use token 

Security
--------

### Best practices

-   Dont use latest tag for images instead use specific versions or hash
    and ensure tags are signed

-   Set User, by default the container runs as *root,* Set a user with
    limited privleges to run the process inside container.  Default root
    user may be used to install software, however.     

-   Use a Docker Registry to ensure images are from verified sources:
     We will most likely use a Private Docker Registry, either through
    Nexus or on a VM

-   Use GOSU not SUDO

-   Deploy Same Apps Containers on Same Hosts no multi tenant, so Vessel
    Risk containers should not be on the same host as Import Cargo
    containers

-   Deploy containers with access to the open internet separately from
    those containing sensitive data, so UI containers would be on a
    separate host from their Backend container counterparts

-   Limit Container Networking

    -    Only open ports needed and only allow other containers to
        connect to them

    -   Use - -icc=false prevent inter container communication
        altogether

-   Remove setuid/setgid binaries

-   Limit memory using flags -m --memory-swap

-   Stress Test Containers with utility stress --cpu 2 --io 1 --vm 1
    --vm-bytes 128M --timeout 10s --verbose

-   Limit CPU

-   Limit Restarts --restart=on-failure only restarts on failure and not
    always to avoid loop of sadness

-   Limit Filesystems

    -   only allolw write if necessary, use --read-only flag otherwise

    -   Use volumes to mount environment specific files

-   Limit Capabilities

    -   <https://docs.docker.com/engine/reference/run/>

    -   --cap-drop 

    -   ---cap-add

-   Run a Hardened Kernel

-   Use up to date images 

-   Audit images with docker diff 

-   Audit mounted volumes

-   Use Tools available: 

    -   <https://github.com/docker/docker-bench-security>

    -   <https://benchmarks.cisecurity.org/>

    -   <https://cisofy.com/lynis/>

-   Run only necessary utilities software

-   Incident Response, in the case of an incident:

    -   Docker commit snapshot of compromised container

    -   docker diff to find out what was changed

    -   docker history to see history of an image

    -   docker inspect for low level info on a container

Resources
---------

[Using Docker Book Code Examples](https://github.com/using-docker)

 
