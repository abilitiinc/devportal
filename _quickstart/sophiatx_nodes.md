---
title: SophiaTX Nodes
position: 2
exclude: true
---

Applications that interface directly with the SophiaTX blockchain will need to connect to a `sophiatxd` node. Developers may choose to use one of the public API nodes that are available, or run their own instance of a node.

### Public Nodes

| URL                             | Owner          |
| ------------------------------- | -------------- |
| ...............                 | @sophiatx      |
| ...............                 | @sophiatx      |


### Private Nodes

The simplest way to get started is by deploying a pre-built node.

##### p2p Node

_To run a p2p node (ca. 2GB of memory is required at the moment):_

##### Full Node

_To run a node with all the data (e.g. for supporting a content website) that uses ca. 14GB of memory and growing:_

### Syncing blockchain

Normally syncing blockchain starts from very first, `0` genesis block. It might take long time to catch up with live network. Because it connectes to various p2p nodes in the SophiaTX network and requests blocks from 0 to head block. It stores blocks in block log file and builds up the current state in the shared memory file. But there is a way to bootstrap syncing by using trusted `block_log` file. The block log is an external append only log of the blocks. It contains blocks that are only added to the log after they are irreversible because the log is append only.

Trusted block log file helps to download blocks faster. SophiaTX, provides public block log file which can be downloaded from [here](https://...) and there is also option from community witness `@gtg` which can be downloaded from [here](...).

Both `block_log` files updated periodically, as of May 2018 uncompressed `block_log` file size ~110 GB. Docker container on `stable` branch of SophiaTX source code has option to use `USE_PUBLIC_BLOCKLOG=1` to download latest block log and start SophiaTX node with replay.

Block log should be place in `blockchain` directory below `data_dir` and node should be started with `--replay-blockchain` to ensure block log is valid and continue to sync from the point of snapshot. Replay uses the downloaded block log file to build up the shared memory file up to the highest block stored in that snapshot and then continues with sync up to the head block.

Replay helps to sync blockchain in much faster rate, but as blockchain grows in size replay might also take some time to verify blocks. 

##### Few other tricks that might help: 

For Linux users, virtual memory writes dirty pages of the shared file out to disk more often than is optimal which results in sophiatxd being slowed down by redundant IO operations. These settings are recommended to optimize reindex time.

```
echo    75 | sudo tee /proc/sys/vm/dirty_background_ratio
echo  1000 | sudo tee /proc/sys/vm/dirty_expire_centisecs
echo    80 | sudo tee /proc/sys/vm/dirty_ratio
echo 30000 | sudo tee /proc/sys/vm/dirty_writeback_centisecs
```

Another settings that can be changed in `config.ini` is `flush` - it is to specify a target number of blocks to process before flushing the chain database to disk. This is needed on Linux machines and a value of 100000 is recommended. It is not needed on OS X, but can be used if desired.
