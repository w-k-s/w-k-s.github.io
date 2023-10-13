---
title: "Worst.API.Ever"
date: 2023-10-13T17:21:00+03:00
tags:
- "Engineering"
- "APIs"
comments: false
toc: false
meta:
- tags
- date
draft: false
description: "Tech Debt stories need to have a plan"
---

Back when I was developing Android applications, the worst REST API I ever had to consume had a response that looked something like this:

```json
[
	[true,true,true,true,true,true,true,true,true,true,true,true,false,false,false,false],
	[false,false,false,false,true,true,true,true,true,true,true,true,true,true,true,true],
	[true,true,true,false,false,false,false,false,false,false,true,true,true,true,true,true],
	[true,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
	[],
	[false,false,false,false,false,false,false,false,false,false,false,false,false,false],
	[true,true,true,true,true,true,true,true,true,true,true,true,true,true,true,true],
]
```

Allow me to explain:
- This API was used to retrieve available time slots in the upcoming week.
- There was a nested array that represented each day in the upcoming week. So for example: If today is Friday, then the array at index 0 represents Saturday.
- Each boolean in the nested array represented whether that half-hour time slot was available or not. The boolean at index 0 represented 8 AM. 

I've attached a picture below to make the above explanation clearer:

![Worst API Ever](/Worst-API-Ever/Availability-Schedule.png)

The Android project was using a tool to generate the client code and so there was no abstraction whatsoever. The UI code was working directly with this multi-dimensional array of boolean. 

You don't need me to tell you how ugly and complicated the code looked! There were for-loops all over to place to figure what the date was and what time slot each boolean corresponded to. 

I just hoped I never had to touch the logic but I did and debugging could easily take days! There were no unit or integration tests in the project either!

***

At the time, I was working for a software agency and sometimes we had some extra time on our hands. 

We had a long term contract to maintain the application that consumed the API described above so I gave myself the task to add an abstraction layer. This is what I came up with:

```java
public interface WeekSchedule{
	@NonNull
	Set<LocalDate> getUpcomingDays();

	@Nullable
	DaySchedule getScheduleForDate(@NonNull LocalDate date);
}
```

```java
public interface DaySchedule{
	@NonNull
	Collection<TimeSlot> getTimeSlots();

	boolean isWeekend();

	boolean hasAvailabeSlots();
}
```

```java
public interface TimeSlot{
	@NonNull
	LocalDateTime getStartTime();

	@NonNull
	LocalDateTime getEndTime();

	boolean isAvailable();
}
```

Implementing the abstraction took one day and then it took another day to get the unit test coverage up to 90+%.

After that, whenever I had time, I'd pick a screen in the booking journey to refactor! 

Getting rid of that mess felt _SO_ good! Suddenly the code became readable and easier to maintain. The unit tests even brought to light a corner case bug though I can't remember what that was.

## Key Takeaways

I guess the key takeaways are:

1. It's worth taking the time to come up with the right abstraction.

2. I guess another takeaway could be to document the process that I'd recommend other developers to follow if they have a similar refactor they want to do in a project they're working on. 

	Not sure if this is correct or even useful, but I'll write it anyway.

**Step 1: Understand the requirement of the API** 

- Look at the API and try to express in human understandable language what it's trying to do.
- In the example above, we saw that the API provides the **available** **timeslots** for the **upcoming week**

**Step 2: Design the abstraction** 

- At this stage, design the abstraction using the terms you identified in the previous step.

**Step 3: Validate the abstraction** 

- Based on your experience  using the API, determine if the abstraction you've come up with would actually make the code more readable and self-documenting.

**Step 4: Present the proposal to the team**

- For larger refactors, it's a good idea to present your proposal to the wider team so that they can validate that it will work on a wider scale + they can provide additional feedback.

**Stage 5: Write an ADR**

- Once your team has agreed on a strategy, document the plan in [ADR](https://adr.github.io/)

**Stage 6: Create a tech debt ticket**

- I've had several experiences where developers identify code that needs to be refactored, so a tech-debt ticket gets added in JIRA.
- These tickets are woefully devoid of detail and strategy. 
- The refactors are poorly thought-out, implemented in a rush and in the end don't provide meaningful improvements.

- However, In the process described above, a tech debt ticket is **NOT** created until you have a clear strategy documented,  presented and agreed upon by the team.

**Stage 7: Implement & Test** 

- Finally, implement the abstraction but make sure you have unit tests in place to make sure you don't break anything.
- The [Refactoring book by Martin Fowler](https://martinfowler.com/books/refactoring.html) covers this step really well.