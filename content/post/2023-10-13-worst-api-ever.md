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
- Each nested array represents a day in the upcoming week. So for example: If today is Friday, then the array at index 0 represents Saturday.
- Each boolean in the nested array represents whether that half-hour time slot is available or not. The boolean at index 0 represents 8 AM. 

I've attached a picture below to make the above explanation clearer:

![Worst API Ever](/Worst-API-Ever/Availability-Schedule.png)

The Android project used a tool to generate the client code and so there was no abstraction whatsoever. The UI code was working directly with this multi-dimensional array of booleans. 

You don't need me to tell you how ugly and complicated the code looked! There were for-loops all over to place to figure what the date was and what time slot each boolean corresponded to. 

I just hoped I never had to touch the logic but I did and debugging could easily take days! There were no unit or integration tests in the project either!

***

At the time, I was working for a software agency and sometimes we had a little extra time on our hands. 

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

Implementing the abstraction took one day and then it took another day to get the unit tests coverage up to 90+%.

After that, whenever I had time, I'd pick a screen in the booking journey to refactor! 

Getting rid of that mess felt _SO_ good! Suddenly the code became readable and easier to maintain. I just love replacing large swathes of code with a simple line or two.  The unit tests even brought to light a corner case bug though I can't remember what that was.

## Key Takeaways

I think this story highlights the importance of 2 things:

**Investing time to come up with the right abstractions**: 

This involves understanding the domain and its terminology so that you can use it correctly in the code. 

When I worked in Banking, studying the ISO 20022 standard really helped me come up with better abstractions.

Not all developers have worked in Banking, so I feel this story is a good one to help communicate developers the value of understanding and using domain terminology

**API-First development approach**

A discussion between backend and front-end on the API contract before any development would have been an efficient use of time. 