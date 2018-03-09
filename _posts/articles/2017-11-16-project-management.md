---
layout: post
title: "The importance of good estimations"
tagline: "Using context around delivery dates to convey risk"
image: /assets/patterns/paisley.png
header:
  image: /assets/patterns/asanoha-400px.png
tags: ["business"]
keywords: business
ref: post-example-in-english
lang: en
---

At my company, I was given the awesome career opportunity to take over as “technical product owner” of a medium-large software project.  I’d been the original… architect, for lack of a better word.  I’d taken mockups and user stories and translated them into work tickets with an opinion on how the work would be organized, worked, and delivered.  Thankfully, I was standing on the shoulders of giants: the last architect had left me with a document of recommended technologies, so it was a matter of connecting the dots.

About three months into the project, we hired a software architect and brought him on board the project.  This guy, “Andrew”, was a very intelligent programmer.  He developed a couple key pieces of infrastructure that enabled us to hit some tricky features.  He switched some of our technology around.  He was also late for every meeting, had terrible communication skills, and was generally unwilling to commit to the time it takes to steward this large project.  He was fired after four months.

Management took me and put me back in charge of the project and I’ve been running it, with the help of my own manager, pretty well for the last couple months.  We’ve oscillated between 5 and 10 engineers, though we’re slated to have 15 engineers on the project shortly.  Up until very recently, things had been going (largely) very smoothly.

## The problem:

Our competitor delivered the feature and were pitching it to our customers.  “Why would you use Company X, when we have this killer feature?”  Now the CEO got involved, and they need this feature by a trade show 5 months out.

Our company has a long history of making “existential threat” claims, but that’s an aside.

All of a sudden, we have this enormous pressure on delivery.  We’re given a project manager who is using a tool called JIRA Portfolio.  Portfolio will take every existing ticket, pointed or not, and construct an estimated delivery date.  You can use it to come up with scenarios: “What happens if we add X number of engineers?  What happens if we cut these features?”  The idea behind it makes a lot of sense.  Unfortunately, with software architecture, not everything is designed 100% up front.  At least not in an Agile company.

In my education, good architecture is a balance of determining how much design to do up front.  If it’s a very large project, you grasp the big pieces and identify where developers downstream can determine the implementation details.

So let’s say you design 40% of the project.  Create tickets, a bunch of placeholders, and start ramping up developers on the known work while you focus on the unknown work, reducing risk, etc.

Enter Portfolio.

Your project manager uses it to estimate the tickets and says, “Ok, we’re estimating this project to be completed in 3 months and 6 days.”  No.  No, no, no, no.  There’s so much _context_ around those dates.  Some areas of work are risky and will probably surface more work.  Some areas just haven’t even been decomposed at all.  Engineers don’t point everything, they point as they go.  That’s what’s good about Agile and allows you to pivot to the most important business needs.  However, when the entire project needs to be delivered at once (eliminating continuous delivery) then they end up demanding an estimate on the entire project.

You do your best to construct timelines that reflect degrees of confidence, but at the end of the day if management just wants a date, they'll only hear a date.  So they decide on resources, scope, and you are going to be held to that date.

When tickets are opened up (such as when engineers break a large ticket into smaller ones), Portfolio moves the estimated date a little later and everyone freaks out.

It’s just a bad time.  0/10 would not recommend.