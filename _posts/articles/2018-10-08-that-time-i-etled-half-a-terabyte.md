---
layout: post
title: "That time I ETL'd half a TB"
image: /assets/patterns/paisley.png
header:
  image: /assets/patterns/asanoha-400px.png
tags: ["business"]
keywords: business
ref: post-example-in-english
lang: en
---

Over the last year and a half, I worked on a team delivering a microservice that split off responsibility around managing email recipients and recipient lists from our monolithic application.  While there were challenges during development, we were able to push through them and communicate important decisions and deadlines to the rest of the team.  Largely, we were able to make our deadlines for delivering this new system.

What we didn't plan for was how to move all of our existing customer data into it.

The two database schemas were fairly different.  Instead of multiple instances of each recipient (one for each list they're on) we now had a single record for a recipient with a new table to track where they're used, some details about them, responses or actions they've taken, and other peripheral tables.  We could not simply dump each old table (we were replacing about 10 tables in the old system) and write to the tables in the new one: the data had to be transformed to fit the new schema.

We had two options: 1) transform the data during a dump on the source into the expected schema of the target, or 2) use the new system's CRUD API to create everything over HTTP.  We opted for the latter, and this turned out to be a big mistake.

First, there was a lot of data.  More data than we anticipated our customers would be sending, so we had network timeouts, slow queries, pegged CPU, memory issues, and I think more than once we tanked production by overloading the database.

Second, we had this (relatively insane) product constraint - we had to message customers at least 2 weeks before we switched them over to the new system.  This meant that we either had to be air-tight that they would be able to "migrate", or we had to "test run" the migration process, sift through the successes, and send out notifications to those customers.

With each iteration, we'd have additional successes and have to create blocks of time when we'd migrate these cohorts.  Since we had staggering groups pass, we had many staggering windows during which we could migrate customers.  And, almost more often then not, they'd pass the test run but fail the actual migration - either the schema had changed, or we ran into network hiccups that dropped a record, or the validation plan we came up with was fragile..

We tried to get this notification requirement removed, since it meant we'd be able to quickly iterate on a company.  It fails?  Ok, fix it and retry.  But since it was baked into the contract and we had some angry customers in the past, that idea was squashed by the CEO.  This meant that each failure meant we'd have to re-notify the company and push it out another two weeks.  And the CEO wanted this whole project done yesterday because the system was built and why weren't customers all moved over to it?

There is a light at the end of the tunnel.  We fixed the network issues (10 minute timeouts), we fixed the slow queries (indexes, fine-tuning the concurrency and hardware), we fixed the validation errors (guessing correctly in the source what will be valid in the target), and we're slowly getting better success rates with each attempted block of migrations.  That's good, because there's mounting pressure from virtually everyone in the organization to get it done.  If it is done within a month from today, I'll still consider this a herculean success.

In retrospect, we should have gotten a copy of the production database and kept iterating on that until everyone passed a "test run" using production hardware.  Then we send out a notification to everyone and be sure it'll pass.  Ultimately, this came down to testing in production which we should have realized was a bad idea.

When every company is successfully migrated, we'll delete the code I've worked on for the last two months and it'll be like this never happened at all.