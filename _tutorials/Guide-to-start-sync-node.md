---
title: How to start sync node
position: 1
---

### Download latest binaries
Go [here](https://github.com/SophiaTX/SophiaTX/releases) and download latest binaries.
### Extract the binaries
Please run this command to extract the binaries `tar -xzf BINARY_NAME`
### Start a node
Please execute following command to run the node `./sophiatxd` and kill it (Ctrl + C)
### Modify configuration
Now you need to modify config.ini file, to have proper full node. Execute following commands:
```
cd witness_node_data_dir/
nano config.ini
```
And add following configuration to your config.ini
```
plugin = condenser_api
plugin = chain_api
plugin = database_api
plugin = block_api

webserver-ws-endpoint = 0.0.0.0:8090
``` 
When you finished you can save document with Ctrl+x and then press Y to approve changes.

### Rerun the node
Now go back and rerun the node
```
cd ..
./sophiatxd
```
### Check if node is syncing
Open new terminal window and run wallet `./cli_wallet` when wallet is running set up a password for wallet and execute info command to check if node is syncing.
```
set_password YOUR_PASSWORD
unlock YOUR_PASSWORD
info
```
Output should look like something like this
```
{
  "head_block_number": 22646,
  "head_block_id": "00005876350c332f4009a37c55fc9147902b8eb1",
  "time": "2018-07-26T07:15:36",
  "current_witness": "initminer",
  "current_supply": "349999999.780000 SPHTX",
  "total_vesting_shares": "2250001.000000 VESTS",
  "witness_required_vesting": "250000.000000 VESTS",
  "maximum_block_size": 262144,
  "current_aslot": 24312,
  "recent_slots_filled": "340282366920938463463374607431768211455",
  "participation_count": 128,
  "last_irreversible_block_num": 22595,
  "average_block_size": 0,
  "witness_majority_version": "0.0.0",
  "hardfork_version": "1.0.0",
  "head_block_age": "2 seconds old",
  "participation": "100.00000000000000000",
  "median_sbd1_price": {
    "base": "0.000000 SPHTX",
    "quote": "0.000000 SPHTX"
  },
  "median_sbd2_price": {
    "base": "0.000000 SPHTX",
    "quote": "0.000000 SPHTX"
  },
  "median_sbd3_price": {
    "base": "0.000000 SPHTX",
    "quote": "0.000000 SPHTX"
  },
  "median_sbd4_price": {
    "base": "0.000000 SPHTX",
    "quote": "0.000000 SPHTX"
  },
  "median_sbd5_price": {
    "base": "0.000000 SPHTX",
    "quote": "0.000000 SPHTX"
  },
  "account_creation_fee": "0.050000 SPHTX"
}
```
You can rerun this command a few times and see the head_block_age is up to date

### Setting up seednode
If you have followed all steps, now you can create a seednode and be official part of our network. To do that you just need to edit following line in config.ini .
```
p2p-endpoint = 0.0.0.0:60000 
```
Please be sure, that your node is open for p2p traffic. 