---
title: "Why is this codebase such a mess?"
date: 2023-12-24T00:00:00+03:00
categories:
- "Engineering"
comments: false
toc: false
meta:
- categories
- date
draft: false
---

When I first started working professionally as a Software Engineer (an Android developer, to be specific), I worked on a codebase that would read configurations from an XML file. I would say that 90% of the crashes in the app were because of the optimistic way the XML file was parsed.

I asked my team lead, "Who wrote this? I'm always fixing this," to which he replied, "He was a talented guy. He just didn't have much time."

Back then, this answer wasn't good enough. Now that I'm older and I've had experience managing teams and projects, I want to write a more detailed answer to this question — something that would have convinced me back then.

### 1. Time Constraints

By far, the biggest reason for the codebase being a mess is time constraints. The developers had to deliver a feature or fix a bug by a certain date, or there would be serious trouble.

We can all understand that. But I want to put this into perspective.

Although we love to code, we also want to get paid. In order to get paid, most of us will look for a job. For that job to pay us, they need to attract business.

So to attract business, a company will reach out to a marketing company and work with them to prepare a campaign. The marketers will ask: "When do you want to go live with this?" and your boss will be like, "Let me ask the project manager when the feature will be ready." The project manager will ask the developers, and the date will go back to the marketers.

Depending on how big the marketing campaign is, there might not be much wriggle room on that date. Especially if there's something like a launch party, and all the executives are going to be there.

**TL;DR**: Having the demo ready in time for the big Launch Party > SOLID Principles.

For start-ups, it's not just about being the first one and capturing the market share. It's about being the first and not letting others catch up. Even when you launch, you'll keep needing to rush the features out to make sure you stay ahead of the competition.

That technical debt that you said you'll fix after go-live: Yeah, that's probably not gonna happen.

### 2. Consistency

So I joined a project where the existing APIs look something like this: `POST /createThing`, `GET /getThing`.

I had to add to design the APIs for upcoming features in the project. So the question is: did I go with: 

- [ ] A. REST Maturity Level 2 (e.g. `POST /newThing`, `GET /newThing/1`) 
- [ ] B. Stick with REST Maturity Level 0

I went with Option B. The reason for this was consistency. The codebase might not be the most beautiful thing in the world, but following a consistent pattern at least makes the codebase intuitive to work with and cuts down on the time wasted where you thought, for example, the User API is implemented in `UserService` but no, it's implemented in `GetUserByIdUseCase` because that part of the project was developed by a different team/developer.

- **TL;DR**: Consistency reduces time-wastage, which helps with the time-constraint point mentioned above.

- **Note**: This doesn't mean always go with the flow and don't refactor ever. It just means that refactoring needs to be done strategically by the team (e.g., by using the Strangler Pattern) rather than willy-nilly by a single developer.

- **Note**: Another reason I went with Option B is that the application didn't have an API Gateway. It needed one, though, so I figured I could just map the endpoints to RESTful Resources at the Gateway level.

### 3. Working with the team rather than against them

Speaking of `UserService` and `UserUseCase`; More recently, I was put in charge of greenfield project with a tight deadline (6 Months). I wanted the code and repo setup so that when the team got back from the holidays, they could hit the ground running.

As such, I had to make a few decisions. One of them was to choose between using Ports and Adapters vs. a Layered architecture. 

For context, there had been a number of presentations on Ports and Adapters Architecture at the company and there were developers who were keen to put it to practice. 

The team I was leading consisted of 2 seniors and a junior developer. None of them had ever used Ports and Adapters and one of them was particularly keen on it. 

Again, I chose the boring option: Layered Architecture. The reason for this is because of a nightmare that played in my head: the time spent by the team on PR debates on how Ports and Adapters should be done properly and then time spent researching and reworking. 

I figured: 
1. The whole team is familiar with layered architecture (as well as people who might join in the future)
2. I want to educate the team on how to do layered architecture correctly first. Then they'll be in a better position to explore Ports and Adapters later on in their careers.

I feel I made the right decision: the team improved on what they already knew rather than struggling with completing tickets while working in an unfamiliar architecture at the same time.

What's more: we didn't have those huge Service classes, because the team learned to avoid the anaemic domain anti-pattern as well how to apply the single responsibility principle to service classes.

**TL:DR 1**: Educate your team with the knowledge and tools to refactor. Don't dump them in unknown territory.

**TL:DR 2**: If you've come upon a messy codebase, your first step is to discuss improvements with the team. Not to dictate.

**Note:** I just want to add: When the team member who was keen on Ports and Adapters asked why we wouldn't use it, my answer was, "When you're responsible for the consequences of the decision, you can pick any architecture you want." I think that nicely sums up the thought-process.

## Conclusion

**Why is the codebase a mess?**
In the pursuit of business goals, things had to be rushed.

**What can you do about it?**
Find a part of the code that would be _valuable_ to refactor and start a discussion with your team on how to go about it.

**What shouldn't you do about it?**
Take it upon yourself to refactor bits of the project as you see fit.