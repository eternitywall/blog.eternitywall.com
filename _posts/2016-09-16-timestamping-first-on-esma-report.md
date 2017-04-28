---
layout:     post
title:      "Timestamping is the first non-monetary blockchain use case"
subtitle:   "In the official response to ESMA"
author:     "riccardo.casatta"
---

ESMA (European Securities and Markets Authority) recently asked a [Consultation](https://www.esma.europa.eu/press-news/consultations/consultation-distributed-ledger-technology-applied-securities-markets) on the distributed ledger technology applied to securities markets.

[Ferdinando Ametrano](https://twitter.com/Ferdinando1970) [^1],
[Emilio Barucci](https://www.mate.polimi.it/qfinlab/index.php?pp=show_pagine&id_art=1&cate=1),
 [Daniele Marazzina](https://www.mate.polimi.it/qfinlab/index.php?pp=show_pagine&id_art=54&cate=1) and [Stefano Zanero](http://home.deib.polimi.it/zanero/) submitted an answer, suggesting that timestamping and notarization services might be the first non-monetary use case for blockchain technology.
The relevant section of their [response]({{ site.baseurl }}/content/20160902_Response_to_ESMA_DLT.pdf) is the following:

> **Q2: Do you see any other potential benefits of the DLT for securities markets? If yes, please explain.**<br><br>
Notarization services are a very promising blockchain application [11]: the bitcoin blockchain (the most secure one, since the effort/cost for its manipulation is prohibitive) can be used for the trustless time-stamping of documents and the anchoring of arbitrarily large data set. A generic data file can be hashed to producing a short unique identifier, equivalent to its digital fingerprint. Such a fingerprint can be associated to a bitcoin transaction, the bitcoin amount being irrelevant, and hence registered on the blockchain: the blockchain immutability then provides robust non-repudiable time-stamping that can always prove without doubt the existence of that data file in that specific status at that precise moment in time. This generic process is even undergoing some standardization to achieve third party auditable verification [12]. Broker-dealers could use it to satisfy the regulatory prescriptions [13] for storing required records exclusively in non-rewriteable and non-erasable electronic storage media. WORM (write once read many) optical media has been used so far, but it is quite impractical, especially for large data set; instead, compliance could be easily achieved anchoring rewritable data sources to the blockchain, providing accurate and secure time-stamping resilient to manipulation.
<br><br>...<br>
<br>[11] [https://eternitywall.it/notarize/](https://eternitywall.it/notarize/), [https://stampery.com/](https://stampery.com/), [https://tierion.com/](https://tierion.com/)
<br>[12] [http://blog.eternitywall.it/2016/06/24/announcing-opentimestamps-support/](http://blog.eternitywall.it/2016/06/24/announcing-opentimestamps-support/), [https://github.com/opentimestamps/python-opentimestamps](https://github.com/opentimestamps/python-opentimestamps)
<br>[13] Rule 17a-4 of the Securities Exchange Act (Broker Dealers). See also [http://www.17a-4.com/regulations-summary/](http://www.17a-4.com/regulations-summary/)

Eternity Wall strongly agree with the content of the answer.
We are happy to see our efforts to promote standardization has been noted, and acknowledged also with a direct reference to our [article](http://blog.eternitywall.it/2016/06/24/announcing-opentimestamps-support/).

We also endorse this tweet by Ametrano.

> "What jewelry is for gold, timestamping could be for bitcoin: not essential, but effective at leveraging its beauty"


After the response, another [blog post](https://petertodd.org/2016/opentimestamps-announcement) about the new version of OpenTimestamps has been published by Peter Todd.

We are already working to provide WORM solutions in response to regulatory prescriptions in the Dodd Frank normative using the blockchain. Updates will be soon on this blog.

The ESMA response is a confirmation of our vision, and our commitment on the subject is stronger than ever.


<br><br>


[^1]: F. Ametrano will be teaching "Bitcoin and Blockchain Technology" this semester at Politecnico, and Politecnico will host Scaling Bitcoin Milan in October
