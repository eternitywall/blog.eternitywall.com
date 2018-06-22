---
layout: post
title: sign-to-contract 
subtitle: How to timestamp with zero marginal cost 
author: leonardo.comandini
image: "/img/secp256k1commitment.png"
---

Creating a timestamp involves writing some data on the blockchain, by including those data in a valid bitcoin transaction (e.g. with `OP_RETURN`).
The timestamp then is the cryptographic path that goes from the timestamped object to a block header, passing through the created transaction.
However, including those extra data causes your transaction size to increase, making the overall cost of the transaction higher. 

Thanks to OpenTimestamps aggregators and calendars, 
this issue is almost completely solved by collecting several timestamp requests and 
fitting those inside a single transaction, funded by the calendar. 
Consequently, if you pass through a calendar, you can timestamp completely for free.
Yet, if you want to timestamp by yourself, you have to pay some more fees.

Actually there is a way to avoid to pay those extra satoshis: a *math trick* that makes possible to *include a cryptographic commitment inside an elliptic curve point*. 
Thanks to this gimmick every purely financial transaction can be turned into a timestamping transaction, while still serving for its original purpose and without adding a single additional byte.
This allows users not only to timestamp their data with no marginal cost, 
but, with no expense, they can also help the calendar timestamping its Merkle tip, leading to more frequent timestamps for calendar clients.

The post is structured as follows:
- history of the name
- the math trick: elliptic curve commitments
    - *pay-to-contract*
    - *sign-to-contract*
    - some remarks
- an actual implementation w/ Electrum
    - how to run
    - how to timestamp
    - how to verify
- conclusions

