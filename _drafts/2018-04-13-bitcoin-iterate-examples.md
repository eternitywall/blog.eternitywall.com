---
layout: post
title: bitcoin-iterate examples
subtitle: a powerful, understimated tool
author: riccardo.casatta
image: ""
---

op return count

./bitcoin-iterate/bitcoin-iterate --blockdir /mnt/optane/bitcoin/blocks/ --cache /tmp -q --output='%os' | grep ^6a | wc -l


any output count
./bitcoin-iterate/bitcoin-iterate --blockdir /mnt/optane/bitcoin/blocks/ --cache /tmp -q --output='' | wc -l


any output count
./bitcoin-iterate/bitcoin-iterate --blockdir /mnt/optane/bitcoin/blocks/ --cache /tmp -q --output='' | grep -v ^6a | sort | uniq | wc -l



./bitcoin-iterate/bitcoin-iterate --blockdir /mnt/optane/bitcoin/blocks/ --cache /tmp -q --output='O %bN %th %oN' --input 'I %bN %ih %ii' | /home/casatta/integer-compression/target/release/examples/utxo
