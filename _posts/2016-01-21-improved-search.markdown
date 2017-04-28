---
layout:     post
title:      "Improved Search"
subtitle:   "Find the message that you are searching"
author:     "riccardo.casatta"
---

Searching on Eternity wall is now improved. Having made available the signed message feature, you can now search for aliases. they are part of the text indexed, so that searching for [Eternity Wall](http://eternitywall.it/search?q=Eternity+Wall) returns all the posts written by the "Eternity Wall" account and those ones containing just the words.
Even better, there are fields available, so searching for [alias:"Eternity Wall"](http://eternitywall.it/search?q=alias%3A%22Eternity+Wall%22) returns only posts from the "Eternity Wall" alias.
You can also search for a transaction [hash](http://eternitywall.it/search?q=435bd04cdc04eacaa715f5d41b684294bf573f03362b02befd8feb43e624665c), or a [bitcoin address](http://eternitywall.it/search?q=135E4KvaMJBAmX6nsq3twKnnBtjSL3csN6) representing the message or the alias.

## Special fields available

* `timestamp` containing the date, eg: [timestamp < 2016-01-01](http://eternitywall.it/search?q=timestamp+%3C+2016-01-01)
* `size` of the message eg: [size > 40](http://eternitywall.it/search?q=size+%3E+60)
* `height` of the block containing the message  eg: [height: 379945](http://eternitywall.it/search?q=height%3A+379945)
* `language` of the message [ language: fr ](http://eternitywall.it/search?q=language%3Afr)
* `alias` of the author writing the message [alias:"Eternity Wall"](http://eternitywall.it/search?q=alias%3A%22Eternity+Wall%22)
