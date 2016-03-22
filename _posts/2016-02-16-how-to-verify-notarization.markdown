---
layout:     post
title:      "How to manually verify notarization?"
subtitle:   "Warning: technical content"
author:     "Riccardo"
---

This is my actual profile picture:

<a href="{{ site.baseurl }}/img/riccardo-casatta-profilo.jpg">
<img src="{{ site.baseurl }}/img/riccardo-casatta-profilo.jpg" class="center-block" style="cursor:pointer">
</a>

Many told me I look like an inmate, anyway it's not the point here, let's hash the file with sha256, from a terminal:

{% highlight shell %}
$ shasum -a 256 riccardo-casatta-profilo.jpg
20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e  riccardo-casatta-profilo.jpg
{% endhighlight %}

Or I can drop the file into [Eternity Wall notarization service](http://eternitywall.it/notarize) and get the following message:

<div class="text-center"><div class="alert alert-success" style="word-wrap: break-word;">Document hash is <strong>20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e</strong><br>It has been <a href="http://eternitywall.it/v1/hash/20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e" class="alert-link">notarized</a> on the blockchain at <strong>Sun Jan 31 2016 09:46:31 GMT+0100 (CET)</strong></div></div>

First of all, the resulting hash is the same.
There is also a message saying the message has been notarized in the blockchain on **Jan 31 2016**.
This means the document hash is written in a transaction confirmed in a block broadcasted that day.
Since no one can rewrite the blockchain this assure the document existed at that time.
The proof is reachable from the notarized link and contains the following json:

{% highlight json %}
{"timestamp":1454229991,
"status":"ok",
"txHash":"bdbe9044539777f12cb6162f40e3aeb419ef1b2d3e9bd80d4a8c84f0a34d33c0",
"merkle":{
  "index":2,
  "root":"53a1df014de8c698e25c1733aa2d3571e51f12b3578b268b318ac71af734480e",
  "hash":"20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e",
  "siblings":["4c822a3b88741e8e83e7b9a289ffc97ba5dbb330cfbbf0cf67b94776edad9dcd","414c7dd644620431dfd2636c27aadb7c59845258ab0f1efb813857b9edc38e94","6e8d8f6163ef68207d6432bd6b368e90fbac65d0068bdcaf6b227066694b1a34"]}
}
{% endhighlight %}

Let's see what parameters means:

### Timestamp

`1454229991` is the date time information in unix format which translate to **Sun Jan 31 2016 09:46:31 GMT+0100**

{% highlight shell %}
$ date -jf "%s" 1454229991                  
Sun Jan 31 00:46:31 PST 2016
{% endhighlight %}

### Status

`ok` is pretty straightforward, other possible results are `processing` which means the hash commitment was already requested but still not being written in the blockchain. The last possible result is `not found` when the hash was never seen before. In the latter case, you will be prompted if you want to notarize it.

### txHash

`bdbe9044539777f12cb6162f40e3aeb419ef1b2d3e9bd80d4a8c84f0a34d33c0` is the hash of the bitcoin transaction containing the root of the merkle tree representing the commitment in the blockchain. If you check on a block explorer [this transaction](https://blockchain.info/tx/bdbe9044539777f12cb6162f40e3aeb419ef1b2d3e9bd80d4a8c84f0a34d33c0), you will find the merkle root `53a1df014de8c698e25c1733aa2d3571e51f12b3578b268b318ac71af734480e` in the tx op_return output (before this data there is the EW tag which is `4557` in hex for this example and EWC `455753` for actual production transactions). 
But this is not yet the hash of my picture!

## Merkle Tree

To understand how to verify the proof we need a digression on Merkle Tree

> In cryptography and computer science, a hash tree or Merkle tree is a tree in which every non-leaf node is labelled with the hash of the labels or values (in case of leaves) of its children nodes. Hash trees are useful because they allow efficient and secure verification of the contents of large data structures. Hash trees are a generalization of hash lists and hash chains. - [wikipedia](https://en.wikipedia.org/wiki/Merkle_tree)

Eternity Wall is committing more than a single hash every time is writing on the blockchain.
When it's time to write all the hashes being queued are taken and sorted. In the following pictures they represent the leaf of the tree (Hash 0-0 Hash 0-1 Hash 1-0 Hash 1-1). The top hash is the root of the tree.

<a href="{{ site.baseurl }}/img/Hash_Tree.svg">
<img src="{{ site.baseurl }}/img/Hash_Tree.svg" width="600px" class="center-block" style="cursor:pointer">
</a>

To prove the hash of my document is in the merkle tree, i need all off the leaf of the tree or the sibling till the root.
If my Hash is 1-0, to derive the root I will need just the siblings Hash 1-1 and Hash 0 because I can compute:

`Hash 1 = hash( Hash 1-0 + Hash 1-1 )`

`Top Hash = hash( Hash 0 + A )`

Why can't I just give all the tree leafs ? Because they are `N`, while the siblings are just `log N`. A proof of a very big merkle tree could be really compact.

### Siblings

Now that we now know more about merkle trees we come back to our data. Our siblings are:
`["4c822a3b88741e8e83e7b9a289ffc97ba5dbb330cfbbf0cf67b94776edad9dcd", "414c7dd644620431dfd2636c27aadb7c59845258ab0f1efb813857b9edc38e94", "6e8d8f6163ef68207d6432bd6b368e90fbac65d0068bdcaf6b227066694b1a34"]`

My hash is `20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e` which is the `sha256` of my document.
The index `2` is representing the leaf position of my hash. The position is needed to understand the order of the hash in the concatenation. If I compute:

`Hash 536b = hash2( Hash 20c7 + Hash 4c82 )`

Where `Hash 20c7` is the hash of my picture and `Hash 4c82` is the first sibling. `hash2` is a double sha256.

`Hash 2451 = hash2( Hash 414c + Hash 536b )`

Where `Hash 414c` is the second sibling and `Hash 536b` is the hash of the previous computation.

`Hash 0e48 = hash2( Hash 2451 + Hash 6e8d )`

Where `Hash 2451` is the hash of the previous computation and `Hash 6e8d` is the first sibling

`Hash 53a1 = reverse( Hash 0e48 )`

where `Hash 0e48` is the hash of the previous computation and `Hash 53a1` is the merkle root. What was to be demonstrated.

What are you waiting for? [Notarize documents](http://eternitywall.it/notarize) on Eternity Wall :)

<a href="http://eternitywall.it/notarize">
<img src="{{ site.baseurl }}/img/certificate-smaller.png" class="center-block" style="cursor:pointer">
</a>
