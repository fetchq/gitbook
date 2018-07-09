# Fetchq Features

### Multiple distributed workers

The whole point of developing _Fetchq_ was to scale a crowler app that I was working on.

My target website (often in this pages I refer to it as the "superheroes" website) did slow down
their response time and I had to put more servers (with different public IPs) into the job.

I soon found out that by simply picking up a task from a sorted list was not enough. I needed
some sort of exclusive access to it that was resilient to multiple concurrent access.

To date I am using _Fetchq_ in production with ~90 machines pointing to it as single source
of truth for the question "what do I do next?".

### Endless (~) reliable storage

I admit I am no expert in higly available distributed queue systems that do not fail.
I know it is possible to build a custom queue system with a couple of existing open source
solution, but I got to face the hard fact that is expensive in knowledge and infrastructure.

_Postgres_ is a reliabale relational database with a solid set of tools to perform maintenance
operations (backup/restore) that are familiar to most developers. Among _Postgres_ wonderful
internal features I can mention the table namespacing, basically you can route data to specific
logic discs based on the table's name. Wonderful. You can distribute data the way you want to the
disk you want, based on need.

To date my production db is ~700Gb on a relatively small _AWS m4.medium_ instance. There are
~160M documents in it, and I can scale the data storage to multiple discks before I even start
to think to clusterize _Postgres_ itself (which is in the roadmap for _Fetchq_ anyway).

It is ridicolously easy to dump single (or multiple) queues into compressed files and automatically
send them up to _S3_ for long term restorable backup. It doesn't sweat!

### Near real-time metrics

`SELECT COUNT(*)` works only up to few hundred thousand records. Beyond that point you have to build
your own guns.

Much much work went into (and still is) building a solid and reliable metric system that rapresent a
_near real time_ status of your queue. But today you can track your entire system progress through a
set of _Grafana boards_ that give an immediate visual understanding. Cool stuff.

![grafana-boards](./images/grafana-boards.png)

Of course you can also programmatically access any metric, plus you can **track your own metrics as well**.

Say you want to **measure and track how long a piece of code takes to execute**, this is not only doable,
but it integrates seamlessly with the core metrics and you can plot a custom chart for that too.

### Best effort policy, with a twist

_Fetchq_ is not just another _FIFO_ queue. Documents are **sorted by date from the older to the
newer**. 

When you insert a document in the queue you can freely choose a **next execution date**, _Fetchq_ guarantees that no actions will be taken until that date comes.

This single feature allows you to inject a document in any point-in-time. Say you really need
to get a document processed right away, you can simply inject it with a 
`nextExecution = 0000-00-00`, it probably get picked up right away (unless you use that setting
for all your documents!)

### Queue partitioning

More often than not you end up having some stuff that is more important than others. You may want to prioritize the execution of documents for a specific customer over another, and so forth.

_Fetchq_ allows you to detail a **priority number**, higher priority gets processed first.

A **priority** will create a partition in the queue, all the pending documents of priority "1"
will be processed before the pending documents of priority "0".

### Queue versioning

God knows if **data change through time**! It's just a fact of Life, hence _Fetchq_ accept
it and tries to work for the best!

When you insert a document in a queue you can set a **version number** which is just an integer. There are no particular rules for that, I normally start from "0" and bump it 
by one unit any time I feel I need a new version.

When you want to pick a document for processing you must specify which version you are targeting.

This is so cool because it allows you to run multiple concurrent versions of the same worker that targets different versions of the document.

Just imagine you need to process some _XML_ files to extract info, but they might come in in
different formats. If you use the version number wisely it's going to be an easy thing for you!

### Migration workers

It's not just it yet with versioning. You can also decide to **migrate a document** from
version "0" to version "1" (maybe you need to update some data in the document's payload).

If you have few thousands documents it's a no brainer, but if you - like me - deal with 
millions or hundreds million of those, you know you can't just run an update or a `forEach` loop.

_Fetchq_ allows you to **define a special type of worker that is employed at the sole pourpose of upgrading a document to a new version**, in preparation for further data prodcessing.

We used that feature a lot with our _superheroes_ data scraping project as they were changing
their APIs and data format A LOT in a very short amount of time and without notice.

> If you scrape a document and you get a data processing error you can decide to "promote"
> that document to a next version, meaning that you need to investigate further and probably
> update your worker so to match the new data format.

### Errors threshold

Your workers are going to screw up in some way. You know that, I know that. Bugs exist.

But it's quite tricky to debug in a system that is possibly running on multiple servers and
handle millions of documents each day. A worker can simply crash for many many reasons.

_Fetchq_ allows you to resolve a document processing with some different actions, one of those
is `reject` where you can specify an error message and a detail. You can even reference 
a _processId_ (I use it to find out which _Docker container_ in which _EC2 instance_ 
threw the errror).

_Fetchq_ has the knowledge of **orphan documents**, documents that got picked by a worker
but were nevere resolved in time because the worker crashed.

In both cases we are facing a document processing error, that may or may not repeat
through time. Just imagine if it was a temporary networking issue, do you want to loose
your document because of that? No you don't!

For this reason _Fetchq_ allows for a **try again** policy. By default each document is
given 5 chances to resolve, but you can customize this threshold queue by queue.

### Errors logging

All the errors that are generated by a worker or from orphan documents are logged in a
queue specific data partition (fancy name for "a table") for performance reasons.

You can read from this table to find out what is going not so well about your data
processing.

### Error retention policy

Error logging can be very expensive in terms of data storage, for that reason
_Fetchq_ automatically drops old logs.

By default we retain the last 24h, but you can customize this policy for each queue.


