# Node Client

FetchQ was built with NodeJS in mind. Node is our primary language, and with
FetchQ and Node we manage ~800M documents handled by ~70 client servers
making external http requests all together. It works nicely.

We maintain the official `fetchq` package which is a wrapper around `pg` and
makes it easy to un a FetchQ worker process. 

**NOTE:** In order to run this tutorial you need a [working FetchQ server](01-setup-postgres.md) and a recent version of NodeJS (>8); We are going to
use all default values to make it easier to try this out.

## Connect a Client

```
// yarn add fetchq
const fetchq = require('fetchq')
```

Now I usually wrap my entire app around an `async` function so to be able
to leverage `async/await` inside. I'll do that here, but mind that all the
client methods return simple promises, you can do it that way _if you are
inclined to do so_.

```
const main = async () => {
    // our code will be placed in here...
}

main()
```

And mind that I'll skip all the `try/catch` in order to keep this tutorial as
focused as possible. You can always take a look at the complete demos in GitHub.

```
const client = fetchq()
await client.init()
await client.start()
```

If no errors are triggered you have now a fully working FetchQ node that is als
running all the due maintenance jobs under the hood.

The `client.init()` instruction is optional, if you aim to an already working database
you don't need it. Anyway it will not cause any trouble if the db is already initialized.

FYI: there all the `pg` authentication methods and `pool` settings are fully supported.

## Register a Worker

Now let's follow the steps and [create a new queue](03-create-queue.md) `pizzeria`
and [push some documents](04-push-document) in it:

```
select * from fetchq_queue_create('pizzeria');
select * from fetchq_doc_append('pizzeria', '{"pizza": "margherita"}', 0, 0);
select * from fetchq_doc_append('pizzeria', '{"pizza": "capricciosa"}', 0, 0);
select * from fetchq_doc_append('pizzeria', '{"pizza": "4 formaggi"}', 0, 0);
select * from fetchq_doc_append('pizzeria', '{"pizza": "prosciutto e funghi"}', 0, 0);
```

All we want to do is to register a simple Javascript function that will be executed
for every document in the queue, when the right moment arrives:

```
client.workers.register({
    queue: 'pizzeria',
    handler: doc => {
        console.log(`MAKE PIZZA: ${doc.payload.pizza} (order n: ${doc.subject}`)
        return { action: 'drop' }
    },
})
```

You don't have to worry about concurrency or work load, FetchQ takes care of those problems
for you! Just write your code using the informations that are carried by the `document`
object:

```
{ subject: '4b12d779-92a9-4fef-8486-94946f8abbb4',
  payload: { pizza: 'capricciosa' },
  version: 0,
  priority: 0,
  attempts: 1,
  iterations: 0,
  created_at: 2018-09-16T15:20:38.523Z,
  last_iteration: null,
  next_iteration: 2018-09-16T15:25:38.531Z,
  lock_upgrade: null
}
```

And implement the logic that you need. Mind that your function can be `async` or can
simply return a `Promise` in order to take care of any asynchronous job.

