## <small class="new-row">[&laquo; Getting Started](./README.md)</small> 02 Initialize Fetchq

First read:   
[Setup Postgres &raquo;](./01-setup-postgres.md)

---


So far you have a plain _Postgres_ running, as it comes out from the factory. Fetchq extension
files are shipped within the container, but the extension itself needs to be created:

```
CREATE EXTENSION IF NOT EXISTS "fetchq";
```

You may also want to create the extension `uuid-ossp` which is a core extension used to generate
unique document names, useful for appending unique documents into a queue.

```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

At this point you are ready to initialize _Fetcqh_ and create the basics _schema_ for your database:

```
SELECT * FROM fetchq_init();
```

Test your installation by listing the tables in the _public schema_:

```
SELECT * FROM pg_catalog.pg_tables WHERE schemaname = 'public';
```

![pg-tables](./02-pg-tables.png)

---

Next step:  
[Create a new queue &raquo;](./03-create-queue.md)
