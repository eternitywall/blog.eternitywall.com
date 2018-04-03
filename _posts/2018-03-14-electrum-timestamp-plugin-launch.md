---
layout: post
title: OpenTimestamps meets Electrum
subtitle: An Electrum plugin to create OpenTimestamp proofs with your transactions
author: leonardo.comandini
image: "/img/electrum-timestamp-plugin/electrum-meets-opentimestamps-thumbnail.png"
---

*Have you ever timestamped?*

Being more precise, did you ever create an OpenTimestamps proof all by yourself?
To do so you need to send a bitcoin transaction incluing some extra data which are a commitment to, let's say, your files `my_first_timestamp.txt` and `my_second_timestamp.txt`.
Then for each file you should create a `.ots` receipt containing the proof linking the file to the merkle root in the block header, using the OpenTimestamps standard.

This is not extremely easy to do, you should be familiar with the OpenTimestamps libraries and a software to create custom bitcoin transactions. 
While trying you may fail, wasting some bytes in your transaction or even worse some of your bitcoins.
But now there is an easy way: you can timestamp your files with your transactions using this Electrum plugin.

![electrum-meets-opentimestamps]({{ site.baseurl }}/img/electrum-timestamp-plugin/electrum-meets-opentimestamps.png)

*Note:* except for educational purposes or particular cases, creating a timestamp in this way is the **dumb** way to do a timestamp. 
The smart way is using a public *calendar* service: it's cheaper, requires less efforts and burdens less on the network.

# Getting started

In this launch phase the plugin is not super easy to install or download, 
we plan to make this easier after more reviews and tests.
In addition we tried to make it the less invasive possible, 
we used the existing hooks and we even haven't (yet) added any fancy OpenTimestamps picture.
In the next phases we will make some steps to provide a nicer user experience.

To use the plugin you need to install a custom version of Electrum including the plugin.

The instructions are adapted from [Electrum README](https://github.com/spesmilo/electrum#development-version)
and it is assumed to run on Linux.

(For Mac OS X use `brew install` instead of `sudo apt-get install`, instead of `python-pyqt5` and `pyqt5-dev-tools` install `pyqt5` and check that it is using `python3`. This should do the job.)

Electrum is a pure python application. 
If you want to use the Qt interface, install the Qt dependencies:

```
$ sudo apt-get install python3-pyqt5
```

Install plugin requirements:
```
$ pip3 install opentimestamps
```

Clone the Electrum source code, then include the plugin files:
```
$ git clone https://github.com/spesmilo/electrum.git
$ git clone https://github.com/LeoComandini/electrum-timestamp-plugin.git
$ cp -r electrum-timestamp-plugin/timestamp electrum/plugins
$ cd electrum
$ python3 setup.py install
```

Compile the icons file for Qt:
```
sudo apt-get install pyqt5-dev-tools
pyrcc5 icons.qrc -o gui/qt/icons_rc.py
```

To run on `mainnet`
```
$ ./electrum
```

To run on `testnet` 
```
$ ./electrum --testnet
```

# Timestamp your files with your transaction

## Enable the plugin

Click on `Tools` -> `Plugins` and tick the `Timestamp` checkbox

![Activate-plugin]({{ site.baseurl }}/img/electrum-timestamp-plugin/activate-timestamp-plugin.png)

Close and restart to Electrum to abilitate the plugin.

## Visualize the timestamps history

Click on `Tools` -> `Timestamps…`

![tools-timestamps]({{ site.baseurl }}/img/electrum-timestamp-plugin/tools-timestamps.png)

## Add new files

Click on the `Add New File` button and select the file yo want to timestamp.

![add-new-file]({{ site.baseurl }}/img/electrum-timestamp-plugin/add-new-file.png)

## Create and broadcast a bitcoin transaction including the timestamp:
On the `Send Tab` select outputs, amount, fee
(select a fee > 1 sat/byte) 

Click on `Preview`

![preview]({{ site.baseurl }}/img/electrum-timestamp-plugin/preview.png)

Click on `Timestamp`, this write a commitment to your file(s) in the transaction 

![pre-timestamp]({{ site.baseurl }}/img/electrum-timestamp-plugin/pre-timestamp.png)

Confirm the changes in the transaction 
(if fee is too low the transaction won't be relayed or mined)

![post-timestamp]({{ site.baseurl }}/img/electrum-timestamp-plugin/post-timestamp.png)

Sign and broadcast the transaction (click on `Sign`, then `Broadcast`)

*Note:* the hash value in the red box is the Merkle root resulting from the hashes of the files along with nonces to guarantee extra privacy. 

## Check the timestamps history

Click on `Tools -> Timestamps…`, the file now is an pending state.

![pending-commitment]({{ site.baseurl }}/img/electrum-timestamp-plugin/pending-commitment.png)

If you try to click on `upgrade` nothing will happen,
to create your timestamps you need to wait to have the transaction is confirmed (6 blocks) so reorgs won't be an issue.

## Upgrade

After 6 blocks clicking on `upgrade` will do something, your timestamps are now completed.

![post-upgrade]({{ site.baseurl }}/img/electrum-timestamp-plugin/post-upgrade.png)

Your timestamp proofs are next to the file you timestamped.

![files-and-proofs]({{ site.baseurl }}/img/electrum-timestamp-plugin/files-and-proofs.png)

## Verify your proofs

Use the OpenTimestamps client (better) or go on [opentimestamps.org](https://opentimestamps.org) (suboptimal, you are trusting the website) to verify your proofs.

![pre-verify]({{ site.baseurl }}/img/electrum-timestamp-plugin/pre-verify.png)

![post-verify]({{ site.baseurl }}/img/electrum-timestamp-plugin/post-verify.png)

# Conclusions

With this plugin you can easily timestamp your files while doing bitcoin transactions with Electrum.

While it may be sometimes useful, it is better to use a *calendar*.

However this could become relevant if *sign-to-contract* or external timestamping becomes a thing. 
Stay tuned.
