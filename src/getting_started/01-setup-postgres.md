# Setup Postgres database

The first step is to boot a _Postgres_ instance that can run the _Fetchq_ extension.

_Fetchq_ crew maintains an [image on _Docker HUB_](https://hub.docker.com/r/fetchq/fetchq/) that is
basically _Postgres_ with the _Fetchq_ extension's files shipped within.

## Run With Docker...

To quickly run _Fetchq_ you can copy paste this command:

```
docker run --name fetchq -p 5432:5432 fetchq/fetchq:9.6-1.3.0
```

## ...or With Docker Compose

I personally like to run it with _Docker Compose_ as it feels easier to modify and 
reproduce across my projects. Try this snippet in your `docker-compose.yml`:

```
# ./docker-compose.yml
services:
  postgres:
    image: fetchq/fetchq:9.6-1.2.0
    ports:
      - 5432:5432
    container_name: fetchq
    network_mode: bridge
```

`container_name` and `network_mode` are important for the successful execution of the `psql``
commands in this page.

## Connect to the database

This will start _Postgres 9.6_ with the default configuration as described in the
[Postgres's Docker Hub page](https://hub.docker.com/_/postgres/).
Anyway use "_postgres_" as setting for _username_, _password_ and _database_ **when you connect**
using _psql_ or a visual client such [Postico](https://eggerapps.at/postico/).

> Here I'm going to use _Docker_ to run _psql_ and connect to the database.

```
# Start a psql session:
docker run -it --rm --link fetchq:postgres postgres psql -h postgres -U postgres

# Launch a single query:
docker run -it --rm --link fetchq:postgres postgres psql -h postgres -U postgres \
-c 'SELECT * FROM current_database()'
```

**IMPORTANT:** from now on I will only write _SQL QUERIES_, assuming that you will run them
with the "single query" command. But suits yourself and use whathever method you like best!
