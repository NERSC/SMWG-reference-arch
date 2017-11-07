# How to set up monitoring based on this reference architecture

There are easily available template DockerFiles based on Debian/Ubuntu so 
initially this reference will use those. This might eventually change, since 
HPC tends to use CentOS, Scientific Linux or SuSE-based distributions

## Test environment

On a workstation, install [Docker](https://docs.docker.com/engine/installation/) 
(the Community Edition is sufficient). There are helpful "Getting Started" 
tutorials at [docker docs](https://docs.docker.com/get-started/)

## Message buffer: RabbitMQ

The official RabbitMQ DockerFiles are on [GitHub](https://github.com/docker-library/rabbitmq).
Debian/Ubuntu-based DockerFiles are available for most We'll use the 
Debian-based image for now 

```console
  $ git clone https://github.com/docker-library/rabbitmq.git
  ...
  $ cd 3.6/debian
  $ docker build -t rabbitmq .
  ...
```

The Dockerfile includes a step like:
`gpg --keyserver ha.pool.sks-keyservers.net --recv-keys 0A9AF2115F4687BD29803A206B73A36E6026DFCA`
which sometimes fails with a message like:
`gpg: keyserver receive failed: Cannot assign requested address`
This seems to be an issue at the keyserver end (perhaps load), waiting a while 
and trying again eventually works. If you persistently have trouble, try 
modifying the Dockerfile to use a different keyserver from the pool (see 
https://sks-keyservers.net/overview-of-pools.php for a list) or a different
method of checking the key signature (see instructions at 
https://www.rabbitmq.com/signatures.html)

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

 
