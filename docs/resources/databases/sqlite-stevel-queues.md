# SQLite3 - Queue Snapshots - Steve L

## abstract

### Point of contact

Steve Leak, <mailto:sleak@lbl.gov>

### Database and type

[SQLite3](https://www.sqlite.org/) [SQL RDBMS](/resources/databases.md#sql)

### Use case

#### Overview

I have a large collection of timestamped snapshots of our batch system, including
currently-queued jobs, currently running jobs, reservations, information about 
each partition, state of each node, etc. Some of this could be reconstructed from
Slurm database, some is transient and not stored (eg reservations present at a 
given time). I wish to ingest the already-collected data as well as ongoing 
collection into a database in order to reconstruct and inspect the state of the 
batch system at a given time in the past.

#### Data stored

My database schema has tables for snapshots of queues, priorities, node-states,
etc, these are keyed by (job/node-id, time-of-snapshot). Also for mostly-static 
items like reservations, partitions, etc, keyed by (name-of-thing, time-recorded). 
These included a 'tvanished' field to indicate when eg a reservation no longer
appears in the system. (A future reservation may have the same name but quite 
different parameters)

#### Size

The collected data is in the order of a few GB per week

### Installation

#### Infrastructure

SQLite3 is a database-in-a-file, so the infrastructure is simply a 
locally-accessible disk. In this instance I am using the Lustre scratch 
filesystem on NERSC Cori (Cray CX40)

#### Recipe

Comes standard in most Unix systems

#### Notes / Outcomes

Note that SQLite3 uses unix file locking to manage concurrency, so it 
requires a POSIX-compliant filesystem (eg, not NFS)

Installation is trivial

### Test constraints

Document:

  - Available hardware (CPUs, memory etc)

