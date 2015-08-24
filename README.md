# Table of Contents

- [Supported tags](#supported-tags)
- [Introduction](#introduction)
    - [Version](#version)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Examples](#examples)

## Supported tags

- `latest`

## Introduction

> NOTE: I'm working at Splunk, but this is not an official Splunk images.
> I build them in my free time when I'm not at work. I have some knowledge
> about Splunk, but you should think twice before putting them in
> production. I run these images on my own home server just for
> my personal needs. If you have any issues - feel free to open a
> [bug](https://github.com/outcoldman/docker-stats-splunk/issues).

Dockerfile to build Splunk Light image with predefined dashboards which work
great with [docker-stats-splunk-forwarder](https://hub.docker.com/r/outcoldman/docker-stats-splunk-forwarder/).

## Version

- Splunk Light: `6.2.5`

## Installation

Pull the image from the [docker registry](https://registry.hub.docker.com/u/outcoldman/docker-stats-splunk/).
This is the recommended method of installation as it is easier to update image.
These builds are performed by the **Docker Trusted Build** service.

```bash
docker pull outcoldman/docker-stats-splunk:latest
```

Or you can pull latest version.

```bash
docker pull outcoldman/docker-stats-splunk:latest
```

Alternately you can build the image locally.

```bash
git clone https://github.com/outcoldman/docker-stats-splunk.git
cd docker-stats-splunk
docker build --tag="$USER/docker-stats-splunk" .
```

## Quick Start

To manually start container (see more details in description for [outcoldman/splunk](https://hub.docker.com/r/outcoldman/splunk/)
and [outcoldman/docker-stats-splunk-forwarder](https://hub.docker.com/r/outcoldman/docker-stats-splunk-forwarder/) images)

```bash
docker run --name vsplunk \
    -v /opt/splunk/etc \
    -v /opt/splunk/var \
    busybox
docker run --hostname splunk \
    --name splunk \
    --volumes-from=vsplunk \
    -p 8000:8000 \
    -d outcoldman/docker-stats-splunk:latest
docker run --hostname dockerforwarder \
    --name dockerforwarder \
    --link=splunk \
    --volume /var/run/docker.sock:/var/run/docker.sock:r \
    -e "SPLUNK_FORWARD_SERVER=splunk:9997" \
    -d outcoldman/docker-stats-splunk-forwarder:latest
```

Or if you use [docker-compose](https://docs.docker.com/compose/)

```yaml
vsplunk:
  image: busybox
  volumes:
    - /opt/splunk/etc
    - /opt/splunk/var

splunk:
  image: outcoldman/docker-stats-splunk:latest
  hostname: splunk
  volumes_from:
    - vsplunk
  ports:
    - 8000:8000

dockerforwarder:
  image: outcoldman/docker-stats-splunk-forwarder:latest
  hostname: dockerforwarder
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock:ro
  links:
    - splunk
  environment:
    - SPLUNK_FORWARD_SERVER=splunk_indexer:9997
```

## Configuration

See [docker-splunk](https://github.com/outcoldman/docker-splunk) for more details.

## Examples

Open Splunk (port `8000` is used by default for web interface), go to the `Dashboards`
and you will see 2 Dashboards.

### Docker

- CPU% (800% because of 8 cores)
- Memory Usage (one line is the maximum limit, another is how much is used right now)
- CPU usage per container
- Memory Usage per container (% of limit)
- Network Input per container
- Network Output per container
- Last Events (excluding `top` as I query it regularly)
- Top processes from all container

[![Splunk Docker Dashboard 01](https://raw.githubusercontent.com/outcoldman/docker-stats-splunk/master/examples/docker_dashboard_01.png)](https://raw.githubusercontent.com/outcoldman/docker-stats-splunk/master/examples/docker_dashboard_01.png)

[![Splunk Docker Dashboard 02](https://raw.githubusercontent.com/outcoldman/docker-stats-splunk/master/examples/docker_dashboard_02.png)](https://raw.githubusercontent.com/outcoldman/docker-stats-splunk/master/examples/docker_dashboard_01.png)

### Docker container

- Top processes
- Last events

[![Splunk Docker Dashboard per container](https://raw.githubusercontent.com/outcoldman/docker-stats-splunk/master/examples/docker_dashboad_per_container.png)](https://raw.githubusercontent.com/outcoldman/docker-stats-splunk/master/examples/docker_dashboad_per_container.png)
