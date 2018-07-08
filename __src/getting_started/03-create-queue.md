# Create a new queue

_Fetchq_ provides you with queues for you to manage few-to-millions documents. Hence your next
step is to _create_ a new queue:

````
SELECT * FROM fetchq_queue_create('foo');
````

![create-queue](03-create-queue.png)

This command will _upsert_ a queue. If the queue didn't exist then it's being created; else not
so much is happening, but the command will not fail. You have a boolean indicator that tells
you whether the queue had been created or updated.

**NOTE:** in future versions the "update" mode might trigger reindexing or version migration
schema updates on a specific queue.

## Queue Tables

If you list the tables again (`SELECT * FROM pg_catalog.pg_tables WHERE schemaname = 'public';`)
you will notice that 3 new tables where created:

* `fetchq__foo__documents` - will store the queue's documents
* `fetchq__foo__errors` - errors will be logged here
* `fetchq__foo__metrics` - timeserie metrics will let you plot the status in time for this queue

## Queue Data

_Fetchq_ keeps some queue related data around the system tables.

You can change some queue settings in `fetchq_sys_queues` like metrics and error logs retention period,
details about how often the maintenance jobs on the queue are performed, generic configuration about
other jobs, or possibly extensions.

For sure you want to keep an eye on the `fetchq_sys_metrics` table which contains info regarding
the current status of all the queues: number of documents, size of the pending documents, etc.

## Drop a Queue

In any moment you are entitled to **completely and irreversabily delete** a queue and all its related
contents: documents, jobs, metrics, ...

```
SELECT * FROM fetchq_queue_drop('foo');
```
