# Maintenance

_Fetchq_ uses _indexes_ in such a smart way that handling millions of documents in a
queue is not a problem at all, even on small machines. Cool stuff.

But in order to achieve this level of performances there are some operations that need
to be executed here and there. Operations like:

* update the documents lifecycle flag
* detect orphan documents and take action on them
* update the metric informations
* update the metric timeserie for each queue
* drop old logs (with retention policy)
* drop old metrics (with retention policy)

**NOTE:** you normally don't need to bother with this task. If you use one of the client
libraries, then the library will run it in background for you.

```
SELECT * FROM fetchq_mnt();
```

This will run all the **maintenance jobs** for all the existing queues. If you are a little
be like me and are courious about how things work, you may want to take a look at
`fetchq_sys_jobs` table. This is a special queue that holds the maintenance jobs schedule.

Every _Fetchq_ client contains a daemon that will trigger jobs from this table, like it was
a _cronjob_, but with a schedule and the **ability to dinamically change the scheduling** by
simply changing the payload to suit your needs.

**NOTE:** There are many other maintenance commands that run specific tasks on a specific queue,
in some circumstances it might be usefull to know them. You can find them in the API section.