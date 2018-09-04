# Databases

## RDBMS vs various NoSQL

### RDBMS <a name="sql"/>

RDBMS (SQL) is mostly about consistency and reliability

ACID: Atomicity, Consistency, Isolation, Durability
   - transaction (ie change to DB) must be atomic, either via locks or by keeping a read-only copy of anything being modified until the txn is committed
   - every transaction must leave DB in consistent state, ie any write must satisfy all constraints, cascades, triggers, etc
   - concurrent txns must act as if executed in sequence (isolation)
   - durable - any completed/committed txn must survive crash, power out, etc

data consistency managed partly by normalizing it (DRY)
data stored in rows (each record is a block on disk)
rows can be searched, cols/fields can be indexed to make search faster
searches requiring more than one table are expensive, returns a giant NxM-row table
heirarchical relationships are hasslesome 

### Key-Value <a name="key-value"/>

 simple, fast 1:1 storage of things by hashed key

### Document DB <a name="doc-db"/>

 like key-value, but the value is in some semi-structured format eg json, so a stored object can have attributes. Can create indexes on an attribute for faster searching of documents having that attribute
Good when not all data is the same, or data is not well suited to normalization (needs more flexibility)
ACID is possible, but not in all cases (eg multi-document queries maybe not ACID)
writing tends to be very fast (no ACID, so no need for much locking), reading/querying might be heavier load
   some correspondence to RDBMS:
      database    <-->    index
      partition      <-->   shard
      table           <-->   type             (basically a label)
      row             <-->   document
      column       <-->   field           (but no schema, so maybe maps to "many columns, guessed type, many mostly NULL")
      schema      <--> mapping (elastic) (kind of per-type schema, defines kind, indexed_or_not, stored_or_not for each field)
=> if you need to do set manipulations eg joins, sql will probably do it better. 
=> consistency is harder, expect things to "drift"     


### Column-store <a name="col-store"/>

like RDBMS, but instead of storing a record at a time, store each column as a contiguous block. Means queries on multiple columns, or on few columns in large record, require less I/O, less seeking, less memory, etc, and tend to be faster. Writing likely to be slower / more difficult / might be only eventually-consistent, not always consistent

### Graph DB <a name="graph-db"/>

 - stored as set of triples of "object, relationship, subject"
 - each part of the tripple can have attributes which can be used in queries
 - good when main use is "find items related to this item" (simple traversal)
 - harder to find sets of objects when based on commonality rather than relationship (though, can index properties to make this more efficient)

article maybe worth a look: https://markedaspertinent.wordpress.com/2009/08/04/nosql-if-only-it-was-that-easy/
also:
https://blog.timescale.com/time-series-data-why-and-how-to-use-a-relational-database-instead-of-nosql-d0cd6975e87c

## Time-series databases <a name="timeseries"/>

- characterized by having a heavy insert load, but updates are rare (queries might be common though)
- more recent data is more likely to be "active"

- mostly implemented as column-store.
- a couple of partiiculary interesting ones:

### RRDTool
aimed at collecting metrics for short-term use (eg dashboard graphs)
- fixed size circular buffer
- fixed interval collection (missed samples are interpolated)
- can hold counters (increment only), gauges (actual measurements), deriveds (counters that can go up or down), absolutes (like counter but assumes previous value was 0) (so actually hold "relatives" not absolutes)

it occurs to me that a variation on this with multiple circular buffers in a tower-of-hanoi type scheme (eg, using 4x zooming, every 4th entry is stored one level higher - possible "as well" depending on how values are accumulated - so you get higher resolution data for more recent and lower for older data)

### TimeScaleDB
a psql extention that decomposes tables in 2d (by time and by field) to give good insert performance in a relational DB

