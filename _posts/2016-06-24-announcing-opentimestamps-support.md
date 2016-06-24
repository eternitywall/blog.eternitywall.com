---
layout:     post
title:      "Announcing OpenTimestamps support"
subtitle:   "Full indipendent verification"
author:     "Riccardo Casatta"
---

# OpenTimestamps

<a href="{{ site.baseurl }}/img/mail-stamp-template.svg">
<img src="{{ site.baseurl }}/img/mail-stamp-template.svg" alt="OpenTimestamps"  />
</a>

## The path towards third party auditable verification

Eternity Wall is making efforts towards allowing a fully independent timestamps verification.

We already provided our users [instructions](http://blog.eternitywall.it/2016/02/16/how-to-verify-notarization/) and open-source [tools](/2016/05/16/how-to-independently-verify-notarization/) to verify our notarizations.
We would like to improve the auditability of our service by having an externally developed independent stamp verifier.

## OpenTimestamps discovery

Peter Todd showed us his OpenTimestamps format during his recent [visit](https://petertodd.org/2016/talks-dex-arnhem-dev-workshop) in Milan.
We immediately realized this was what we needed to create a trustworthy timestamping ecosystem. One of the best part of the format is flexibility, without changing the way Eternity Wall is stamping documents we added in ours stamps the OpenTimestamps format, under the `ots1` property as in this [example](http://eternitywall.it/v1/hash/20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e).

## Basic Idea

The basic idea of the format is that if you can create a path of generic operations from the document to the hash of a block you are proving that the document is committing to that block.

All the operations you need are hashing functions (like `sha256` and `ripemd`), generic functions (`reverse`) and concatenations of binary data as a prefix or a suffix.
We are using the bitcoin blockchain to prove the timestamping but the format is flexible enough to support also other blockchains.

## How to verify

The following are the steps you need to verify an Eternity Wall notarized document using [Peter's python tool](https://github.com/petertodd/python-opentimestamps). The tool needs a bitcoin node with a reachable RPC endpoint (basing on the settings in your `bitcoin.conf`).

We are using the picture of this [example](http://blog.eternitywall.it/2016/02/16/how-to-verify-notarization/) as the document to prove. Get the [stamp](http://eternitywall.it/v1/hash/20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e) data and copy in the clipboard the content of the `ots1` child in the json, you will need this data later.


{% highlight shell %}
git clone https://github.com/petertodd/python-opentimestamps
cd python-opentimestamps
wget http://blog.eternitywall.it/img/riccardo-casatta-profilo.jpg
nano riccardo-casatta-profilo.jpg.ots # paste the clipboard content in this file
./ots verify riccardo-casatta-profilo.jpg.ots
{% endhighlight %}

```
INFO:root:Success! riccardo-casatta-profilo.jpg was created on or before 2016-01-31 09:46:31
```

Getting this messages means the document has been timestamped in the bicoin blockchain on or before 2016-01-31 09:46:31


## Improvements

The actual verification is still cumbersome but we are hoping that a wider ecosystem support of the OpenTimestamps format will grant an easier verification process in the future.

We are already working in this direction to promote broader adoption of the standard. Stay tuned!
