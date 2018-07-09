# Maintenance

_Fetchq_ uses _indexes_ in such a smart way that handling millions of documents in a
queue is not a problem at all, even in small machines. Cool stuff.

But in order to achieve this level of performances there are some operations that need
to be executed here and there. Some maintenance operations that:

* update the documents lifecycle flag
* detect orphan documents and take action on them
* update the metric informations
* update the metric timeserie for each queue
* drop old logs (with retention policy)
* drop old metrics (with retention policy)

**NOTE:** you normally don't need to bother with this task. If you use one of the client
libraries, then the library will run it in background for you.

## Queue maintenance

```
SELECT * FROM fetchq_mnt_run_all(100);
```

![fetchq-mnt-run-all](06-fetchq-mnt-run-all.png)

## Metrics maintenance

```
SELECT * FROM fetchq_log_pack();
```

![fetchq-metric-log-pack](06-fetchq-metric-log-pack.png)