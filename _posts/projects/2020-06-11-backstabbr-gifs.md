---
layout: single
title: "Backstabbr GIFs"
header:
header:
  overlay_image: /assets/img/diplomacy_header.jpg
  caption: "HistoryGamerDotCom"
tags: ["games"]
keywords: games
lang: en
category: projects
toc: true
excerpt: Making GIFs from games
---

# Backstabbr GIFs

[Click here to see the finished GIF](#finished-gif)

I love playing Diplomacy on [Backstabbr](backstabbr.com).

Recently, I played a really fun game where I tried to solo in 1905, and everyone rallied together to stop me.  It was a give-and-take chase across Europe for the next 15 years, leading to my eventual defeat in Winter of 1920.

What I really wanted - besides to know what my opponents were saying about me - was to see the game as a **GIF**.

Now, I could go back to previous turns and screenshot them all, crop the images, and create a GIF, but on every previous page Backstabbr always overlays the red and black "order resolutions" on the map.

<img src="assets/img/orders.jpg" />

While that is an integral part of the GIF, I also wanted to show the state of the open board each turn, *before orders were resolved*.  This would show more clearly the position players were in, as well as convey a period of careful planning and strategy before entering orders.

So how would I do this?

Since the map on the page is an \<SVG>, my plan was to take the page's HTML, remove all the order resolution elements, and have an image of the pre-resolution state.

## Getting the HTML

My first step was saving what I saw in `View Source` (`⌘⌥u` on Mac).

I quickly learned, however, that `View Source` is **NOT** the same as `Inspect Element`.

`View Source` will give you the HTML document provided by the server (available through the `Sources` tab in the Chrome DevTools).

`Inspect Element` looks at HTML that has been **modified** by CSS and Javascript.  Because Backstabbr was using Javascript to draw the SVG in the browser, it did not appear in `View Source`.

I could not find an "easy way" to download the `Inspect Element` HTML.  Maybe I missed it, because the way I ended up doing it felt pretty long.

I decided to **Save** the page and was given two options, `Save page (complete)` and `Save page (single file)`.

### Save page (complete)

"Complete", I thought.  I unzipped the download, and was given two items:
```
backstabbr.htm
backstabbr_files/
  all.min.css
  jquery.min.css
  ...
```

As you can see, this downloads every externally hosted asset.   In the `.htm` file there are relative paths for all assets:

```
<link href="./backstabbr_files/bootstrap.min.css" rel="stylesheet" integrity="..." crossorigin="...">
```

I tried swapping out the relative path for an absolute one, `https://www.backstabbr.com/bootstrap.min.css`, but -- in retrospect, obviously -- that's not where the files are hosted..

Given I can't go through every assets and infer where it's from (I guess I could if I reallllly had to), I decided to take another route.


### Save page (single file)

Ah, ha! I thought.  This must be it.

I downloaded the page as a single file and was given a file with an `.mhtml` extension.

I open it up in my text editor and notice some really strange stuff:

```
From: <Saved by Blink>
Snapshot-Content-Location: https://www.backstabbr.com/game/Its-Okay-If-Stefon-Loses/6301224099381248/1918/spring
Subject: Game: It's Okay If Stefon Loses | spring 1918 | Backstabbr
Date: Sat, 13 Jun 2020 23:00:51 -0000
MIME-Version: 1.0
Content-Type: multipart/related;
	type="text/html";
	boundary="----MultipartBoundary--CnhE2y0UIwz7apsP21ksjv9bp07FgmYYC4sjAfKWZe----"


------MultipartBoundary--CnhE2y0UIwz7apsP21ksjv9bp07FgmYYC4sjAfKWZe----
Content-Type: text/html
Content-ID: <frame-6D5C3621DE86CABB90FC28BED1397B07@mhtml.blink>
Content-Transfer-Encoding: quoted-printable
Content-Location: https://www.backstabbr.com/game/Its-Okay-If-Stefon-Loses/6301224099381248/1918/spring

<!DOCTYPE html><html lang=3D"en"><head><meta http-equiv=3D"Content-Type" co=
ntent=3D"text/html; charset=3DUTF-8">
  <link rel=3D"manifest" href=3D"https://www.backstabbr.com/manifest.json">
  <meta http-equiv=3D"X-UA-Compatible" content=3D"IE=3Dedge">
  <meta name=3D"keywords" content=3D"diplomacy,diplomacy game,online diplom=
```

So before we get to the actual HTML, there are some headers.  And as I scrolled down, I noticed a LOT of encoded data:

```
    9 ------multipartboundary--cnhe2y0uiwz7apsp21ksjv9bp07fgmyyc4sjafkwze----
    8 content-type: font/woff2
    7 content-transfer-encoding: base64
    6 content-location: https://fonts.gstatic.com/s/lato/v16/s6uyw4bmutphjx4wxiwtfcc.woff2
    5
    4 d09gmgabaaaaadbcaa0aaaaabvgaadafaaeaaaaaaaaaaaaaaaaaaaaaaaaaaaaag6r6hhagyacb
    3 rbemcogeaigdggudqgabngika4z2bcafhrghheubgf5fb2lgoadmhpynoh7iucjihg2qk7zk/58t
    2 1biyf+sack5uubhuvqz8zaqfogmuvz7uvu93ltbfg9z2punpgz+vgx9huymbumvwljtzr2kx0xwj
    1 f+4zxgbqzn65+5ktjcnmgxlmb6bzmpb4vui6a9xhryw7d+v+/57djpd94jilmqiojy0haocayfzv
11715 ybpritaehkngh/zxb2ybpn07n7dtiuko7tqibc8udwn+q+c2zps0hascqigokaicg3exlrblihmb
   ```

I had no idea what this was. The M in MHTML stands for MIME, but this is a webpage, not mail. My guess is that mail is involved in the file download, perhaps Chrome "sending" it to your computer.

I tried a different browser, Firefox, but got the same thing.  Ultimately, though, my \<SVG> element was in there!

## Changing the SVG

Unfortunately, due to the MIME artifacts, the SVG was a little mangled:

```
  <div id=3D"map"><svg height=3D"100%" version=3D"1.1" width=3D"99%" xmlns=
=3D"http://www.w3.org/2000/svg" style=3D"overflow: hidden; position: relati=
ve; left: -0.5px;" viewBox=3D"0 0 610 560" preserveAspectRatio=3D"xMinYMin"=
><desc style=3D"-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">Created wit=
...
```

That's ok!

I used a couple regexes (`s/=\n//g`, `s/3D//g`) to get the \<SVG> element on a single line,
<br>... then `:g!/.*<svg/d` to delete all other lines, 
<br>... then `s/<\/\(\w*\)>/<\/\1>\r/g` to put each SVG element on its own line,
<br>... then put the `<svg>` inside an `<html>` and `<body>`,
<br>...and changed the file extension to `.html`

Viola!

<img src="assets/img/orders-html.png" />

Sample SVG lines, for illustration:

```
<path fill="#dfddc9" stroke="#ffffff" d="M192,323L205,331L204,338L211,346L213,352L209,363L208,367L194,382L178,381L178,390L173,396L168,395L163,387L165,383L158,380L156,374L165,365L185,344L188,332Z" fill-opacity="0" stroke-width="1" transform="matrix(1,0,0,1,0,0)" id="ter_Bur" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); fill-opacity: 0;"></path>
<text x="246" y="425" text-anchor="middle" font="10px &quot;Arial&quot;" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: middle; font: 9px Verdana; fill-opacity: 1;" font-family="Verdana" font-size="9px" fill-opacity="1" transform="matrix(1,0,0,1,0,0)"><tspan dy="3.609375" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">Tus</tspan>
```

At this point, I have an `.html` file with order resolutions.

Using variations on `:g/<path.*#000000/d`,  I erased black and red `<circle>`s and `<path>`s.

This leaves me with exactly what I want:

<img src="assets/img/orders-none.jpg" />

## Converting to a JPG

From searching, it looks like people recommend `wkhtmltoimage`, the less-documented, red-headed step-child of `wkhtmltopdf`.

I was getting some weird errors, which turned out to be a [`3-hour-old issue`]([https://github.com/wkhtmltopdf/wkhtmltopdf/issues/4716](https://github.com/wkhtmltopdf/wkhtmltopdf/issues/4716)).

```
dyld: lazy symbol binding failed: Symbol not found: ____chkstk_darwin
  Referenced from: /usr/local/bin/wkhtmltoimage
  Expected in: /usr/lib/libSystem.B.dylib
  ```

So I grabbed an earlier release, and used:
`wkhtmltoimage --height 600 --width 600 backstabbr.html backstabbr.jpg`

To create a series of images, I created the **following script:**

```
for i in {1..51}
do
  vim $i.mhtml -c '%s/=\n//g | %s/3D//g | wq'
  sed -n '/^.*<svg/p' $i.mhtml > $i.html
  vim $i.html -c '%s/<\/\(\w*\)>/<\/\1>\r/g | %s/<div id="map">/<html><body>/ | %s/\/images\/map/https:\/\/www.backstabbr.com\/images\/map/ | wq'
  echo "</body></html>" >> $i.html
  sed -i '' '/<path fill="#000000" stroke="#000000.*stroke-width="0"/d' $i.html
  wkhtmltoimage --height 600 --width 600 $i.html image$((i*2)).jpg
  sed -i '' '/<path fill="none" stroke="#000000/d' $i.html
  sed -i '' '/<path fill="#000000" stroke="#000000.*stroke-width="2"/d' $i.html
  sed -i '' '/<path.*#ff0000/d' $i.html
  sed -i '' '/<path.*#ffa500/d' $i.html
  sed -i '' '/<circle.*fill="none" stroke="#000000/d' $i.html
  sed -i '' '/<circle.*fill="none" stroke="#ff0000/d' $i.html
  wkhtmltoimage --height 600 --width 600 $i.html image$((i*2 - 1)).jpg
done


```

I can't say that this part was fun.  But it *is* done.

I then used `ImageMagick` to convert the stills into a GIF with:

```
convert -delay 1x2 -loop 0 *.jpg -coalesce -layers OptimizeTransparency backstabbr.gif
```

## Finished GIF

<img src="assets/img/backstabbr.gif" />
