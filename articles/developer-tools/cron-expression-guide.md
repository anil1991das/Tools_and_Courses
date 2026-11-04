---
title: "Cron Expressions Explained: Schedule Jobs with Five Fields"
description: "Understand the five fields in a cron expression, learn common schedule examples, and check important timezone and day-of-week details."
slug: "cron-expression-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Developer Tools"
tags:
  - "Cron"
  - "Scheduling"
  - "Linux"
  - "Automation"
relatedTools:
  - title: "Cron Expression Generator"
    url: "/cron-generator"
relatedArticles: []
draft: false
---

# Cron Expressions Explained: Schedule Jobs with Five Fields

Cron is a time-based job scheduler commonly used on Unix-like systems. It can run commands at recurring times, such as every hour, every weekday morning, or on the first day of a month.

A cron expression describes the schedule. It is compact, but the meaning of each field must be understood before using it in a production system.

The [iamnoobs Cron Expression Generator](https://iamnoobs.com/cron-generator) lets you fill five schedule fields, choose common presets, and generate an expression with a basic explanation.

## What is a cron expression?

A common crontab schedule contains five time-and-date fields followed by the command to execute.

~~~text
minute hour day-of-month month day-of-week
~~~

For example:

~~~text
0 9 * * 1-5
~~~

This expression means minute 0 of hour 9 on weekdays, subject to the cron implementation's timezone and day-field rules.

## Understand the five fields

| Field | Typical range | Meaning |
|---|---:|---|
| Minute | 0–59 | Minute within the hour |
| Hour | 0–23 | Hour of the day |
| Day of month | 1–31 | Calendar day of the month |
| Month | 1–12 | Month of the year |
| Day of week | 0–6 | Weekday, commonly 0 for Sunday through 6 for Saturday |

Some cron implementations also accept names, extensions, or additional fields. Always check the scheduler that will execute the expression.

## Special characters used in cron

| Character | Common meaning | Example |
|---|---|---|
| Asterisk (*) | Every allowed value | Asterisk in the hour field |
| Comma (,) | A list of values | 1,3,5 |
| Hyphen (-) | A range | 1-5 |
| Slash (/) | A step interval | */15 |

These operators are widely supported in standard cron syntax, but extensions vary between products and schedulers.

## Common cron examples

| Expression | Intended schedule |
|---|---|
| * * * * * | Every minute |
| */5 asterisk asterisk asterisk asterisk | Every five minutes |
| */15 asterisk asterisk asterisk asterisk | Every fifteen minutes |
| 0 * * * * | At the start of every hour |
| 0 0 * * * | Every day at midnight |
| 0 9 * * 1 | Monday at 09:00 |
| 0 9 * * 1-5 | Weekdays at 09:00 |
| 0 0 1 * * | At midnight on the first day of each month |

The examples use a conventional five-field cron format. The scheduler's timezone determines what local time they represent.

## Generate an expression with the online tool

1. Open the [iamnoobs Cron Expression Generator](https://iamnoobs.com/cron-generator).
2. Choose a preset or enter values for minute, hour, day, month, and weekday.
3. Select **Generate**.
4. Review the generated five-field expression.
5. Read the basic explanation and compare it with the intended schedule.
6. Test the schedule in the actual scheduler before deploying it.

The current tool joins the five input fields and provides a simple text explanation. It does not fully validate every field's range or every cron extension. Treat the generated expression as a starting point and validate it against your target scheduler.

## Important detail: day of month and day of week

Traditional cron has a subtle rule when both the day-of-month and day-of-week fields are restricted.

On common Vixie-style cron implementations, if both fields contain specific values rather than an asterisk, the job runs when either field matches. This is not the same as requiring both conditions to match.

For example, a schedule specifying a particular day of the month and a particular weekday can run on dates matching either condition. Confirm the exact behavior of your cron implementation before using such a schedule.

## Timezones and daylight saving time

Cron schedules are interpreted by the scheduler, not by the generator. The timezone may come from the machine, a service-level setting, or a container configuration.

Daylight saving time can also affect schedules in regions that observe it. A local clock time may be skipped when clocks move forward or may occur twice when clocks move backward. Some schedulers handle these cases differently.

For critical jobs:

- Confirm the scheduler's timezone.
- Document whether the intended schedule is local time or UTC.
- Check daylight-saving behavior where applicable.
- Monitor job execution rather than assuming that a matching expression guarantees success.

## Cron expression versus the command

A crontab entry has more than a schedule. It also contains the command and possibly a user field, environment settings, or scheduler-specific syntax.

For example:

~~~cron
0 2 * * * /usr/local/bin/backup.sh
~~~

The first five fields define when the command is scheduled. The remaining text is the command to execute.

A valid schedule does not guarantee that the command succeeds. The script may need an absolute path, environment variables, permissions, logging, and error handling.

## Common mistakes

### 1. Confusing hour with minute

The first field is minute and the second is hour. The expression **30 8 * * *** is 08:30, not 30:08.

### 2. Forgetting the scheduler timezone

An expression for 09:00 runs according to the scheduler's configured timezone, which may not match the timezone of the person who wrote it.

### 3. Assuming every scheduler uses identical syntax

Cloud schedulers, Kubernetes CronJobs, Quartz, and traditional Unix cron may use different formats or extensions. Some use six or seven fields. Do not copy a five-field expression into another scheduler without checking its documentation.

### 4. Assuming a generated expression is fully validated

The current generator provides a lightweight expression builder, not a complete standards validator. Verify field ranges and special syntax independently.

## Frequently asked questions

### What does an asterisk mean?

In a cron field, an asterisk generally means every allowed value for that field. For example, an asterisk in the hour field means every hour.

### What does a step value of */10 mean?

In a field that supports step values, */10 means every ten values within that field's range. In the minute field, it commonly means minutes 0, 10, 20, 30, 40, and 50.

### Does cron run a job immediately when I save the expression?

No. Cron evaluates schedules according to its polling and scheduling behavior. A newly added entry normally waits for the next matching scheduled time.

## Further reading

- [Linux man-pages: crontab(5)](https://man7.org/linux/man-pages/man5/crontab.5.html)
- [Cronie project documentation](https://github.com/cronie-crond/cronie)

---

**Try it:** [iamnoobs Cron Expression Generator](https://iamnoobs.com/cron-generator)
