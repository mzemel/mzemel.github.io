---
#
# Here you can change the text shown in the Home page before the Latest Posts section.
#
# Edit jekyll-theme-simple-blog's home layout in _layouts instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
#
layout: home
permalink: /
header:
  image: /assets/img/home-header.jpg
tagline: > # this means to ignore newlines until "repository:"
  Thoughts on engineering, games, and travel
excerpt: >
  Michael Zemel has a blog!
repository:
  is_project_page: false
  show_downloads: false
ref: home
lang: en
---
<h2>Latest Projects</h2>
<div>&nbsp;</div>
{% include list-category-posts.html lang=page.lang category="projects" max=3 %}

---

<h2>Latest Articles</h2>
<div>&nbsp;</div>
{% include list-category-posts.html lang=page.lang category="articles" max=3 %}
