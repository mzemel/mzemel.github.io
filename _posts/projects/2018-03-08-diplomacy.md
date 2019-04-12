---
layout: single
title: "Gunboat Diplomacy"
header:
  overlay_image: /assets/img/diplomacy_header.jpg
  caption: "HistoryGamerDotCom"
  actions:
    - label: "GitHub"
      url: https://github.com/mzemel/gunboat_diplomacy
tags: ["games"]
keywords: games
lang: en
category: projects
toc: true
excerpt: Who just stabbed me?
---

## What is Diplomacy?

I love board games, especially ones where little to no luck is involved.  Diplomacy, similar to Risk, situates you as a superpower bent on world domination.  The key difference is, instead of rolling dice, all of your moves succeed or fail depending on support from your allies (other players).

Players will have a brief "diplomacy phase" where they quibble and meet in secret, then write down their moves secretly on a sheet of paper.  All moves are resolved at the same time, meaning some will conflict, and may vary from what you'd told someone you would do.

Alliances are constantly forming and breaking, and your skill as a player is entirely based on your ability to inspire cooperation and betray your allies at the right time for sole victory.

<img src="assets/img/diplomacy.png" />

## What is Gunboat?

The "diplomacy phase" I mentioned earlier represents the majority of the time spent on a game, and the average game lasts about five or six hours.  One popular variant, Gunboat, eliminates the negotiating phase and boils the game down to an anonymous, orders-only game.  This can condense a game of diplomacy into about 30 or 45 minutes.

How do you ensure anonymity in person, when people are turning in their sheets of paper, flipping them over, recognizing handwriting, and seeing reactions to orders being read?

Well, I created an API that allows you to submit your orders via SMS.  All players simply text their moves to a phone number (provided by Twilio) and they are forwarded on as HTTP requests to a Ruby application hosted on Heroku.

This application exposes an API around a simple state with some logic on when to consider a round "ready" for resolving moves.

## Installation and usage

After getting the application deployed and an account on Twilio set up (see GitHub), players can use the following commands:

```
Commands    # List all commands
End Round   # Tabulate all responses and send them to all players
Germany
  F Kie-Den
  A Ber-Kie
  A Mun-Ruh # Example moves for Germany
```

This was a fun project to play around with Twilio, which lets you send and receive SMSs as part of your application's business logic.

Source code is [available here](https://github.com/mzemel/gunboat_diplomacy)

Thanks for reading!