# A bit of history
When hearing the name *sign-to-contract*, 
you may wonder from where the contract comes out.
To find an answer we have to go back (in 2012) to one of the [first applications](https://arxiv.org/pdf/1212.3257.pdf) that was developed: *pay-to-contract* (*sign-to-contract* elder brother) which basically allows to commit a contract to a receiver public key.
When a customers performs a payment to a merchant, with *pay-to-contract*, 
he can commit the bill containing the details of the purchase to the receiving address.

Both *pay-to-contract* and *sign-to-contract* are based on the same operation that allows to commit a value to a public key (`secp256k1` point) used in a bitcoin transaction.
Such operation is called *secp256k1 commitment* and its functioning is detailed in the following section.

Although secp256k1 commitments could serve different scopes, 
we focus on timestamping purposes 
and we take as a reference the [PR](https://github.com/opentimestamps/python-opentimestamps/pull/14) to OpenTimestamps 
(and the preceding [issue](https://github.com/opentimestamps/python-opentimestamps/issues/12)) by Andrew Poelstra.

# A bit of math: elliptic curve commitments
Now we need to get our hands into cryptography to unveil how this math trick works under the hood
(don't get scared, we provide a quick recap of the results at the end of the section).

Given an elliptic curve with generator `G` and a random oracle hash function `h`.
The map `m,P -> h(P||m)G + P`, where `||` stands for concatenation, `m` is the value to commit and `P` is an elliptic curve point, is a valid commitment operation.
The output of the map is `C(m,P)`, which is an elliptic curve point.

The value `m` is arbitrary, in particular it can be a commitment to something else (e.g. the hash of a document), 
and `P` is a public key used for a certain (and possibly independent) purpose.
The public key `C(m,P)` is a commitment to `m` and `P` 
in the sense that if the input is changed, so `(m,P)!=(m',P')`, 
then the output changes, `C(m,P)!=C(m',P')`.
Thus we can use `C` as a commitment operation (like a hash function) inside a timestamp proof.

Now, to curb the abstraction, choose `secp256k1` as elliptic curve and `sha256` as hash function. 
The motivation is that we want to use Bitcoin as a notary, 
and Bitcoin itself grounds its working on the integrity of these two ingredients.
We won't use directly the corresponding `C`, but instead a slightly modified version: `OpSecp256k1Commitment`.
It takes one input, `P||m`, and outputs the x-coord of the elliptic curve point `C(m,P)`.

**Recap:** 
pick a value to commit `m` and a public key `P` (on the curve `secp256k1`), concatenate them (`P||m`), then compute:
```
C(m,P) = SHA256(P||m)G + P 
OpSecp256k1Commitment(P||m) = C(m,P).x
```
The value obtained is an elliptic curve point that serves two (independent) purposes: 
is a public key __and__ is a commitment to `(m,P)` 
(or only to `m`, if `P` is given before).

To timestamp we need to write the x-coord of the elliptic curve point `C(m,P)` on the blockchain, 
there are two places where such points can be found, leading to two techniques:
- Address/Public key: *pay-to-contract*
- Signature: *sign-to-contract*

## *pay-to-contract*
Alice has to receive some bitcoins from Bob.
Alice has public key `A` but she also wants to timestamp a document with hash value `d`.
Thus she tells Bob to send his bitcoins to `Q = A + h(A||d)G`.
Bob, who eventually is not aware that he is timestamping Alice's document, 
broadcasts the transaction, 
which after a while becomes part of the blockchain forever.
Alice composes her OpenTimestamps proof for her document, by finding `Q.x` on the chain and conveniently arranging the proof.

However, by implementing this scheme Alice is exposing herself to a new risk.
Her new key `Q` is a deterministic function of `x` (private key of `A`) and `d`, 
but, while `x` is derived from the seed (carefully stored),
instead the value `d` is probably something new and unrelated to her wallet.
If her computer catches fire, 
the seed alone won't be enough to find the private key to redeem the coin locked by `Q`,
since it is required also `d`, 
which needs an ad hoc backup that possibly has not taken place.

This technique may lead to a loss of funds, hence using *pay-to-contract* for timestamping purposes is not advisable.

## *sign-to-contract*
Now Carl has to pay Diana. 
Carl wants also to timestamp a value `c`.
With *sign-to-contract* he can fit a commitment to `c` inside the signature.
To understand how, we need to recall how signatures work.

Signatures in Bitcoin are made with ECDSA which involves elliptic curve points, 
namely a signature is a couple of integers `(r,s)` in `Z/nZ` where `n` is the order of the curve.
In a simplified way, a signature for a message `m` and from a user with private key `x` is done as follows:

```
def ECDSAsign(x,m):
    k = deterministic_nonce(x,m)
    R = k*G
    r = R.x mod n
    s = k^(-1) * (m + r*x) mod n
    return (r,s)
```
Carl wants to spend the coins locked in a given address whose private key is `x`.
He composes an unsigned transaction `uTX` sending from that address to Diana's.
To sign the transaction he applies `ECDSAsign(x,m)` where `m` is the hash value of `uTX` conveniently serialized.
Once produced the signature `(r,s)` Carl fill `uTX` with an encoded version of the signature `SIG(r,s)`, 
obtaining the signed (and broadcastable) transaction `TX`.
Then sends `TX` to the network and, later on, when confirmed, it becomes part of the blockchain forever.

We can see that inside `ECDSAsig` an elliptic curve point (`R`) is used.
The idea of *sign-to-contract* is simply to tweak it with `h(R||c)`, 
making it __also__ a commitment to another value `c`.

```
def ECDSAsign2contract(x,m,c):
    k = deterministic_nonce(x,m)
    R = k*G
    e = k + h(R||c)
    Q = R + h(R||c)*G  # which is Q = e*G = C(c,R)
    q = Q.x mod n
    z = e^(-1) * (m + q*x) mod n
    return (q,z), R
```
Carl applies `ECDSAsign2contract`, which produces `(q,z)` and `R`. 
The former is the signature corresponding to the pubkey `x*G` for the message `m`,
while the latter and `c` are the ingredients to prove the commitment to `Q.x`. 
Now `uTX` is filled with `SIG(q,z)` which contains `q`, which in turn is (almost always) `Q.x`.
As before, the resulting `TX` then is committed to the blockchain.

Carl then looks at `TX`, in which he spots `Q.x`, with `TX = TXp||Q.x||TXa`. 
By retrieving the info to link `TX` to a block header we can complete the timestamp for `c`:
```
File sha256: c
Timestamp:
prepend R == R||c
secp256k1commitment == Q.x
prepend TXp == TXp||Q.x
append TXa == TXp||Q.x||TXa
# transaction id ...
sha256
...
sha256
verify BitcoinBlockHeaderAttestation(...)
# Bitcoin merkle root ...
``` 
**Resuming**, while doing a purely financial transaction to Diana, **Carl timestamped** a value, 
without adding any bytes, so **with zero marginal cost**.
Diana could be unaware that Carl timestamped, 
from her perspective the coins she just received are indistinguishable from coins signed in the standard way. 
Such coins are locked in the pubkey (or address) that Diana told Carl, 
hence Diana's new coins are as secured as normal coins. 

Carl chose a particular nonce that allowed him to commit to a value.
The resulting singature is indistinguishable from a standard one and has the same security.
Moreover, if he loses the committed value `c`, 
then he loses the ability of proving the timestamp,
but he does not compromise any coin: 
his coins have been already spent and Diana has hers in the desired destination, 
which is independent from `c`.
For this reason *sign-to-contract* should be preferred when timestamping.

## Remarks
- The name is merely due to historical reasons and it came out of the desire to commit to contracts when paying someone.
  The value committed could be any arbitrary data: 
  for practical timestamping purposes it will be a Merkle tip aggregating several single timestamps.
- Each elliptic curve point can include a commitment,
  thus each pubkey, as well as each signature, could be used as anchoring point.
  Multiple commitments can be included in a single transaction,
  however, for timestamping, this is not essential: as pointed out above, 
  a contract can be a commitment to an arbitrary high number of single timestamps.
- These techniques (as everything in OTS) does not apply only to Bitcoin, 
  with convenient adjustments, they can be extended to other similar systems,
  for instance Litecoin or MimbleWimble.
- With *segwit* some issues arise. 
  The signature is committed in the `wtxid`,
  but not in the `txid`: 
  the latter is committed in the transaction Merkle tree, 
  while the former is committed in another Merkle tree, 
  whose tip is inserted in the coinbase transaction. 
  As a result, the signature is committed in the block header, 
  but the path has to traverse both trees and the coinbase.
  Thus proofs at least double in size, 
  the miner has some control on what is inside the coinbase, 
  so he may decide to include malicious data or 
  make the coinbase so large (multiple KB) 
  that it won't be possible to create an OTS proof passing through that.

# An actual implementation w/ Electrum
Implementing this stuff may seem simple,
but in practice it involves many steps very unrelated between each other:
extract private keys, 
perform elliptic curve math, 
fill the custom signature in the tx,
correctly serialize in the tx,
retrieve the link from tx to the block header 
and finally
compose the OTS proof.

Anyhow we made it simple with an extension to the [Electrum plugin](https://github.com/LeoComandini/electrum-timestamp-plugin) 
we presented [few weeks ago](https://blog.eternitywall.com/2018/03/14/electrum-timestamp-plugin-launch).

We now outline how to run this custom version and 
then how to create your first *sign-to-contract* OTS proof.

**Remark:** 
at this stage the code is not intended for a general public
thus the procedure to make it works may be not super easy to put in place.

## Getting started
**Beware:** 
what follows is experimental,
make sure it is not messing up your Electrum or OpenTimestamps working libraries. 

Assuming running on Linux.
 
You need to use the custom library by [apoelstra](https://github.com/apoelstra) that integrates `OpSecp256k1Commitment` in OpenTimestamps.
You need to download Electrum from source and integrate it with the timestamp plugin. 
 
``` 
git clone https://github.com/apoelstra/python-opentimestamps.git
git clone https://github.com/LeoComandini/electrum-timestamp-plugin.git
git clone git://github.com/spesmilo/electrum.git
```

Now go to the sign-to-contract (`s2c`) branch, 
copy the plugin file in the local Electrum directory, 
then copy a temporary version of `setup.py` in the custom version of python-opentimestamps just downloaded.
```
cd electrum-timestamp-plugin
git checkout s2c
cd ..
cp -r electrum-timestamp-plugin/timestamp electrum/plugins
cp electrum-timestamp-plugin/setup.py python-opentimestamps
```

Install the custom library.
```
pip3 install python-opentimestamps/
```

Follow an adapted version of [Electrum README](https://github.com/spesmilo/electrum#development-version).

Electrum is a pure python application. 
If you want to use the Qt interface, install the Qt dependencies:
```
sudo apt-get install python3-pyqt5
sudo apt-get install python3-setuptools
cd electrum
python3 setup.py install
```

Compile the icons file for Qt:
```
sudo apt-get install pyqt5-dev-tools
pyrcc5 icons.qrc -o gui/qt/icons_rc.py
```

If you tried the version of the plugin without s2c remove the db storing the info for generating the proof.
```
rm db_file.json
``` 

Run on `testnet`
```
./electrum --testnet
```

Run on `mainnet`
```
./electrum
```

## Create timestamps with your transactions
1. Enable the plugin:
    - `Tools -> Plugins -> Timestamp`, tick the checkbox
    - clicking on `Settings` you can switch from `op_return` to `sign-to-contract`, stay on the latter
    - close and restart Electrum to activate the plugin

2. Visualize your timestamp history list:
    - `Tools -> Timestamps` 

3. Start tracking the file(s) to timestamp:
    - click on `Add New File` and select the file

4. Create and broadcast a bitcoin transaction including the timestamp:
    - on the `Send Tab` select outputs, amount, fee
    - click on `Preview`
    - click on `S2C`, insert the password, 
    this will insert a commitment to the file you selected in the signature using *sign-to-contract*
    - click on `Broadcast`

5. Check the timestamp history:
    - `Tools -> Timestamps`, the file now is an pending state

6. Wait until the transaction is confirmed, you can now create the timestamp proof (`file_name.ots`):
    - `Tools -> Timestamps`, 
    click on `Upgrade`,
    the timestamp now is complete

You can find the timestamp proof next to the file(s):
```
/path/file_name.txt
/path/file_name.txt.ots
```

## Try the proof
Note that the `.ots` contains a `OpSecp256k1Commitment` so the standard OTS library won't recognize it.
Print it using the python library just installed,
for instance with `python3 ots-info.py "/path/file_name.txt.ots"`.
Then manually verify the correspondence with the hash of the file and the block header merkle root.

# Conclusions
Elliptic curve commitments commit values in elliptic curve points.
They can be used with public keys (*pay-to-contract*) or in signatures (*sign-to-contract*).
Although they have several uses, we confined ourselves to timestamping.
*pay-to-contract* drives you out of a BIP32 logic and may lead you to a loss of funds;
*sign-to-contract* does not, thus is better for timestamping purposes.

Nevertheless, *sign-to-contract* has some issues that should be addressed. 
If using segwit, remind that the proof is longer, 
contains arbitrary data by the miner and could be too large to create the OTS proof.

As of writing, `OpSecp256k1Commitment` is not yet part of 
the standard library python-opentimestamps,
thus proofs including it won't be retained valid by the standard clients.
If and when the PR will be merged, 
this new commitment operation can become part of valid timestamps.

*sign-to-contract* can be integrated in every Bitcoin signing software 
and can produce OTS proofs with zero marginal cost. 
We integrated it with Electrum as a plugin: 
your purely financial transaction can also timestamp stuff with no extra charge.

Since the cost reduction, users may start to consistently help the calendar timestamping its Merkle tip, 
leading to more frequent timestamps for calendar clients.
However, even though this seems promising, it may expose clients to new risks, 
hence it requires some work to be implemented properly. 

__P.S.__ I would like to thank Riccardo Casatta, Peter Todd and Andrew Poelstra for reviewing this post.

__P.P.S.__ This blog post sums up the the content of my master's degree thesis. 
The work was done during an internship at Eternity Wall, 
which put me in the condition to properly understand the subject 
and deserves most credit. 
The complete research can be found [here](https://github.com/LeoComandini/Thesis),
contributions of any kind are more than welcome.

