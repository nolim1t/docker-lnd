lncm/docker-lnd
================

![Build Status]
[![gh_last_release_svg]][gh_last_release_url]
[![Docker Image Size]][lnd-docker-hub]
[![Docker Pulls Count]][lnd-docker-hub]

[Build Status]: https://github.com/lncm/docker-lnd/workflows/Build%20%26%20deploy%20tag/badge.svg

[gh_last_release_svg]: https://img.shields.io/github/v/release/lncm/docker-lnd?sort=semver
[gh_last_release_url]: https://github.com/lncm/docker-lnd/releases/latest

[Docker Image Size]: https://img.shields.io/microbadger/image-size/lncm/lnd.svg
[Docker Pulls Count]: https://img.shields.io/docker/pulls/lncm/lnd.svg?style=flat
[lnd-docker-hub]: https://hub.docker.com/r/lncm/lnd


This Dockerfile is based on the [Dockerfile] officially provided within the `lnd` repo. 

The changes from upstream include:

* image versions correspond to lnd versions strictly,
* Official `./release.sh` script used for maximum compatibility with provided binaries,
* `apk add` removed from the `final` stage of the build to make cross-compilation possible,
* maintainer label added.

[Dockerfile]: https://github.com/lightningnetwork/lnd/blob/master/Dockerfile


## Tags

* [`v0.8.0` (`+build7`)][build7] - currently _latest_ version of `lnd` (see [log][log7])
    * `v0.8.0-amd64`,  `v0.8.0.arm64`, `v0.8.0-arm32v7`, `v0.8.0-arm32v6`
* [`v0.8.0-monitoring` (`+build9`)][build9] - latest version of lnd with [monitoring] enabled (see [log][log9])
* [`v0.7.1` (`+build11`)][build11] (see [log][log11])


[build7]: https://github.com/lncm/docker-lnd/releases/tag/v0.8.0%2Bbuild7    
[log7]: https://github.com/lncm/docker-lnd/runs/262864700

[build9]: https://github.com/lncm/docker-lnd/releases/tag/v0.8.0-monitoring%2Bbuild9
[log9]: https://github.com/lncm/docker-lnd/runs/262901705
[monitoring]: https://github.com/lightningnetwork/lnd/blob/v0.8.0-beta/monitoring/monitoring_on.go

[build11]: https://github.com/lncm/docker-lnd/releases/tag/v0.7.1%2Bbuild11
[log11]: https://github.com/lncm/docker-lnd/runs/263056982


## Usage


### Pull

First pull the image from [Docker Hub]:

```bash
docker pull lncm/lnd:v0.8.0
```

> **NOTE:** Running above will automatically choose native architecture of your CPU.

[Docker Hub]: https://hub.docker.com/r/lncm/lnd


#### Start

Then to start lnd, execute:

```bash
docker run -it --rm \
    -v ~/.lnd:/root/.lnd \
    -p 9735:9735 \
    -p 10009:10009 \
    --name lnd \
    --detach \
    lncm/lnd:v0.8.0
```

That will run lnd such that:

* all data generated by the container is stored in `~/.lnd` **on your host machine**,
* port `9735` will be reachable for the peer-to-peer communication,
* port `10009` will be reachable for the RPC communication,
* created container will get named `lnd`,
* that command will run the container in the background and print the ID of the container being run.


#### Interact

To issue any commands to a running container, do:

```bash
docker exec -it lnd BINARY COMMAND
```

Where:
* `BINARY` is either `lnd` or `lncli`, and
* `COMMAND` is something you'd normally pass to the binary   

Examples:

```bash
docker exec -it lnd lnd --help
docker exec -it lnd lnd --version
docker exec -it lnd lncli --help
docker exec -it lnd lncli getinfo
docker exec -it lnd lncli getnetworkinfo
```


## New Release

Release process is fully automated.  That being said there are a few things that need to be done to prepare for the release.

### Patch Release 

In case of a patch release (v0.0.X), it's enough to edit `TAG` variable in appropriate `Dockerfile`, and push the changes.

Ex. If `lnd` gets updated from `v0.6.0-beta` to `v0.6.1-beta`, it's enough to open `0.6/Dockerfile`, update [this line] with the new tag, commit, and push.

[this line]: https://github.com/lncm/docker-lnd/blob/master/0.6/Dockerfile#L17

### Major/Minor release

Since this releases might bring changes that are not backwards compatible, A new root directory needs to be created with `MAJOR.MINOR` version in its name.  Once that done, a copy of a Docker file from previous release should be copied there, and `TAG` variable within should be updated.  If necessary, other changes can be made to that file as well.

### Trigger

To trigger build of new-multi arch release.  Create a git tag, and push it to Github.  After a few minutes the new version should show up on Docker Hub. 
