---
layout: single
title: "Building 'Monarchs'"
header:
  overlay_image: /assets/img/monarchs.png
  overlay_filter: 0.5
  actions:
    - label: "Monarchs"
      url: https://thebackend.dev/monarchs
    - label: "GitHub"
      url: https://github.com/mzemel/monarchs
tags: ["history"]
keywords: history
lang: en
category: projects
toc: true
excerpt: An interactive, visual timeline of European's sovereigns
---

# Background

<!--
A while back, I was watching PBS' Wolf Hall, a miniseries about the marraige of Henry VIII to Anne Boleyn.  As I watched the series, I thought to myself that it would be great to understand who else was in power.  As I learned more about Henry VIII, I realized there was a whole world of fantastic stories, all true, that relate him back to rulers across Europe.  Through his first wife, Catherine of Aragon, he was related to the Holy Roman Emperor, Charles V.  Through his sister, he was related to the house of Stuart in Scotland.  Why did he go to war against France?  What was the pope doing this whole time, and why was it so hard for him to acquire a divorce?

As a visual learner, history has always been a series of disconnected dates.  1492, Columbus sailed the ocean blue.  Some time in the 13th century, the Magna Carta was signed.  Waterloo was, for some reason, very important.  Putting these events together has never been easy for me, but each time someone was able to tell a story that slotted in tidbits I already knew, I was fascinated.
-->

To me, history has always seemed like a scatterplot of disconnected dots.  I'd learn about one narrow lane of history at a time, like the American Revolution, and feel frustrated that I couldn't recall specific enough dates to correlate what was happening in other parts of the world.  For example, while America was fighting Britain in the War of 1812, Napoleon had swept across Europe and dismantled one of the largest, most powerful institutions: The Holy Roman Empire.

Not only did I have trouble understanding synchronicity, I also had gaps in my education about any nation or boundary that existed before I was born.  For example, I always had this flash of, "What was Prussia again" when I'd encounter it in Wikipedia.  And I'd go down the rabbit hole but, again, the way I approached learning was always too focused to retain that information in a larger context.

My goal here is to create a feeling of *synchronicity* and *flow*.  Synchronicity in that the user can see who was in power at the same time and what world events were unfolding.  Flow in that you can click through a nation's history and sense the changing borders, alliances, and dynasties as you move from one ruler to the next.

I hope that you're able to have fun with this website (and blog post), finding them informative and perhaps even a reference you can come back to later.

<!--

<img src="assets/img/monarchs/europe_500.png" />

Above, we can see some tribes from the year 500 AD.  You'll recognize the Franks and Danes, from which modern France and Denmark are derived, as well as the Angles and Saxons.  You may recognize the region of Mercia, yet you won't find it on any map today - in fact, Mercia ceased to exist before the Normans conquest of England.  How did we get from that picture to this one?

<img src="assets/img/monarchs/europe_2019.gif" />

If you're anything like me, you know it involves some diplomacy, some battles, some religion, and some scientific achievements.  Also, if you're like me, you've reached the limit of your at-hand knowledge, which is exactly where this project aims to pick up.

When I look at pictures like this, I'm confronted with the boundary between what I do and do not know: enough to know there's a story, not enough to see the whole picture.

As I did a little preliminary thinking, I gathered up some questions that had been in my mind for some time:

* What was Prussia - was it a German state?  A country?  When did that change?
* What was the Diet of Worms, besides a terrible meal plan? 
* What is a Pretender?
* What's the difference between a baron, a duke, an earl, and a count?

If you've had these questions, or similar ones, continue reading!

-->

# Building it

The first question was what to display.

I had a loose goal: show something, make it look good, and make it interesting.  If anything catches your eye, and you go down a rabbit hole, I'll consider it a success.

First, I wanted to show each **monarch** and what years they were in power.  That would at least help me put a century to a name.  No longer would I assume Henry VIII ruled at some point between Caesar and Kennedy.

Next, I wanted to show each **house**.  Knowing dynasties is a useful heuristic for estimating when someone was in power.  The Tudors came before the Stuarts who came before the Saxe-Coburg & Gothas.

