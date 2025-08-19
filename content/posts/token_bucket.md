---
title: "Algorithms 1: Token Bucket"
date: "2025-04-19T13:46:12+01:00"
Description: ""
Categories: ["Algorithms"]
Author: "Lmbf"
draft: true
toc:
  enable: false
---

The token bucket falls into a class of algorithms usually known as rate-limiting algorithms, since they allow a server to process requests at a controlled rate. The way this algorithm works is fairly simple: 

* We have a bucket that holds a maximum number of tokens and each token represents permission to perform a request;
* Tokens are added to the bucket at a constant rate;
* When a client wants to make a request, they need to take a token from the bucket. If there are no tokens available, the request will be rejected. If there are tokens available, the number of tokens is updated and the client's request goes through the remainder of the system.

There are a few considerations when implementing this:
1. How many tokens does each request consume
2. How do we store the current number of tokens
3. How do we handle concurrent requests
4. What rate do we want our bucket to fill
5. How many tokens should the bucket hold
5. How do we want to handle traffic spikes

For simplicity, let us assume that the answer to `1.` is a single token per request. 

As for `2.`, we have to consider our options. If our token bucket is simply storing the number of available tokens at a given time, then the bucket has to be filled asynchronously. But if the bucket is filled asynchronously, then each incoming request has to place a lock on the number of available tokens, and so much the bucket filling process. For fast filling buckets, this leads to lock contention, which will ultimately result in not being able to process requests at an appropriate speed. For this reason, it is preferable to store the number of tokens available as well as the timestamp of the previous request.

While we can take up the task of handling concurrency ourselves, it is usually better to let dedicated systems handle this task. A classical relational database such as Postgres might not be the best option since disk accesses/writes would result in considerable added latency to each request. As such an in-memory cache is a more suitable option due to its speed and time-based expiration strategy support (Redis is a popular choice for this).

Questions `4` and `5` can be answered in tandem. It all depends on what business logic are we attempting to rate limit. If we are refering to login attempts, we might want the bucket to have a slow refill rate (on the magnitude of minutes) with a fairly low maximum bucket capacity per user. On the other hand, we if are applying this to an API that handles thousands of requests per second, we will require a much faster refill rate, and possibly different limits and refill rates depending on the type of user (*i.e.*, free user *vs.* premium user).

Last but not least, we have to make a consideration on how we want to handle traffic spikes. My personal preference here is to let the token bucket handle traffic as is but also set up a metric for the amount of rejected requests. This metric would then have an alert associated and a way to perform a manual override to the bucket refill rate, in case the surge in traffic we are witnessing is legitimate traffic (*e.g.*, Black Friday traffic spike). But that's more of a system design question.


