---
layout:     post
title:      "How to independently verify notarization?"
subtitle:   "From your browser"
author:     "riccardo.casatta"
image:      "/img/eternity_logo_final.svg"
---

After the notarization service [launch](http://blog.eternitywall.it/2016/02/03/timestamping-hashes-on-the-blockchain/) back in February we posted about [manually verify](http://blog.eternitywall.it/2016/02/16/how-to-verify-notarization/) the Eternity Wall provided stamp [example](http://eternitywall.it/v1/hash/20c7ba9c57f653b7c079df5171c196f494a5446d684c1b26a63bc5fc3fa2e25e).

Now we are providing our users a way to automatically and independently verify their notarizations.

## Why we are doing this?

Because even if Eternity Wall completely disappear, you can continue to prove integrity and date of your documents thanks to the blockchain.

From today we provide also a stand-alone [page](http://riccardo.casatta.it/independent-notarization-verifier/) doing the verification directly in your browser. You can also save the page for later use to be completely independent or by forking the github [repo](https://github.com/RCasatta/independent-notarization-verifier/tree/gh-pages).

## What are the verification made by the page?

The page takes two input:

* The document which has been notarized
* The stamp provided by Eternity Wall once the notarization has been done (You can download it on the notarization page providing the original document or just its hash by following the "notarized" link in the green alert)


After you click on the verify button the following checks are performed.

* Document hash matches the one found in the stamp
* The merkle root derived from the document hash and the siblings in the stamp, matches the merkle root found in the stamp
* The transaction referred by the id found in the stamp is asked from an independent block explorer.
  * The timestamp found in the stamp matches the one of the block containing the transaction we just asked
  * The transaction contain an op_return script which contains the merkle root found in the stamp

Since the blockchain is unmodifiable this checks confirm that your document existed at the timestamp found in the stamp, and even if Eternity Wall disappear.
