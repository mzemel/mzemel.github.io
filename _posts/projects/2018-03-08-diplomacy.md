---
layout: post
title: "Playing anonymous Diplomacy in person"
tagline: "How to lose friends and influence people"
image: /assets/patterns/paisley.png
header:
  image: /assets/patterns/asanoha-400px.png
repository:
  is_project_page: true
  show_downloads: true
  repository_url: https://github.com/mzemel/gunboat_diplomacy
tags: ["games"]
keywords: games
ref: cayman-blog-project
lang: en
category: projects
---

I love board games.  Diplomacy is an especially good one where there is absolutely no luck involved.  It is similar to Risk but instead of rolling dice to evaluate the success of an invasion, you must negotiate with your fellow players for support.  If France wants to invade Germany, he's not going to have much luck unless England is willing to lend the support of their fleets and armies.

![diplo](https://i.imgur.com/NvFeesa.png)

What makes this even more appealing: what you agree to is non-binding.  So if you _claim_ to help France in his quest, your written instructions may even be the opposite: help Germany remain in place or even move into France.  Alliances are formed and broken; your skill as a player depends on your ability to encourage teamwork and trust, then betray your allies at the right time to claim sole victory.

### Gunboat

Diplomacy can take upwards of six or seven hours to complete a game.  Much of the time is spent in a "colluding" phase before moves are entered and resolved, where players discuss their alliances with each other.  A quicker variant, called Gunboat, speeds up the game to about an hour by eliminating this colluding phase.  In this version, players remain anonymous and do not communicate with each other while entering their moves.

In order to play this version, it's helpful to have a way to ensure anonymity.  To achieve this, I created a simple database and API (via SMS) to record moves.  When all players have entered their moves, everyone receives an SMS with all moves so they can be resolved while the players remain anonymous.

### Commands

* `Commands` - List commands
* `End Round` - Tabulate all responses and send them to all players
* `Germany F Kie-Den A Ber-Kie A Mun-Ruh` (example) - enter moves as Germany

### Last notes

This was a fun project to play around with Twilio, which lets you send and receive SMSs as part of your application's business logic.

Source code is [available here](https://github.com/mzemel/gunboat_diplomacy)

Thanks for reading!