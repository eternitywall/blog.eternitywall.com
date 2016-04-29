---
layout:     post
title:      "New feature released"
subtitle:   "Short urls and customized referral link"
author:     "Riccardo"
---


We just released a couple of new interesting feature on Eternity Wall.


### Short links


The first is the short url for message page, you can now share short links.

* **long link** [http://eternitywall.it/m/ae223853a142ee16238eeba01ba1dcbb8dff5415fb28cacb9e90a20758140462](http://eternitywall.it/m/ae223853a142ee16238eeba01ba1dcbb8dff5415fb28cacb9e90a20758140462)
* **short link** [http://eternitywall.it/m/ae223853](http://eternitywall.it/m/ae223853)

Remember that the link need at least the first 8 characters of the transaction id to work.


### Referral link containing customized message


Some users requires custom message in the Eternity Wall pages they are linking to. For example in a qrcode they are generating for an exhibition or whatever.
You can now build a link to a message containing a text that will be shown in the page linked. For example:

[http://eternitywall.it/m/4c077b90?m=I%20told%20you,%20young%20Martin!](http://eternitywall.it/m/4c077b90?m=I%20told%20you,%20young%20Martin!)

Notice that we are also using a short url to save space, than we are inserting a custom message in the query string `I told you, young Martin!`.

Html is not allowed and is striped out to prevent injection, however one link is recognized and rendered as clickable.
