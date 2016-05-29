---
layout:     post
title:      "Buy Eternity Wall API with bitcoin"
subtitle:   "Without an account"
author:     "Riccardo"
---

Yesterday there were two ways to call our hash timestamping service.

### For humans

The first is through the [notarize](http://eternitywall.it/notarize) page.
The service is free, but to prevent spam you need to solve a captcha to use it. Without the captcha the service could be machine called multiple times causing a lot of backend costs. We simply can't afford that.

### For machines

The second is through the [authenticated api](http://eternitywall.it/api#auth) with a call like
[this](http://eternitywall.it/v1/auth/hash/7f066dc8262610339d0407e8dfafc9216b20e35c421785a56b87f28c566d61da?account=1K9gCCHberw6s61H9HiD6D9FtzCgry1bj7&signature=HzBR7t4aZn8L0lMN5ZBbBNzPgz8yi8oZfEMCoJhoOic7Xdh/kxzGxQjDna6IW8JtUeO1Z6xLlrOt8ryjyuJbskw=&challenge=[challenge]).
If you clicked on the example call, you can see the `Unauthorized request` error, you need to have an Eternity Wall account and provide a valid signature to be able to call the authenticated api.

### For bitcoin-enabled machines

Using the [21](https://21.co) platform we are proposing another way to consume the API, a way that is machine-callable but without needing an account. How to prevent spam and request abuse? By paying bitcoin at every request.
If you try to call this [endpoint](http://21.eternitywall.it/v1/hash?hash=44ee321219c5db38b31f876521ba950af4c347445de5e2366a45c1c1685e50aa) you will be presented the `Payment required` error, the `402` special http code.

This response code was thought at the very beginning of the http protocol specification but none used it because we haven't a valid digital money yet. Until bitcoin.

By installing the 21 library (at the time of writing available on Mac and Ubuntu) you can purchase the endpoint with 100 satoshi (now $0.0005) with a command like this:

{% highlight shell %}
21 buy http://21.eternitywall.it/v1/hash?hash=44ee321219c5db38b31f876521ba950af4c347445de5e2366a45c1c1685e50aa
{% endhighlight %}

And getting the result

{% highlight json %}
{"timestamp":1464334903,"status":"ok","txHash":"4a0affd1be068f6e82a264a4ff1c4f2160a9d4009062a11d7c36fc4465086ea5","merkle":{"index":6,"root":"b876cc932d3bb115a7f1ea841b5a81209d7472e2f283e91a6945b61ccabbdf62","hash":"44ee321219c5db38b31f876521ba950af4c347445de5e2366a45c1c1685e50aa","siblings":["478b504ee9d865b9d43667e82728787e9e0c6af4c8ea6cb8fdaf9e0af1aedb7a","b2a366378fea5af446b09e695bb738c7d0819406b352aa2e12781fa30627f094","26e083b03987f33ac3d2a19d4f91eaabc131405726d08df4614d269f83b506d9","8f45a797b601cf3f59f70aeea81d7fc64022d8dcbbb43bcb2c75f22d99f9445a","26ca433ac131818780b05e3aa3ba095b76ce5f54869dc2ad170870ca1bf856ab"]}}
{% endhighlight %}

Instead of getting information about a previously subimitted hash, you can send a new one created by a document of yours by calling the sha function and a `POST` request, for example:

{% highlight shell %}
# create hash of a document
shasum -a 256 README.md
44ee321219c5db38b31f876521ba950af4c347445de5e2366a45c1c1685e50aa  README.md

# insert the hash
21 buy http://21.eternitywall.it/v1/hash?hash=44ee321219c5db38b31f876521ba950af4c347445de5e2366a45c1c1685e50aa --request POST
{% endhighlight %}


Calling the API from the command line is mostly for testing, but you can easily integrate the api call in a [python program](http://21.eternitywall.it/client).

If you like this article and our endpoint, don't forget to rate it with the command

{% highlight shell %}
21 rate JDl 5
{% endhighlight %}

Thanks!
