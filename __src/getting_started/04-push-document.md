## <small class="new-row">[&laquo; Getting Started](README.md)</small> 04 Push a Document

First read:   
[Create a new queue &raquo;](03-create-queue.md)

---

Now that you have a queue ready, the first action you want to look into is how to push data into it.

There are 3 possible ways to add a document into a queue:

- `fetchq_doc_push()` - add a unique document by subject
- `fetchq_doc_upsert()` - add a unique document by subject, or update an existing document
- `fetchq_doc_append()` - add a new document with an auto-generated unique subject

In this document I'm going to briefly describe how and in which conditions you may want to use those methods.

## fetchq_doc_push

```
# Function signature
fetchq_doc_push(
  queue::string          // queue name, es: "foo"
  subject::string        // your unique id, es: "john"
  version::int           // the version of the document, es: 0
  priority::int          // an arbitrary priority, es: 0
  nextIteration::date    // when to execute the document, es: "2081-06-30 15:05"
  payload::jsonb         // a json payload, consider it a cookie. es: '{"age":22}'
)

# SQL Example
SELECT * FROM fetchq_doc_push('foo', 'john', 0, 0, NOW() + INTERVAL '20s', '{}');
```

If you own the subject of your document you are going to use `fetchq_doc_push()`. The classic
example is that you have a list of superheroes that you want to monitor by scraping the
website `http://avengers.com/{superuser}`.

This project requires you to periodically scrape `avengers.com` on a specific page, which
url can be generated automatically by knowing the name of the superuser. At this point the most
important thing is to do not insert the same superhero twice or we are going to waste time!

In this context the `payload` does not have much of an importance, but can be used as
storage for informations that need to be carried out between different iterations:

```
# those documents are good because the subject is unique
SELECT * FROM fetchq_doc_push('avengers', 'ironman', 0, 0, NOW(), '{ "lastScore": 20 }');
SELECT * FROM fetchq_doc_push('avengers', 'hulk', 0, 0, NOW(), '{ "lastScore": 18 }');
...
# this should be an error because "ironman" was already there!
SELECT * FROM fetchq_doc_push('avengers', 'ironman', 0, 0, NOW(), '{ "lastScore": 20 }');
```

**If you attempt to push duplicates they will simply be ignored**. The function returns the
number of documents that got inserted without causing subject duplication. 

**NOTE:** this behaviour allow for the fastest possible `INSERT` performance, expecially when
using the `bulk insert` signature.

> On an average mac I could measure insert speed of **18K documents per second**
> when pushing **5M documents!**


## fetchq_doc_upsert

```
# Function signature
fetchq_doc_upsert(
  queue::string          // queue name, es: "foo"
  subject::string        // your unique id, es: "john"
  version::int           // the version of the document, es: 0
  priority::int          // an arbitrary priority, es: 0
  nextIteration::date    // when to execute the document, es: "2081-06-30 15:05"
  payload::jsonb         // a json payload, consider it a cookie. es: '{"age":22}'
)

# SQL Example
SELECT * FROM fetchq_doc_upsert('foo', 'john', 0, 1, NOW() + INTERVAL '1m', '{"age":22}');
```

## fetchq_doc_append

```
# Function signature
fetchq_doc_append(
  queue::string          // queue name, es: "foo"
  payload::jsonb         // a json payload, consider it a cookie. es: '{"age":22}'
  version::int           // the version of the document, es: 0
  priority::int          // an arbitrary priority, es: 0
)

# SQL Example
SELECT * FROM fetchq_doc_append('foo', '{"age":22}', 0, 1);
```

If you need to queue a document regardless of the subject univocity you append.

In this circumstance the `payload` becomes the most important piece of information as it is
the only discriminant between documents. Document version and priority work as usual.

A classic example might be a `sendmail` queue to which we want to append documents that describe
emails that need to be send. The worker will then process the queue, using the payload as source
of the email informations:

```
SELECT * FROM fetchq_doc_append('sendmail', '{"to":"foo@foo.it", "template":"signup", "subject":"welcome!"}', 0, 0);
```

This document will be appended with the current dateTime and will be processed as soon as possible.
Classic implementation of a FIFO queue. In this type of queue the document is likely to be deleted
after a successful iteration, but you may have different needs!


---

Next step:  
[Consume the queue &raquo;](05-consume-queue.md)
