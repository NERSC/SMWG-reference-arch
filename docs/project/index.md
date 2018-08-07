# Defining a reference architecture

High-level strategy is to base the reference architecture on NERSC's Data 
Collect and implement it as a set of Docker containers, along with these
web pages describing how to setup and customize the design


## Requirements, contraints and implications of those

- must support being implemented incrementally - bite-sized chunks

- should be able to run *something* on minimal hardware (ideally 1 server), 
  and scale up to collect more data from more sources by adding servers
  (a container approach should support this)

- Docker is a convenient platform for the reference architecture, but we don't
  want to be locked into it - should be possible to implement on eg VMs or 
  bare metal too  
  Including Dockerfiles (as well as docker-compose file) should help with this
  as they include an install recipe

- should support on-the-fly analysis of high-frequency/never-stored data  
  This is supported by including a message queueing service before ingest.
  NERSC uses RabbitMQ for message buffer. Caribou uses Kafka. Spark also 
  reportedly supports this sort of flow. Some exploration of the capabilities 
  of each is interesting


## High priority use-cases

- Diagnose reason for large apparent variation in job runtime
  - other applications competing for resources?
  - failing components, full filesystems?
  - job parameters, user script?

- Understanding and diagnosing HSN performance characteristics
   - practical way to collect Aries counters
   - resource/utilization needs?
   - job placement within the network

- Support for central syslogging


## Architecture vision

The architecture is based on NERSC's Data Collect, and we plan to create 
DockerFiles describing each component and a docker-compose file describing how 
they interact. This should allow the architecture to be implemented on as 
little as a single service node but also to be scaled up to a monitoring 
cluster

The architecture is described by the following block diagram:

![reference_architecture.png](../resources/reference_architecture.png "Block diagram of reference architecture for monitoring")

The two central components are the message queue and the storage.

### Message Queue

The message buffer/queue serves a few purposes:

- Single consistent interface

- Robustness
  The message buffer can be scaled out for redundancy and prevents data from 
  being lost due some specific component being unavailable

- Scalability
  More data collection can be supported by spinning up more buffer containers

- Flexibility
  Storage need not be the only component using incoming data, we can also eg
  send high-bandwidth data to a real-time visualizer component

#### Which message queueing system to use?

[RabbitMQ](https://www.rabbitmq.com/) and [Kafka](https://kafka.apache.org/) 
are well-known options, they approach the problem differently. 

See [Resources/Message Queuing Systems](/resources/message_queues) for detailed notes.

### Which storage to use?

There are *many* databases to choose from. NESRC undertook an exercise to 
identify the questions we want data collection to help answer, and what types
of data collection we are using or might use. The diagram below is a very broad
summary of what we found. Summarizing it further: most data is either 
time-series metrics, snaphots and logs, or document/record-oriented data (also
with time fields).

See [Resources/Databases](/resources/databases.md) for database options and comparisons.


![collection_summary.png](../resources/collection_summary.png "Data collection needed to support different types of questions")


 
