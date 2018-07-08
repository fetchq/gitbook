# Welcome to Fetchq

`fetchq` is a **queue system** that we think is easy to integrate in your existing project:

- fetchq is 70% a Postgres extension
- fetchq is 15% a Client library
- fetchq is 15% a REST server

We are using `fetchq` in production to handle queues up to **60 millions unique repetitive
tasks** (web scraping) with throughputs of easily thousands of jobs / minute, in a 
**real life project** where we have to deal with unreliable HTTP requests, **horizontal scaling 
across 70 workers server** (real concurrent access) and the constant fight for ever changing 
APIs or our targets (which I'm not going to disclose here).

