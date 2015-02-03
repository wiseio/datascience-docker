# datascience-docker

[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/wiseio/datascience-docker?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Containers and material for the hackday at wiseio HQ.

## Introduction ##

If you haven't used Docker before, a good place to start is the [Docker User Guide](https://docs.docker.com/userguide/).

### Setup ###

For those running a linux distro, you should be able to apt-get/yum docker. For those on OS X, you'll need to install boot2docker, which adds a virtual machine that docker will/can run in. [http://viget.com/extend/how-to-use-docker-on-os-x-the-missing-guide]("How to Use Docker on OS X: The Missing Guide") is a a good write up on the subject.

## Getting Started ##

The basic Python-centric image is hosted at the Docker Hub. You can grab it:

```
docker pull wiseio/datascience-base
```

The README.md in datascience-base/ gives an overview of how to use that image to run a container locally.


