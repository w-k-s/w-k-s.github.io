---
title: "Distributed Microkernel Architecture"
date: 2023-03-27T00:00:00+03:00
tags:
- "Engineering"
- "Microservices"
comments: false
toc: false
meta:
- date
- tags
draft: false
---

We were contracted to build an appointment booking system that enabled hauliers (truckers) to pick up containers assigned to them from a sea port.

The tricky bit was that the contracting company operated a number of ports, and for legacy reasons, different ports had different strategies to assign a container to be picked up by a particular haulier.

For example: in one port, you'd assign a haulier by entering their username. In another port, you might share the booking reference.

In whatever way containers were assigned to hauliers, an booking had to be created in the backend so that it would show up at the port gate.

## High Level Solution

The solution that I had in mind was that we would have a "core" booking microservice which was only concerned with who's picking up which containers and when. This service offered a consolidated view of all bookings.

We would then have any number of "plugin" microservices for each port. These plugin microservices would implement the way hauliers are nominated at that particular port and then pass the necessary details on to the core booking service.

The architecture is illustrated in the diagram below:

![Microkernal Architecture Diagram](/Distributed-Microkernal-Architecture/Architecture-Diagram.png)

The Core system would use a Document Store (e.g. MongoDB) since different nomination systems at different ports meant different attributes needed to be stored.

## Outcome

Was this proposal implemented? Nope!

I did share this proposal with the senior architect who responded very positively to it and asked me to document the finer details.

However, at the time, I had never heard of the "Microkernel" or "Plugin" Architecture and I thought this was a novel solution to the problem; that made me nervous. I had a habit of second-guessing myself so I didn't pursue the idea further.

In retrospect, I wish I had. No solution is perfect, but I think the key thing about this solution was that it was relatively easy to understand and maintain. I also think I ought to have discussed the idea with other members of the team; I'm sure that if anyone of them had mentioned that this architecture has a name, it would have given me a lot more confidence.