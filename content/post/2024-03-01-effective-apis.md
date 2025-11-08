---
title: "Effective APIs"
date: 2024-03-01T00:00:00+03:00
categories:
- "Engineering"
- "APIs"
comments: false
toc: false
meta:
- categories
- date
draft: true
---

Points to remember in order to build robust APIs.

## 1. Validate date exists, not just the format.

- Most date format validators only verify the structure of a date, but not the validity of the date itself. 
- In other words, the date `1989-02-29` would pass validation even 1989 is not a leap year.
- Therefore, it's a good idea to parse the date string into a date type and respond with a descriptive error in case an Exception is thrown.

## 2. Storing absolute URLs for images in the database

- Typically images are uploaded to an object store (S3 for example) and exposed through a content delivery network (e.g. cloudfront).
- What I've seen in a few projects is the cloudfront or S3 URL of the image is stored in the database.
- Caution needs to be exercised with this practice: it is possible that the data might be migrated from one object store to another; or exposed through a different CDN in order to reduce costs. 
- In such cases, developers would need to create a migration script to update the base URL of each image. Personally, I'm not a fan of this. I feel that replacing URLs is a fincicky operation. For example: you could end up with two slashes instead of one, or no slahes where there was supposed to be one. The task becomes tricker when you have a lot of data and the URLs stored might not all be the same format.
- I believe the base URL should be sourced from a configuration file and concatenated when selecting the database row. The file name stored is a lot easier to validate (e.g. just ensure there is no slash at the beginning.)

    ```sql
    SELECT CONCAT('https://cheapasscdn.com/AAF7-109EF9D259E/images/', avatar_file) AS avatar_url
    FROM user;
    ```
