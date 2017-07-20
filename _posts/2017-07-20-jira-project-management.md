---
layout: post
title:  "JIRA project management"
categories: business
---

JIRA is a great tool for project management, but drawing the lines between JIRA projects correctly leads to painful collaboration across teams.

At my company, we have two JIRA projects: Monolith (`MONO`) and Service (`SVC`).

`MONO` comprises 2 scrum teams that work across 3 legacy codebases.

`SVC` comprises a third scrum team that only works on a new API codebase: Dragonfruit.

To no surprise, `MONO` and `SVC` have different processes for things like stand-up, testing, code review, and demos.  However, the legacy codebases are starting to depend on Dragonfruit.  When we need Dragonfruit work commissioned, we write tickets in the `SVC` project and wait until they've been groomed, pulled into a sprint, and completed to un-block `MONO` tickets.

Obviously, this is not a good system since it requires an almost waterfall approach where we as `MONO` developers turn into POs for the `SVC` project.  If the requirements change, the scope of the `SVC` sprint changes.

The best solution seems to be allowing `MONO` developers to work in the Dragonfruit codebase but that messes up the `SVC` scrum team's demos and testing cycles, since they "own" that codebase.  Unexpected changes to the API, potential breaking changes, that kind of thing.  We could permit these breakages, in my opinion, if it led to faster development if and only if the two codebases have the same milestones.

Unfortunately, given that they are two JIRA projects they cannot share the same milestone reporting metrics ("Fix Version").  When new work is discovered in `MONO` and ticketed up in `SVC`, the estimated date of milestone completion in `SVC` gets pushed back while it remains the same in `MONO`.  In our company, this is illogical when you consider that Dragonfruit has no business value except to support milestones in `MONO`.

Where am I getting with this?  Oh yes, a solution.

Draw JIRA projects across milestones, ideally no more than two or three at a time.  A scrum team should span projects and can have that number of sprints in progress at a time (or, if possible, let a sprint comprise tickets across projects).  When a milestone finishes, archive and close the project.  When projects are "owned" by one team then all other teams must conform to that team's process, and when projects are tied to a codebase then business-value milestones must be arbitrarily split and tracked separately.