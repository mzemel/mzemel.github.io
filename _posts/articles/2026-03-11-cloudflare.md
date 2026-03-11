---
layout: single
title: "What I've Been Up To at Cloudflare"
tags: ["career"]
keywords: career
lang: en
category: articles
---

I joined Cloudflare in April 2024 as a Systems Engineer. It's been almost two years now, and I thought I'd write down what I've been working on -- mostly so I can look back on it later.

## Building a resource sharing API

My first project was helping build a Go API from the ground up. The service lets enterprise customers share configurations -- like security policies -- across multiple accounts within an organization. I implemented most of the initial CRUD endpoints, wrote integration tests, set up authentication and authorization, and added filtering, sorting, and pagination. It was a good way to get familiar with Cloudflare's infrastructure.

Over time, I got more involved with the operational side of the service: setting up Grafana dashboards, writing production monitoring tests, improving error handling in our async workflows, and fixing bugs that cropped up as real customers started using it. One recurring theme was dealing with stuck asynchronous jobs and figuring out how to surface useful error information to the frontend without breaking retry logic.

## Enterprise organization features

More recently, I've been working on Cloudflare's enterprise organization and tenancy layer. This has been a mix of backend and some dashboard work:

- Adding security checks to enforce that users have adequate security postures (like two-factor authentication) before they can be granted certain administrative roles
- Tightening access controls for sensitive internal administrative operations, particularly around managing organization membership
- Working on account lifecycle features, like blocking unsafe account moves when shared resources are involved
- Helping integrate support flows into the dashboard UI

## Day-to-day

Most of my day-to-day is writing Go, reviewing code, deploying services, and occasionally touching the TypeScript dashboard. I spend a fair amount of time on production readiness: making sure services have proper alerting, that error handling is sound, and that we're not silently failing in ways that affect customers.

I've been lucky to work with a small, capable team. I'm still learning a lot.
