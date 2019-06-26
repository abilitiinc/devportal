---
title: SophiaTX Nodes
position: 5
exclude: true
---

There are basically 3 types of nodes(functionalities that a node can represent) on SophiaTX blockchain. 
All of them use the same binary, the only difference is in configuration:
* **Full Node** [(config)](https://github.com/SophiaTX/SophiaTX/blob/develop/contrib/fullnode_config.ini) - opened API's.
_To run a full node (ca. 4GB of memory is required at the moment)._
* **Seed Node** [(config)](https://) - opened p2p port for syncing blockchain state with other nodes
_To run a full node (ca. 2GB of memory is required at the moment)._
* **Witness Node** [(config)](https://github.com/SophiaTX/SophiaTX/blob/develop/contrib/fullnode_config.ini) - configured to create blocks and take part in blockchain consensus algorithm
_To run a full node (ca. 2GB of memory is required at the moment)._ 

Connection options / opened ports:
* \<url\>:**9191** : websocket endpoint
* \<url\>:**9193** : http endpoint
* \<url\>:**9194** : https endpoint - not supported on public nodes yet
* \<url\>:**60000** : p2p endpoint - syncing the blockchain state

Applications that interface directly with the SophiaTX public blockchain will need to connect to a `sophiatxd` node. 
Developers may choose to use one of the public API nodes that are available, or run their own instance of a node.

SophiaTX provides separate public [Full](#public-full-nodes) and [Seed](#public-seed-nodes) Nodes.

#### Public Full Nodes

Full Nodes have enabled API's plugins so all [Alexandria API calls](/apidefinitions/#apidefinitions-alexandria-api) are available. 

| URL                             | Owner          |
| ------------------------------- | -------------- |
| socket1.sophiatx.com            | @sophiatx      |
| socket2.sophiatx.com            | @sophiatx      |

#### Public Seed Nodes

Seed Nodes are only for syncing the state of blockchain. No API's are enabled (only p2p port 60000 is opened). 
Users can setup these as "p2p-seed-node" in config.

| URL                             | Owner          |
| ------------------------------- | -------------- |
| seednode1.sophiatx.com          | @sophiatx      |
| seednode2.sophiatx.com          | @sophiatx      |
| seednode3.sophiatx.com          | @sophiatx      |
| seednode4.sophiatx.com          | @sophiatx      |
| seednode5.sophiatx.com          | @sophiatx      |
| seednode6.sophiatx.com          | @sophiatx      |

#### Public Witness Nodes

There are no public witness nodes.

### Syncing blockchain (public network)

Normally syncing blockchain starts from very first, `0` genesis block. It might take long time to catch up with live network. 
Because it connectes to various p2p nodes in the SophiaTX network and requests blocks from 0 to head block. 
It stores blocks in block log file and builds up the current state in the shared memory file. 
But there is a way to bootstrap syncing by using trusted `block_log` file. 
The block log is an external append only log of the blocks. 
It contains blocks that are only added to the log after they are irreversible because the log is append only.

Trusted block log file helps to download blocks faster. SophiaTX, provides public downloadable 
[block_log](https://mega.nz/#!bJwCnQgQ!XincxIHD5XRl3vKqQT3xe4mgkqkeQ_8tKzD34tmXDek) and 
[block_log.index](https://mega.nz/#!Sc5gXShT!_6W8Ptu4HrU3TMUE5067RKP0qkNQADnJCy7d4Y-YyLw) files. 
As of April 2019, size of both files is ~1 GB.  

To download these files, megatools can be used on linux:
```
sudo apt-get install megatools
megadl '<url>'

Note: as url use block_log & block_log.index links above
```


Block log should be place in `blockchain` directory below `data_dir` and node should be started with `--replay-blockchain` 
to ensure block log is valid and continue to sync from the point of snapshot. 
Replay uses the downloaded block log file to build up the shared memory file up to the highest block stored in that snapshot and then 
continues with sync up to the head block.

Replay helps to sync blockchain in much faster rate, but as blockchain grows in size replay might also take some time to verify blocks. 

##### Few other tricks that might help: 

For Linux users, virtual memory writes dirty pages of the shared file out to disk more often than is optimal which results in sophiatxd 
being slowed down by redundant IO operations. These settings are recommended to optimize reindex time.

```
echo    75 | sudo tee /proc/sys/vm/dirty_background_ratio
echo  1000 | sudo tee /proc/sys/vm/dirty_expire_centisecs
echo    80 | sudo tee /proc/sys/vm/dirty_ratio
echo 30000 | sudo tee /proc/sys/vm/dirty_writeback_centisecs
```
<br>

Another settings that can be changed in `config.ini` is `flush-state-interval` - it is to specify a target number of blocks to process before flushing the chain database to disk. This is needed on Linux machines and a value of 100000 is recommended. It is not needed on OS X, but can be used if desired.
