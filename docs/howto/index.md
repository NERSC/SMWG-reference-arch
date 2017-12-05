# How to set up monitoring based on this reference architecture

Official Dockerfiles for various components are based on a mixed bag of
Linux distributions. Eventually we may try to converge on a common base

## Test environment

On a workstation, install [Docker](https://docs.docker.com/engine/installation/) 
(the Community Edition is sufficient). There are helpful "Getting Started" 
tutorials at [docker docs](https://docs.docker.com/get-started/)

## Message buffer: RabbitMQ

The official repository for Docker images with RabbitMQ is on DockerHub, at
https://hub.docker.com/_/rabbitmq/ . You can pull a pre-made image with:

```console
  $ docker pull rabbitmq
```

Alternatively, the official RabbitMQ DockerFiles are on 
[GitHub](https://github.com/docker-library/rabbitmq). We'll use the 
Debian-based image for now 

```console
  $ git clone https://github.com/docker-library/rabbitmq.git
  ...
  $ cd 3.6/debian
  $ docker build -t rabbitmq .
  ...
```

> **Note:** The Dockerfile includes a step like:  
> `gpg --keyserver ha.pool.sks-keyservers.net --recv-keys 0A9AF2115F4687BD29803A206B73A36E6026DFCA`  
> which sometimes fails with a message like:  
> `gpg: keyserver receive failed: Cannot assign requested address`  
> This seems to be an issue at the keyserver end (perhaps load), waiting a while 
> and trying again eventually works. If you persistently have trouble, try 
> modifying the Dockerfile to use a different keyserver from the pool (see 
> https://sks-keyservers.net/overview-of-pools.php for a list) or a different
> method of checking the key signature (see instructions at 
> https://www.rabbitmq.com/signatures.html)

RabbitMQ uses ports 4369, 5671, 5672 and 25672, so we'll need to make these 
visible from outside the container (the DockerFile exposes them via the 
`EXPOSE` command). We use the `-p host_port:guest_port` option of `docker run` 
to map host ports to exposed guest ports - using the same port
numbers is ok for now:

```console
  $ grep EXPOSE DockerFile
  EXPOSE 4369 5671 5672 25672
  $ docker run -p 4369:4369 -p 5671:5671 -p 5672:5672 -p 25672:25672 rabbitmq
  ...
```

There is a simple tutorial on the [RabbitMQ web pages](https://www.rabbitmq.com/tutorials/tutorial-one-python.html)
to test that the containerized RabbitMQ server is working correctly. You will 
need the python `pika` package to use the example, you can install it with
`pip install pika`

## Storage component: ElasticSearch

The official repository for ElasticSearch Docker images is at 
https://www.docker.elastic.co/# . You can pull an image with:

```console
  $ docker pull docker.elastic.co/elasticsearch/elasticsearch:6.0.0
```

Alternately, to build an image from a customized Dockerfile, the templates 
from the [official Elastic repository](https://github.com/elastic/elasticsearch-docker) 
are a good starting point. You will need Python 3.5+ and virtualenv 
(`pip install virtualenv`)

> **Note:** The `master` branch of the Elastic git repository is the development
> branch, for a stable snapshot checkout a spefic version tag. See 
> https://www.elastic.co/downloads for available versions, the latest is 
> currently 6.0 

```console
  $ git clone https://github.com/elastic/elasticsearch-docker.git
  $ git checkout 6.0
```

The repository has jinja2 templates for the Dockerfile, and a Makefile to
generate the Dockerfiles from these, build the image and run some tests.

> **Note:** In version 6.0, the `version.json` file lists 6.0.1 as the specific
> version for 6.0, however a tar of that version is not available for the build 
> system to download, so change this to 6.0.0


> **Note:** The Dockerfile template in version 6.0 uses a `root_url` of 
> `artifacts.elastic.co`, which resolves under a web browser to www.elastic.co,
> but not with curl. To fix this add a line near top of makefile:  
> `URL_ROOT ?= https://www.elastic.co/downloads/elasticsearch`  
> and where it runs jinja2 on templates/Dockerfile.j2, add the option:  
> `-D url_root='$(URL_ROOT)'`  

