---
title: Understanding Dynamic Global Properties
position: 1
description: Maintains global state information
exclude: true
---

### Intro

Dynamic Global Properties represents a set of values that are calculated during normal chain operations and reflect the current values of global blockchain properties.

The [API call](#example-method-call) returns an object containing information that changes every block interval such as the head block number, the total vesting fund, etc.
    
### Sections

* Fields
  * [head_block_number](#head_block_number)
  * [head_block_id](#head_block_id)
  * [time](#time)
  * [current_witness](#current_witness)
  * [current_supply](#current_supply)
  * [total_vesting_shares](#total_vesting_shares)
  * [witness_required_vesting](#witness_required_vesting)
  * [maximum_block_size](#maximum_block_size)
  * [current_aslot](#current_aslot)
  * [recent_slots_filled](#recent_slots_filled)
  * [participation_count](#participation_count)
  * [last_irreversible_block_num](#last_irreversible_block_num)
  * [average_block_size](#average_block_size-removed)
* [Not Covered](#not-covered)
* [Example Method Call](#example-method-call)
* [Example Output](#example-output)

### `head_block_number`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Block height at the head of the blockchain.  This represents the latest block produced by witnesses.

* example: `24155032`

### `head_block_id`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Used to implement TaPoS (Transaction as Proof of Stake).  The first 4 bytes (8 hex digits) of the block ID represents the block number.  E.g., `01709398` in hex is `24155032` in decimal.

* example: `0170939865fa4e3aa7fca8f8df35d23333fe0bee`
* see: [RIPEMD-160 hashes](https://en.wikipedia.org/wiki/RIPEMD#RIPEMD-160_hashes)

### `time`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Point in time (UTC) that the block was included in the chain.

Used to synchronize events like Hard Fork activation.

When attempting to calculate the validity of a transaction we need to lookup a past block and check its block hash and the time it occurred so we can calculate whether the current transaction is valid and at what time it should expire.

For new transactions, expirations originate from this time.

* example: `2018-07-14T01:19:51`

### `current_witness`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Account name of the current witness.

* example: `blocktrades`

### `current_supply`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

SPHTX currently in existence.

* example: `271546371.129 SPHTX`

### `total_vesting_shares`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

VESTS that are invested in SPHTX.

* example: `390950506702.452773 SPHTX`

### `witness_required_vesting`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

VESTS that are required to be invested in SPHTX to in order to become a witness.

* example: `300000.000000 SPHTX`

### `maximum_block_size`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Maximum block size is decided by the set of active witnesses which change every round.  Each witness posts what they think the maximum size should be as part of their witness properties, the median size is chosen to be the maximum block size for the round.
  
**Note:** the minimum value for `maximum_block_size` is defined by the protocol to prevent the network from getting stuck by witnesses attempting to set this too low.

* example: `65536`

### `current_aslot`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

The current absolute slot number.  Equal to the total number of slots since genesis.  Also equal to the total number of missed slots plus `head_block_number`.

* example: `24231997`

### `recent_slots_filled`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Used to compute witness participation.

* example: `340282366920938463463374607431768211455`

### `last_irreversible_block_num`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

The latest block number that has been confirmed by two thirds of all block producers and is thus irreversible.

* example: `24155017`

### `average_block_size` <span class="danger removed">Removed</span><a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Average block size is updated every block to be: `average_block_size = (99 * average_block_size + new_block_size) / 100`.  This property is used to update the `current_reserve_ratio` to maintain approximately *  50% or less utilization of network capacity.

* example: `9309`

### `Not Covered`<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

Fields not covered in this recipe are:

* `participation_count`

### Example Method Call<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

To retrieve the current results for [`alexandria_api.get_dynamic_global_properties`](/apidefinitions/#alexandria_api.get_dynamic_global_properties), we can retrieve the current state information using `curl`:

```bash
curl -s --data '{"jsonrpc":"2.0", "method":"alexandria_api.get_dynamic_global_properties", "params":{}, "id":1}' https://API_URL
```

### Example Output<a style="float: right" href="#sections"><i class="fas fa-chevron-up fa-sm" /></a>

```json
{
  "jsonrpc": "2.0",
  "result": {
    "properties": {
      "head_block_number": 58019,
      "head_block_id": "0000e2a396fb66a55ae156bf48ce26105b984826",
      "time": "1970-01-01T00:00:00",
      "current_witness": "initminer",
      "current_supply": "350015744.660303 SPHTX",
      "total_vesting_shares": "4169.375986 VESTS",
      "witness_required_vesting": "250000.000000 VESTS",
      "maximum_block_size": 262144,
      "current_aslot": 7835941,
      "recent_slots_filled": "340282366920938463463374607431768211455",
      "participation_count": 128,
      "last_irreversible_block_num": 57968,
      "average_block_size": 49
    }
  },
  "id": 1
}
```