As I did some preliminary research, I kept a list of what seemed to be the most important houses.  Like the picture of early medieval tribes, I knew enough to feel engaged but not enough to feel knowledgable. 

England seemed to focus on the Plantagenets, Lancasters, Yorks, Tudors, Stuarts, Hanovers, and the Windsors.

France involved the Capet, Valois, some cadet branches (see Terminology), and Bourbons.

But Spain also had (and currently has) Bourbons.  That's interesting.  And before that it had Habsburgs, who I recognized from the Holy Roman Empire.  Something interesting must have happened during the switch from Habsburgs (and the Holy Roman Empire) to Bourbons (and France).  And, of course, something interesting did happen - The War of Spanish Succession - because of this handsome fellow.

<img src="assets/img/monarchs/charles_ii.jpg" style="display:block; margin-left: auto; margin-right: auto; width: 50%;" />

Speaking of **wars**, I wanted to show how these empires related to each other via wars.  For example, the Hundred Years War ought to show that for a period from 1337-1453 England and France were engaged in an on-and-off-again war that characterized their near-millenium rivalry.

And for good measure, let's show some information to make the monarch more compelling, like **events**, **religion**, and major events in other parts of the world.

## Design

From here, I drafted my earliest mockup

<img src="assets/img/monarchs/mockup.png" />

There would be a modal containing the above information, a timeline for each country, red lines for wars, and a vertical line that tracks your mouse and displays the year.

## Technology

