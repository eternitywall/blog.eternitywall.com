---
layout:     post
title:      "How to write a message on the Blockchain"
subtitle:   "without Eternity Wall"
author:     "federico.tenga"
image:      "/img/how-to-write.png"
---

In this guide we will see how you can write a message on the Blockchain by yourself, without using Eternity Wall. All you need is a synchronized Bitcoin Core client with some balance on it, just to pay transaction fees.


## Step 1

Start up your Bitcoin Core client and open the console window (help/debug window/console).
If you are using a terminal prepend `bitcoin-cli` to the commands of this tutorial.

The first thing you need to do is to list the unspent outputs of the address you are going to use:


##### Command:
{% highlight shell %}
 listunspent ( minconf maxconf ["address",...] )
{% endhighlight %}


##### Example:
{% highlight shell %}
 listunspent 0 999999 '["18crwWX79SZ1EMv7xuuE3XmrUrn4STVd3t"]'
{% endhighlight %}


##### Output:
{% highlight shell %}
[
{
"txid": "d1b492277ad5e18a4e9125198857d091d1d79f0e1bc152f8f1314d289e5ce5b7",
"vout": 1,
"address": "18crwWX79SZ1EMv7xuuE3XmrUrn4STVd3t",
"account": "op test",
"scriptPubKey": "76a91453912076234a50b79c0c7fcb85a9d11f9223c55f88ac",
"amount": 0.00970000,
"confirmations": 85,
"spendable": true
}
]
{% endhighlight %}


## Step 2

Than you have to create the raw transaction:


##### Command:
{% highlight shell %}
createrawtransaction [{"txid":"paste here txid form previous step output","vout":number from previous step},'{"data":"the message you want to write on the blockchain converted in hex ","the address you are using":change}']
{% endhighlight %}


##### Example:
{% highlight shell %}
createrawtransaction '[{"txid":"d1b492277ad5e18a4e9125198857d091d1d79f0e1bc152f8f1314d289e5ce5b7","vout":1}]' '{"data":"455720457465726E616C206C6F766520666F72207468697320616D617A696E672057616C6C","18crwWX79SZ1EMv7xuuE3XmrUrn4STVd3t":0.00100691}'
{% endhighlight %}

##### Output:
{% highlight shell %}
0100000001b7e55c9e284d31f1f852c11b0e9fd7d191d057881925914e8ae1d57a2792b4d10100000000ffffffff020000000000000000276a25455720457465726e616c206c6f766520666f72207468697320616d617a696e672057616c6c53890100000000001976a91453912076234a50b79c0c7fcb85a9d11f9223c55f88ac00000000
{% endhighlight %}


N.B. if you don’t set the change, all the bitcoin you have on the address you are using will go to the miner. In this example I made a mistake while setting the change, and I ended up with paying more than 4 dollars in fees, so be careful!

To convert your message in hex you can use your own tool or use one of the free converter you can find online, [here there is an example](http://www.idea2ic.com/PlayWithJavascript/hexToAscii.html). If you want your message to be visible on Eternity Wall, it has to start with EW.
In my example I converted the message *“EW Eternal love for this amazing Wall”* in the hex format `455720457465726E616C206C6F766520666F72207468697320616D617A696E672057616C6C`.


## Step 3

Sign the raw transaction you just created:


##### Command:
{% highlight shell %}
signrawtransaction "hexstring from previous step output"
{% endhighlight %}


##### Example:
{% highlight shell %}
signrawtransaction 0100000001b7e55c9e284d31f1f852c11b0e9fd7d191d057881925914e8ae1d57a2792b4d10100000000ffffffff020000000000000000276a25455720457465726e616c206c6f766520666f72207468697320616d617a696e672057616c6c53890100000000001976a91453912076234a50b79c0c7fcb85a9d11f9223c55f88ac00000000
{% endhighlight %}


##### Output:
{% highlight json %}
{
"hex": "0100000001b7e55c9e284d31f1f852c11b0e9fd7d191d057881925914e8ae1d57a2792b4d1010000006a473044022066e662940aa1c2435028552655ef65ad902ff9b9c65f58dbc462ed779399b90f02203751025953307134297360ac152ba98acb6a8a3ee563416d56b65ba0dae358ab0121027b8644d160a7f485f85c3799ff64b89eface431426688b7ad6cf6346e23ce266ffffffff020000000000000000276a25455720457465726e616c206c6f766520666f72207468697320616d617a696e672057616c6c53890100000000001976a91453912076234a50b79c0c7fcb85a9d11f9223c55f88ac00000000",
"complete": true
}
{% endhighlight %}


## Step 4

Last step, now you just have to broadcast the sign transaction:


##### Command:
{% highlight shell %}
sendrawtransaction "hexstring from previous step output"
{% endhighlight %}


##### Example:
{% highlight shell %}
sendrawtransaction 01000000017d0b93e049dbb6b717dbef4ceeb8af8f91357fd6a60eb7eafb91ac084e0f33e5000000006b483045022100b02e09aaf4fbd6d54024d8e87dc7be8f25dbc83e13869269a28747b3ecaa33e202201b3c2e12f188161fd3f7c796a2122f5289937fbf2857bfc5d94143ba640b30510121027b8644d160a7f485f85c3799ff64b89eface431426688b7ad6cf6346e23ce266ffffffff020000000000000000296a274557206e6f74207573696e6720457465726e6974792057616c6c2068657265206269746368657310cd0e00000000001976a91453912076234a50b79c0c7fcb85a9d11f9223c55f88ac00000000
{% endhighlight %}


##### Output:
{% highlight shell %}
050a4811501f9404e970526701c91a4ce9153766799ec732e569003dae909067
{% endhighlight %}

The output from this last step is the transaction ID that you can use to find your message on any block explorer. If your message starts with EW, after one confirmation it will appear also on Eternity Wall.
You can indeed see my example on a [block explorer](https://www.blocktrail.com/BTC/tx/050a4811501f9404e970526701c91a4ce9153766799ec732e569003dae909067) and on [Eternity Wall](http://eternitywall.it/m/050a4811501f9404e970526701c91a4ce9153766799ec732e569003dae909067).
