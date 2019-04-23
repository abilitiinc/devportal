---
title: SophiaTX Blockchain
position: 2
description:
---


SophiaTX is Delegated Proof of Stake blockchain. The source code is publicly available on [github](https://github.com/SophiaTX/SophiaTX). 

#### Public Announcement & Discussion

SophiaTX was announced on the
[Bitcointalk forum](https://bitcointalk.org/index.php?topic=2214715.0) prior to
the start of any mining.

#### Whitepaper
 
You can read the SophiaTX Whitepaper at [SophiaTXWhitePaper.pdf](https://www.sophiatx.com/storage/web/SophiaTX_Whitepaper_v1.9.pdf).

#### Quickstart

Just want to get up and running quickly? We have pre-built docker images for your convenience. More details are in our [technical quickstart guide](/quickstart/#quickstart-technical-quickstart).

#### Downloading

Latest release can be always found [here](https://github.com/SophiaTX/SophiaTX/releases)

#### Building

If you would like to build from source, we do have [build instructions](/quickstart/#quickstart-build-instructions) for Linux (Ubuntu LTS).

#### Config File

Run `sophiatxd` once to generate a data directory and config file. The default location is `witness_node_data_dir`. Kill `sophiatxd`. 
It won't do anything without seed nodes. If you want to modify the config to your liking, we have two example configs. 
( [full node](https://github.com/SophiaTX/SophiaTX/blob/develop/contrib/fullnode_config.ini), 
[witness node](https://github.com/SophiaTX/SophiaTX/blob/develop/contrib/witness_config.ini) ) 
All options will be present in the default config file and there may be more options needing to be changed from 
those configs (some of the options actually used in images are configured via command line).

#### Seed Nodes

A list of some seed nodes to get you:
<li>seednode1.sophiatx.com:60000</li>
<li>seednode2.sophiatx.com:60000</li>
<li>seednode3.sophiatx.com:60000</li>
<li>seednode4.sophiatx.com:60000</li>
<li>seednode5.sophiatx.com:60000</li>
<li>seednode6.sophiatx.com:60000</li>
<br>

#### System Requirements

For a full web node, you need at least 110GB of disk space available. SophiaTXd uses a memory mapped file which currently holds 56GB of data and by default is set to use up to 80GB. The block log of the blockchain itself is a little over 27GB. It's highly recommended to run sophiatxd on a fast disk such as an SSD or by placing the shared memory files in a ramdisk and using the `--shared-file-dir=/path` command line option to specify where. At least 16GB of memory is required for a full web node. Seed nodes (p2p mode) can run with as little as 4GB of memory with a 24 GB state file. Any CPU with decent single core performance should be sufficient. SophiaTXd is constantly growing, so you may find you need more disk space to run a full node. We are also constantly working on optimizing SophiaTX's use of disk space.

<br>
<br>
Below are SophiaTX specific terms that will help you understand documentation, whitepapers, and the speech of SophiaTX.