I decided to go with [d3.js](d3js.org), which you might recognize from the [New York Times](https://www.nytimes.com/interactive/2014/09/19/nyregion/stop-and-frisk-map.html).  D3 manipulates HTML SVGs as data enters and exits the page, usually after user interaction.  You can set up transitions, which gives the site a very responsive feel.

<img src="https://media3.giphy.com/media/BLX7DZmgkT31K/giphy.gif" />

These transitions are often paired with javascript event handlers, such as hovers and clicks.  In the above example, a hover will reduce the opacity of non-selected elements, helping to bring focus to a smaller subset of data.

The API for d3 is very intuitive and pleasant to work with.  The following code populates and provides user interaction for the "important date" circles above the x-axis:

<img src="assets/img/monarchs/dates.gif" style="display:block; margin-left: auto; margin-right: auto; width: 50%;" />

Let's step through it, or feel free to skip ahead if reading code is not your thing.

```javascript
dateControlContainer.selectAll("circle")
  .data(dateControlData)
  .enter()
  .append("circle")
  .attr("cx", function(d, i) { return margin.left + dateControlInterval / 2 + (i * dateControlInterval); })
  .attr("cy", height - margin.bottom / 2)
  .attr("r", circleRadiusLarge)
  .attr("fill", function (d) { return d.color })
  .attr("fill-opacity", 0.5)
  .attr("class", function(d) { return ['control',  d.key].join(' ') }) 
  .on("click", focusDates)
  .on("mouseover", function(d) { enlargeCircle.bind(d3.select(this))(this); })
  .on("mouseout", function() { reduceCircle.bind(d3.select(this))(this); })
  .append("title").text(function(d) { return d.key; });
```

This code will select an SVG `<g>` container element, `dateControlContainer`, and some data: an array of dates, `dateControlData`.

`enter()` and `append()` are directives for what to do with each data element.  `attr()` will set a particular HTML attribute - in this case, SVG attributes like `x` and `y`.  You can return dynamic values by passing in a callback which takes the element as an argument, such as `.attr("fill", function(d) { return d.color })`.   You could write it using ES6 arrow functions for a cleaner interface, but I'm not using Babel or any javascript transpiler, so I'm not sure that my code would run on IE or any less modern browsers, so I opted to write in pure javascript.  Still, though, it would be nice to write `.attr("fill", d => d.color)`

The `.on()` function is used to apply event handlers - in this case, `click`, `mouseover`, and `mouseout`.  What's very pleasing is that order is not very important for these functions since each one returns the d3 object (same as returning `self`), which makes method chaining very easy.

The event handlers themselves look like:

```javascript
function enlargeCircle(el) {
  if (detailsOpen) return false

  var $this = $(el),
    $r = parseFloat($this.attr('r'));

  this
    .attr("r", $r * 1.25)
    .attr("smallR", this.attr("smallR") || $r)
    .attr("fill-opacity", 0.75);
}

function reduceCircle(el) {
  if (detailsOpen) return false

  var $this = $(el),
    $r = parseFloat($this.attr('r'));

  this
    .attr("r", this.attr("smallR"))
    .attr("fill-opacity", 0.5)
}
```

`enlargeCircle` will transition the circle to be 25% larger and more opaque, while `reduceCircle` (on `mouseout`) will return the circle back to its original size and opacity.

The full [source code](https://github.com/mzemel/monarchs/blob/master/src.js) is visible on GitHub and comes in around 1,000 lines of code, most of which is d3 (with a little jQuery and Lodash).

## Credits

Icons made by [FreePik](https://www.flaticon.com/authors/freepik) and [Smashicons](https://www.flaticon.com/authors/smashicons) from [www.flaticon.com](www.flaticon.com)

Fonts from [dafont.com](https://www.dafont.com/stonehenge.font) and [Google Fonts](https://fonts.googleapis.com/css?family=Lato)

Thanks to my wonderful wife for designing the details modal.

Thanks to Paul Hayslett for UI feedback and Chris Barthell for the idea of borders over time.

## Challenges

There were several challenges, first and foremost the amount of time it took to compile all of the data.

On average, I'd spend about 20 minutes on a single monarch: pull all the relevant info from Wikipedia, find a good picture, create the map, and edit the info to fit into the frame and tell a compelling, accurate story.

Sacrifices had to be made: leaving out dates of wars when the text bled into the map, only including the 3 "most important" events, explaining complex intrigue in 100 characters or less...  My goal was to be about 95% accurate, leaving Wikipedia for a more accurate detailed view should anything pique the reader's interest.

The breakdown of time spent looked something like this

INSERT PIE CHART HERE

If I do something like this again, I'd probably look for an existing dataset.

The other challenges were more technical.  Doing the whole website in SVG made scaling to fit the window easy: every element gets its dimensions from the window's width/height.  This means images, padding, and even font size scale correctly on most devices.  Also, SVGs are supported by all modern browsers - at least the features I used - so the site looks the same on Chrome, Firefox, and IE without any changes needed.

On the other hand, text handling in SVG leaves a lot to be desired.  There is zero support for paragraphs, so I had to use single-line text for everything.  I wrote two functions: one that fits text to a rectangle and one that fits a rectangle to text.  That allowed me, mostly, to reliably prevent text from bleeding over into other elements.  In some cases, I had to use monospace font to get higher accuracy (the date circles at the bottom).


# Representation choices

As I mentioned, sacrifices had to be made to show as much data as possible without overloading the user.

Not every country is present: Greece, Norway, Poland, and others have dynastic monarchies but had to be excluded iin the interest of focusing attention on the larger players.

When a country was a vassal to a more powerful one, for example northern Italy to the Holy Roman Empire, the sovereignty of the vassal would not be displayed.  Put another way, even though there was a King of Italy, the throne was occupied by the Holy Roman Emperor and thus I considered Italy part of the Empire.

Speaking of Italy, there was no recognition of a nation of Italy before the 19th century.  To represent the Italy column, I'd have to choose between the kings of Northern Italy, Sicily, Sardinia, and Two Sicilies - all of which existed at different points. Germany and Spain have similar issues, which I'll address below.

## England

## Scotland

## Spain

Starting with Ferdinand and Isabella, Spain was a union of the crowns of Castile, Aragon, and Leon.  It wasn't until the end of the War of the Spanish Succession that Spain became unified under one crown, the King of Spain.

To make things more complicated, after the deaths of Ferdinand and Isabella, the unified crowns of Castile, Aragon, and Leon passed to their grandson, the Holy Roman Emperor Charles V.  In keeping with the ethos of not representing vassal states independently, I decided to start with Charles V passing the Spanish throne to his son Philip II, who did not have any direct involvement with the Holy Roman Empire.

I was on the fence about including Franco who, besides being a monster, was not technically a monarch.  He re-established the monarchy, though, and played a critical role in how we understand Spain today.  Much moreso than other interregnum leaders, such as Oliver Cromwell.

## France

When does France start?

There were Kings of the Franks before Charlemagne (the Merovingian line, for example) but the nation-state of France evolved much later.  Charlemagne, at the height of his power, controlled what is now considered Germany, Northern Italy, and the low countries (Belgium and the Netherlands).  His son, Louis the Pious, struggled to keep that land together and, when he died, the land was split into three large kingdoms:

<img src="assets/img/monarchs/europe_843.png" style="display:block; margin-left: auto; margin-right: auto; width: 50%;" />

The western kingdom, West Francia, goes to Charles the Bald (and eventually becomes France); East Francia goes to Louis the German (and eventually becomes Germany); Lotharingia, the middle kingdom, goes to Lothair.  A couple generations later, Charles the Fat inherits East Francia, Lotharingia, and West Francia giving him control over as much land as Charlemagne.  When he dies, however, the land is split 5 ways.

Originally, I had France not starting with Charles the Bald, since he is crowned Emperor of the Romans.  Instead, I started with his son, Louis the Stammerer, even though his descendant Charles III is also crowned King of the Romans.  However, for simplicity, I decided to start with the founder of the Carolingian dynasty, Pepin the Short.

The boundaries of France change perhaps the most dramatically over time, from a low-point during the Angevin empire

<img src="assets/img/monarchs/europe_1154.png" style="display:block; margin-left:auto; margin-right:auto; width:50%;" />

to a high-point during the Napoleonic wars:

<img src="assets/img/monarchs/europe_1812.png" style="display:block; margin-left:auto; margin-right:auto; width: 50%;" />

## Holy Roman Empire

While Charlemagne is the first to be crowned Emperor of the Romans, I decided to start the Holy Roman Empire with Otto I, founder of the Ottonian dynasty.  This is largely because there would be overlap with France, as several King of the Franks were also crowned Emperor of the Romans, whereas Otto is the first to be crowned while also holding the lands that will make up the Holy Roman Empire for centuries: modern-day Germany and Northern Italy.

## Germany

<!--
After the dissolution of the Holy Roman Empire in 1805 the German territories, spurred by pan-German nationalism, organized themselves into the German Confederation after the fall of Napoleon, at the Congress of Vienna.  The most powerful of the 39 member states, Prussia, pushed for their king to lead this confederation.  After a war with Austria in 1866, the states reorganized themselves under Prussian leadership as the North German Confederation in 1867 and again as the German Empire in 1871.  This lasted until Germany's defeat in 1918, when the country became known simply as Germany.

Representing this as a single timeline is difficult.  For example, the leaders of Prussia were styled as "King in Prussia" (1701-1772) and then "King of Prussia" (1772-1871) when the German Empire was founded, and the king became an Emperor in his own right.  However, for much of the time that the King in/of Prussia existed, so did the Holy Roman Empire, making the King not an absolute sovereign.

Thus, to truly represent "Germany", you need to encompass the Holy Roman Empire, German Confederation, North German Confederation, German Empire, and Germany.  In the interest of getitng this project done in a timely fashion, I opted to skip Germany for now.
-->

Germany, to the astute observer, is not Germany as we recognize it today.  Between the fall of the Holy Roman Empire and today, it was the Confederation of the Rhine, the German Confederation, the North German Confederation, the German Empire, and finally modern Germany.  Throughout this period, the most powerful player besides Austria (who is represented separately) is Prussia.  Therefore, in my timeline the "monarchy of Germany" is a sequential line of the Hohenzollern rulers of Prussia, North German Confederation, and the German Empire.

## Italy
<!--
Italy is another country whose fascinating history had to be cut for simplicity.

Northern Italy, for many centuries, had been ruled by the Holy Roman Emperor.  The Papal States, while always controlled by the papacy, grew and shrunk dramatically.  The rest of Italy was either the Kingdom of Sicily or the handful of semi-autonomous states, such as Venice, Genoa, and Florence (depending on what year you choose).

Italy as we know it today was not fully unified until 1861, and its monarchy lasted 85 short years before being abolished after World War II.  Because an independent, unified Italy would appear to be so short compared to other countries, I left it off for now.
-->
As I mentioned, Italy did not exist as a unified country that it is today until 1861(?).  Before that, the land was host to a plurality of kingdoms and independent nation-states.  To display Italy in earnest, it would be four monarchs starting with unification and ending with the abdication of Umberto II after the second world war.  Leaving out Italy also means leaving out the kingdoms of Sicily, Sardinia, Two Sicilies, and Northern Italy.

## Scandinavia

Scandinavia is another area that I had to make some cuts.  Norway in particular is tricky, since it has existed in a union with either Denmark, Sweden, or Denmark _and_ Sweden on and off for the past 1,000 years.  In a tragic oversimplification, I decided that Denmark was the most stable and powerful, and decided to just show them for now.  During the period of the union between Denmark, Norway, and Sweden (the Kalmar Union), I opted to show that as "Denmark" even though the king, Erik III, was proclaimed King of Norway first.

## Others

Other countries not currently included are Greece, Austria, Wales, Ireland, the Netherlands, and the popes.

Not because they're not cool, but because having more than 7 countries seems to be information overload.

# Terminology

Royalty use a lot of terms.  For example, there are a lot of dukes in England, but when someone is called the Duke of Wales, that means they're next in line for succession.  And in the interest of making things as confusing as possible, each country has their own set of terms, so I've attempted to compile many of them here.

There are a lot of overloaded or non-obvious terms in medieval history.  For example, how is the Duke of Wales different than the Duke of Norfolk?  What is a Stadtholder or Prince-Elector?

As I went through Wikipedia, I pulled out a lot of terms that I felt needed clarification:

| Term | Meaning | Notes | Example |
|:-----|:--------|:------|:--------|
|Prince of Wales|Heir apparent|English|Prince Charles|
|Duke of Rothesay|Heir apparent|Scottish (applies to English since unification)|Prince Charles|
|Prince of Asturias|Heir apparent|Spanish||
|Infanta|Princess|Spanish, especially eldest daughter who was not heir||
|Dauphin|Heir apparent|French, used after France acquired the territory of Dauphiné||
|Archduke|Heir apparent|Holy Roman Empire|Archduke Franz Ferdinand|
|Prince-Elector|Council that elects the Holy Roman Empire|||
|Stadtholder|Duke/head of state|Dutch Republic|William of Orange, before becoming King of England|
|Doge|Chief magistrate of Venice or Genoa|Italian|[Doge](https://upload.wikimedia.org/wikipedia/en/thumb/5/5f/Original_Doge_meme.jpg/300px-Original_Doge_meme.jpg)|
|Cadet house|Hereditary line through a younger sibling||Valois is traced back to Capet through second son of Philip III|
|Primogeniture|Hereditary line through the oldest sibling||Salic law|
|Junior King|Crowned heir apparent|Sporadic practice; French, English, Hungarian||

# Anecdotes

There are so many cool standalone stories that don't fit into this visualization, but I want to tell them anyway.

## The [Black Dinner](https://en.wikipedia.org/wiki/Clan_Douglas#Black_Dinner), Scotland 1440

By the 15th century, clan Douglas was seen as a powerful threat to the ruling family, Stuart.  The Earl of Douglas and his younger brother, both under the age of 17, were invited to dine with the ten-year-old king of Scotland, James II.

While they ate, a black bull's head, a symbol of death, was brought in and placed before the Earl.  The two brothers were then dragged out to Castle Hill, given a mock trial and beheaded.

Game of Thrones, anyone?

## King James V and demonology

## Legendary Danish and Norwegian kings

## Avignon Papacy / Antipopes 

## Who was king of France between 1422 and 1453?

From 1422 to 1453, Henry VI was crowned King of the French in Paris (whilst Charles VII was only crowned in 1429 after Joan of Arc freeded Reims from English dominion)

## History painters

## Favorites

## To convert, or not convert

Some would-be monarchs refused to convert (50+ Catholics who passed up English crown); some did convert (Henry IV converting to Catholicism to get the French throne)

## Epithets

Some of the epithets that kings were given were flattering: Charles the Great (Charlemagne) and Louis the Pious. 

Some were questionable, like William the Silent and Aethelred the Unready.

Some were downright rude:

<table>
  <tr>
    <td>
      <select>
        <option>Pepin</option>
        <option>Charles</option>
        <option>Wilfred</option>
        <option>Louis</option>
        <option>Egbert</option>
      </select>
    </td>
    <td> the </td>
    <td>
      <select>
        <option>Short</option>
        <option>Fat</option>
        <option>Simple</option>
        <option>Hairy</option>
        <option>Stammerer</option>
        <option>Bald</option>
        <option>One-Eyed</option>
      </select>
    </td>
  </tr>
</table>

## The Orkney Islands

## Where to bury the body?

Frederick I (and earlier emperors) having their body parts buried in different locations: "his flesh was interred in the Church of St Peter in Antioch, his bones in the cathedral of Tyre, and his heart and inner organs in Tarsus."

# Superlatives

## Smartest

## Dumbest

## Worst Death

## Cruelest

## Best Looking

Many of the early kings were not [winning any beauty pageants](https://thebackend.dev/monarchs/img/england/stephen.jpg).  Perhaps it was the [limited painting materials](https://thebackend.dev/monarchs/img/england/john.png), or the [medieval standards of beauty](https://thebackend.dev/monarchs/img/france/louis_xi.jpg), but of the kings, Lothair III of France wins best-looking:

<img src="https://thebackend.dev/monarchs/img/france/lothair_iii.jpg" style="display:block; margin-left:auto; margin-right:auto; width:50%;" />

## Notable mentions

### Hugh the Great

### Henry the Fowler

# Cool paintings

<img src="https://upload.wikimedia.org/wikipedia/commons/4/49/Siege_of_Paris_%28885%E2%80%93886%29.jpeg" style="display:block; margin-left:auto; margin-right:auto;" />

Odo, Count of Paris, defends the city against Danish Vikings led by Rollo, 885-886

Painting by Jean-Victor Schnetz, 1837

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/21/Friedrich_Kaulbach_-_Kr%C3%B6nung_Karls_des_Gro%C3%9Fen.jpg/1024px-Friedrich_Kaulbach_-_Kr%C3%B6nung_Karls_des_Gro%C3%9Fen.jpg" style="display:block; margin-left:auto; margin-right:auto;" />

Coronation of Charlemagne by Pope Leo III, 800

Painting by Friedrich Kaulbach, 1861

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/Eug%C3%A8ne_Delacroix_-_La_libert%C3%A9_guidant_le_peuple.jpg/757px-Eug%C3%A8ne_Delacroix_-_La_libert%C3%A9_guidant_le_peuple.jpg" style="display:block; margin-left: auto; margin-right: auto;" />

Liberty Leading the People, Eugene Delacroix, 1830

I always thought this was the French Revolution but it was, in fact, the July Revolution of 1830

# Useful graphics

# Learning more 

1. Wolf Hall
2. The Favourite
3. The Lion in Winter
4. The Crown
5. [The History of Europe: Every Year](https://www.youtube.com/watch?v=UY9P0QSxlnI)
6. [BazBattles](https://www.youtube.com/channel/UCx-dJoP9hFCBloY9qodykvw)

# Conclusion

Hope you enjoyed this project!  If there's anything you'd like to see included, or you want to reach me, shoot me an [email](mailto:michael.zemel@gmail.com) or reach out via the social links on the left.
