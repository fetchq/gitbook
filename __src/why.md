# Yet another queue system?

Yes, indeed. When I feel the urge to write some code... I google first. And I did. A lot.

I tried different systems:

- [RabbitMQ](https://www.rabbitmq.com)
- [AWS SQS](https://aws.amazon.com/sqs)
- [celery](http://www.celeryproject.org)
- countless NPM modules

> I experienced some problems...

My problem was simple: I had to scrape the _WebsiteXXX_ which hosted _Iron Man_'s profile.
And **I had to scrape _Iron Man_'s info every second day** because the guy's so cool and his
profile changes a lot.

> **repetitive tasks** was problem n.1

Every time my worker did scrape _Iron Man_'s profile, it found references to other super heroes:
_The Hulk_, _Captain America_, _Black Widow_, ... and of course I wanted them too. Soon enough
I noticed that **they were often referencing each others**, so duplicates begun to appear in
my solution. Ouch! My system was doing the job more than once every second day!

> **unique tasks** was problem n.2  
> (with RabbitMQ you need to use a Redis - or similar - server to check uniqueness before pushing)

Some time went by and my system did work well. Too well. So much "well" that **it founds millions of
superheroes**. Damn, they are everywhere! Soon enough **my server could not cope with the amount of
profiles it needed to check** every second day. There was not enough time (given _WebsiteXXX_ is so slow).
At this point I figured: _"why don't just start copied of my system on multiple machines?"_ 
Best idea ever, easy to think, easy to do (just throw money at AWS, right?). Few minutes into 
horizontal scaling I noticed that my many machines were all processing the same super hero!

> **exclusive distributed tasks**  was problem n.3  
> (all the existing systems solved this problem quite well)

Once I solved the concurrent access problem I was set for success. Or so I thought. It was about
Christmas time so my optimism was sky-rocketing. Well, well, well... After a quick check I found
out that _Iron Man_ removed his profile from _WebsiteXXX_ but my system kept trying to access
the profile, getting a non surprising `404` back. Then I thought: _"shouldn't the system be
smart enough to stop trying?_ Of course it should be! But AI/ML is so expensive so I simply

> **task failure threshold** was problem n.4

There have been many other small and big challenges in getting toghether this _Postgres_ extension,
performances are always on top of my mind and I am now investigating new ways of using _table
inheritance_ and _data partitioning_ to create an even more efficient system.

But at its core _Fetchq_ is simply a stable queue system that can run millions of documents in a
very small small machine, with a memory footprint that is basically ridicolus.
