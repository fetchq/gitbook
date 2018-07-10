# Welcome to Fetchq

`fetchq` is a **queue system** that we think is easy to integrate in your existing project:

- fetchq is 70% a Postgres extension
- fetchq is 15% a Client library
- fetchq is 15% a REST server

We are using `fetchq` in **Real Life <sup>(tm)</sup>** projects to handle queues up to **60 millions unique repetitive tasks** (web scraping) with throughputs of thousands of jobs / minute, where we have to deal with unreliable HTTP requests, 
**horizontal scaling across 70 workers server** (real concurrent access) and the constant fight for ever changing APIs of our targets (which I'm not going to disclose here).

