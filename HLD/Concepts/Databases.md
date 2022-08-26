### What is a Database
* A construct to hold together data for an application - persistent or non-persistent, based on requirements.
* Any application would have some sort of data 
  * Persistent could be the one critical to application, generally CRUD;
  * There can be other such as usage, logs, performance etc
  * There can be other non-persistent ones such as data cached to improve run-time query latency
* Type of data to be stored has led to various type of databases suitable for the data.
  * Improve run-time queries --- key, value data :: Cache data sotrage :: Redis, Memcached etc
  * Wide column DB: Cassandra, Hbase
  * Document DB : Mongo, Dynamo, Couch
  * Data with relations among them --> School, library, travel etc :: Relational :: PostGres, MySql, CockroachDB (scalable relational data store)
  * Complex relation based data --> Social network :: Graph DB
  * Multi-model :: FaunaDB
  * Time-series DB :: data with history :: Netflix shows watched over time, quote or other values for a stock or financial instrument. :: Timeseries DB :: Influx
  * Data warehouse
  * Search data - data with attributes; Data storage should allow filtering, searching, ranking etc.

Generally, 

### What factors affect choice of data store
* Structure
* Query Pattern
* Scale to handle


### Types of Databases
#### Cache DB
* 

### Concepts to scale storage
* Scaling
    * Sharding
    * Replication
    * Partitioning
* SQL vs NoSql