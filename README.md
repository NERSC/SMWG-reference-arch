# SMWG-reference-arch

A reference architecture for monitoring of Cray supercomputers

Modern HPC systems are comprised of many interacting subsystems with very high
component counts, making it difficult to determine:
 - Whether the system is healthy
 - Which components contribute to some observed problem
 - How effectively the system is being utilized 

There are many sources of system data (logs,sensors, performance counters for 
individual components). This project aims to describe a reference architecture 
for collecting, processing, storing and analyzing data in a way that helps HPC
center staff identify and prevent system problems and to decide where to 
concentrate resources in order to best optimize usage of the system

## Layout of this repo

- [`howto/`](howto/index.html)  
  The end product: guides that a site sysadmin can follow to stand up a 
  monitoring infrastructure following this reference architecture

- [`resources/`](resources/index.html)  
  Supporting resources a sysadmin can download, customize and use, eg Docker
  compose files for components of the infrastructure 

- [`project/`](project/index.html)  
  Notes for this project, ie creating and providing a reference architecture
