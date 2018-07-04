# Getting Started

This tutorial will guide you through the setup of a _Fetchq_ project, you are going to use
_NodeJS_ and _Docker_ to set this up.

## Setup the database

The first step is to boot a _Postgres_ instance that can run the _Fetchq_ extension.

_Fetchq_ crew maintains an image on _Docker HUB_ that is basically _Postgres_ with the
_Fetchq_ extension's files shipped within.

(@TODO: For more details take a loot at the _Docker_ distribution)

I personally like to run it with _Docker Compose_ as it feels easier to modify and 
reproduce across my projects.

```
# docker-compose.yml
version: '2.1'
services:
  postgres:
    image: fetchq/fetchq:9.6-1.2.0
    ports:
      - 5432:5432
    environment:
      POSTGRES_USER: fetchq
      POSTGRES_PASSWORD: fetchq
      POSTGRES_DB: fetchq

```

When you run this file with `docker-compose up` a brand new Postgres instance should be
up and running on your development box in minutes.

## Basic SQL queries

Let's connect

## Setup the Node app