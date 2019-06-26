---
title: SophiaTX testnet
position: 6
exclude: true
---

SophiaTX blockchain software is written in C++ and in order to modify the source code you need some understanding of the C++ programming language. Each SophiaTX node runs an instance of this software, so in order to test your changes, you will need to know how to install dependencies which can be found in the [SophiaTX repo](https://github.com/SophiaTX/SophiaTX/blob/master/doc/building.md). This also means that some knowledge of System administration is also required. There are multiple advantages of running a testnet, you can test your scripts or applications on a testnet without extra spam on the live network, which allows much more flexibility to try new things. Having access to a testnet also helps you to work on new features and possibly submit new or improved pull requests to official the SophiaTX GitHub repository.

## SophiaTX's Testnet

SophiaTX maintains a live testnet to which you can connect. In the near future, we expect the chain id to update with every code change. To get the current chain id for any HF20+ SophiaTX testnet you can use the [database_api.get_version](/apidefinitions/#database_api.get_version) api call (curl example included for your convenience). Be sure to point it at an api node on the testnet for which you want information!

At the time of this writing, the connection information for SophiaTX's testnet is as follows: 
 
* ChainID: `46d82ab7d8db682eb1959aed0ada039a6d49afa1602491f93dde9cac3e8e6c32`
* Address prefix: `TST`
* API node: `https://...sophiatx.com`

## Running a Testnet Node

First, let's build `sophiatxd` specifically for testnet.  Recommended specs:

* `Ubuntu Server 16.04 LTS`
* `100GB HDD`
* `16GB RAM` (mostly needed for `sophiatxd` build)

```bash
TODO: provide testnet build instructions
```

### `config.ini`

```ini
log-console-appender = {"appender":"stderr","stream":"std_error"}
log-file-appender = {"appender":"p2p","file":"logs/p2p/p2p.log"}
log-logger = {"name":"default","level":"info","appender":"stderr"}
log-logger = {"name":"p2p","level":"warn","appender":"p2p"}

backtrace = yes
plugin = chain p2p webserver witness database_api network_broadcast_api block_api 

shared-file-dir = "blockchain"
shared-file-size = 12G
p2p-endpoint = 0.0.0.0:2001
webserver-http-endpoint = 0.0.0.0:8751
webserver-ws-endpoint = 0.0.0.0:8752

# testnet.sophiatx.com
p2p-seed-node = 
```

```bash
sophiatxd --data-dir=. --chain-id=46d82ab7d8db682eb1959aed0ada039a6d49afa1602491f93dde9cac3e8e6c32
```

Now let it sync, and you'll have a shiny new testnet seed node to play with.



## Custom Testnet

In order to create a custom, isolated, testnet separate from the SophiaTX's we need to modify a few things mentioned in the previous section.

In the file named `SophiaTX/libraries/protocol/include/SophiaTX/protocol/config.hpp`, we can see the first few lines dedicated to the Testnet section. The line starts with `#ifdef IS_TEST_NET`.

Let's say we want to create a custom testnet with an initial supply of **1,000,000 sphtx**. We can change `SOPHIATX_INIT_SUPPLY 1,000,000` and by changing `SOPHIATX_CHAIN_ID_NAME "testnet"`, **testnet** to **mytestnet** we will automatically get a unique Chain ID for our testnet. The address prefix can be set to something like **MTN** and of course, we need to change the public and private keys to the genesis account. Note that the genesis account will receive the entire pre-mined supply of 1,000,000. That way, you can execute a setup script to fund any newly created accounts. Such a custom testnet will not have any additional hardware requirements to run.

A minimum of 8GB RAM should be sufficient to run a custom testnet. Currently, SophiaTX only has Linux and Mac compiling guides to build. A testnet can either be hosted locally, on a rented AWS, or dedicated bare metal servers so one can start testing functionality, explore different APIs, and start developing.

One more crucial point to modify is to change the number of witnesses required to accept hardforks for a custom testnet, by default it is set to 17, we can change it to **1** `SOPHIATX_HARDFORK_REQUIRED_WITNESSES 1` so that only one node instance would be sufficient and the network will be still functional and fast.

Another thing to note is that you can start a new chain with all previous hardforks already accepted, by changing the file named `SophiaTX/blob/master/libraries/chain/database.cpp` with the following function:

`void database::init_genesis( uint64_t init_supply )` inside `try` add this line:

`set_hardfork( 19, true );`

This would mean that 19 hardforks have been accepted by witnesses and the new chain will start with all previous forks included.

After these changes, all we have to do is compile the source code and get the `sophiatxd` executable. And once we fire up the custom testnet we can start testing and experimenting.

