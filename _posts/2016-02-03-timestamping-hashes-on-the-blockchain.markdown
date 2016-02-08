---
layout:     post
title:      "Timestamping documents"
subtitle:   "on the blockchain"
author:     "Riccardo"
---

## Timestamping documents on the blockchain

You can now hash documents and commit on the blockchain simply sending an email with attachments at:

<h4>
<div class="alert alert-info center-block text-center" role="alert">
  <span class="glyphicon glyphicon-envelope" aria-hidden="true"></span> <a class="alert-link" href="mailto:timestamp@eternitywall.it">timestamp@eternitywall.it</a>
</div>
</h4>

*A hash of a document in the blockchain is a proof of existence of the document in a certain point in time.*

Beware that this feature is out in **Beta**, and there is one commitment per day.

### Let me know the details

At Eternity Wall we think putting hash of data in the blockchain and putting the data themselves are two completely different things.

### Data in the blockchain

By putting all the data in the blockchain, you get the same property of the blockchain:  eternity, uncensorability, indipendence and proof of time.
The network take care of data at the price of the network fee for including the transaction.

### Hash of Data in the blockchain

When you put a hash of some data in the blockchain you have a commitment of the data in the blockchain. Your data is timestamped only if you or someone you trust keeping the real data. You are losing indipendence, uncensorability and eternity.

### So, why not putting everything on the blockchain?

Because the blockchain is expensive. Decentralization is a tough business, coming to consensus and mantaining the data structure require that network nodes use a lot of resources.

### Why timestamping hashes?

Because they scale without requiring one transaction for every hash. With a structure called merkle tree a lot of hashes could commit on a single one. You need to write in the blockchain only the tree root hash to make a commitment for every hash composing the tree.

### What about ownership?

With the current API working with email is already possible to sign documents but it's up to the user. You can digitally sign your email with PGP or with the Estonian E-residency before sending.

### Is Eternity Wall storing the documents?

Eternity Wall is not storing the document after computing the hash. However attachments in email are readable during their travel, if you are concerned about privacy you can encrypt your data or wait for the client hashing service, soon available.

### Try it now, it's free :)

<h4>
<div class="alert alert-info center-block text-center" role="alert">
  <span class="glyphicon glyphicon-envelope" aria-hidden="true"></span> <a class="alert-link" href="mailto:timestamp@eternitywall.it">timestamp@eternitywall.it</a>
</div>
</h4>


<br>
