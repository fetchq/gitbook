
## pending document

A document who's `next_iteration` date is set in the past is called **pending** and it
will be processed as soon as possible.

## pending documents

see: pending document

## active document

A document becomes active when is being selected using `fetchq_doc_pick()`, normally
a worker process is responsible of this state.

Only pending documents can become active.

## active documents

see: active document

## worker

A _worker_ is an app written in any language you may know (our examples are written in NodeJS)
that pick a document from a queue and process it.

_Fetchq_ is engineered so that you can run multiple workers concurrently and they will not
cross each other or receive the same document twice!

## orphan document

When a document gets picked by a worker, the worker is given a certain amount of time to
complete the data processing. It's a timeout tha the worker can set by itself and by default
is set to 5 minutes.

If this time lapse and the worked takes no action, _Fetchq_ assumes that the worker is dead and
takes action on the document.

Based on the error threshold the document may be scheduled for another attempt or may
become a dead document.

## orphan documents

see: orphan document

## dead document

Means: "you don't need to process me ever again".

A document can be marked as "dead" by an explicity worker's action or because it went beyond
the error threshold.

**NOTE:** the document is still in the queue so no other documents with the same subject
will be admitted into the queue. If your want to allow for re-insert then you need to 
drop the document.
