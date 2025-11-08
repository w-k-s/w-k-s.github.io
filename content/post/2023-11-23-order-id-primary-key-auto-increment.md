---
title: "order_id PRIMARY KEY AUTO INCREMENT"
date: 2023-11-23T00:00:00+03:00
categories:
- "Engineering"
- "Databases"
comments: false
toc: false
meta:
- categories
- date
draft: true
---

Some time back, I had a system design interview. One of the questions that I was asked was:

> "How would you assign unique IDs in a distributed event processing system."

I proposed UUIDv7, however the interviewer didn't agree. 

Then I suggested Snowflakes but nah, he didn't like that either.

After the interview, I did a deep dive in [Distributed Sequencers](https://github.com/w-k-s/Distributed-ID-Generators-Experiment) and:

**1.** I discovered the correct answer is Vector Clocks.

**2.** While investigating what the applications that I use regularly use for unique IDs, I stumbled on something interesting. That's what the rest of this article is about.

---

So there's this app that I used to use about once a month. When I reviewed my order history, I noticed that the order id always seemed to increase and it seemed to increase by the same amount each month. 

I did some calculations on it and saw a pattern. 

For the sake of this article, I actually put the numbers on an excel sheet with formulas to calculate the rate of increase of order id numbers.

|    Date    | Order Id | Days since last order | Difference in order id | Rate of change | Average increase in order id |
|:----------:|:--------:|:---------------------:|:----------------------:|:--------------:|:----------------------------:|
| 2023-03-12 | 36825670 |                     0 |                      0 |              0 |                  6275.547183 |
| 2023-04-12 | 37017817 |                    31 |                 192147 |    6198.290323 |                              |
| 2023-06-03 | 37403289 |                    52 |                 385472 |    7412.923077 |                              |
| 2023-06-25 | 37546928 |                    22 |                 143639 |    6529.045455 |                              |
| 2023-07-28 | 37691338 |                    33 |                 144410 |    4376.060606 |                              |
| 2023-08-08 | 37772159 |                    11 |                  80821 |    7347.363636 |                              |
| 2023-09-12 | 37974795 |                    35 |                 202636 |         5789.6 |                              |

The conclusion I came to is:

1. This application has applied `AUTO INCREMENT` on the `id` field on their `order` table.
2. In the first half of 2023, they processed roughly 6500 orders per day.

But here's what I find interesting. Supposing I was an investor who used this app and noticed the same thing. I could simply keep adding the dates and order ids to this excel sheet to see if the company was actually increasing its order volume or not.

If the average stayed the same, I might decide to not invest any more thinking that the company is having trouble scaling their operations...

...All because a developer decided to use `AUTO INCREMENT` on the `id` field!

---

Notes:

1. The dates and ids have been modified in this post, but the average is roughly the same.
2. If you've done something similar in your project and want to fix it, your best bet would be to consider using [Snowflakes](https://github.com/callicoder/java-snowflake).