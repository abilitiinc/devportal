---
title: How to become witness
position: 1
---

# Setup witness node
### Download wallet and node daemon 
Go to https://github.com/SophiaTX/SophiaTX/releases and download latest version. 
### Prepare node
Start node for the first time with following command:

`./sophiatxd`

Now go to kill the daemon and go to witness_node_data_dir/ and edit config.ini

```cd witness_node_data_dir/```

And modify this properties:
```
plugin = alexandria_api
plugin = network_broadcast_api

webserver-ws-endpoint = 0.0.0.0:9191
```
Now start `cli_wallet`, create new wallet file and key for witness

```
./cli_wallet
new >>> set_password YOUR_PASSWORD
locked >>> unlock YOUR_PASSWORD
unlocked >>> import_key YOUR_PRIVATE_KEY
unlocked >>> suggest_brain_key
```
Output should look like something like this

```
{
  "brain_priv_key": "BLENT EPOCHA GREED OCQUE BHALU TAPE SMUT AVERAH BALAO SHARPEN BUSTARD ATHIRST PACTION WIDBIN NYMPHA BANK",
  "wif_priv_key": "5J7ejuMu2ZkNWQ367X38wVvBWFzgbsjPvw6PLNtwsYEQrbhigT6",
  "pub_key": "SPH6m3vR9ZfRiGCSmpdY8G6kaAjXxGYBxu6pRE99cFL95SEcxEWeh"
}
```
Now kill the daemon and modify config.ini
Now you can reedit config.ini with following properties (you can remove rest of the plugins if you want to be just the witness)

```
plugin = witness

witness = "YOUR_ACCOUNT_NAME"

private-key = 5J7ejuMu2ZkNWQ367X38wVvBWFzgbsjPvw6PLNtwsYEQrbhigT6 (just example)

enable-stale-production = false

required-participation = 35
```
And rerun the daemon with command 
``` ./sophiatxd ```

Now you need to vest tokens to you account and update yourself as witness
```
unlocked >>> transfer_to_vesting YOUR_ACCOUNT_NAME YOUR_ACCOUNT_NAME "250000 SPHTX" true
unlocked >>> update_witness YOUR_ACCOUNT_NAME "YOUR_URL" SPH6m3vR9ZfRiGCSmpdY8G6kaAjXxGYBxu6pRE99cFL95SEcxEWeh {"account_creation_fee":"0.05 SPHTX","maximum_block_size":50331648,"price_feeds":[]} true
```

### Check if your setup is right
Go to cli_wallet and execute command: `get_witness YOUR_WITNESS_NAME` and if you check the output, you should be able to you last confirmed block

### Setup price feeding service
Install the price feeder script: `npm install -g sophiatx-price-feeder`. 

Once done, test it with:

 `feeder <YOUR_ACCOUNT_NAME> <YOUR_MINING_KEY>`, e.g. `feeder <YOUR_ACCOUNT_NAME> 5J7ejuMu2ZkNWQ367X38wVvBWFzgbsjPvw6PLNtwsYEQrbhigT6`. Use your actual mining key, as entered into config.ini.

Once the script functionality is checked, run it regularly, at lease once per day. You can use e.g. crond service on Linux:

```
crontab
0 * * * * /usr/local/bin/feeder <YOUR_ACCOUNT_NAME> <YOUR_MINING_KEY>
<ctrl-D>
```

## Setup NTP Time Synchronization
### Suse
[Suse guide](https://www.suse.com/documentation/sles11/book_sle_admin/data/sec_netz_xntp_netconf.html)
### Ubuntu
Before installing ntpd, you should turn off timesyncd:
```
sudo timedatectl set-ntp no
```
Please install ntpd by executing following command:
```
sudo apt-get install ntp
```
ntpd will be started automatically after install. You can verify that everything is working by following command:
```
sudo ntpq -p
```
Example:
```
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 0.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 1.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 2.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 3.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 ntp.ubuntu.com  .POOL.          16 p    -   64    0    0.000    0.000   0.000
 ec2-52-17-231-7 193.120.142.71   2 u    -   64    1    0.483   -1.022   0.025
 ec2-52-209-118- 89.101.218.6     2 u    1   64    1    0.679   -1.663   0.267
 tshirt.heanet.i .PPS.            1 u    -   64    1    3.800   -0.999   0.017
 meg.magnet.ie   195.66.241.2     2 u    -   64    1    1.549   -0.667   0.276
 ec2-34-242-217- 89.101.218.6     2 u    -   64    1    0.817    1.451   0.092
 t1.time.ir2.yah 212.82.106.33    2 u    1   64    1    1.666   -0.939   0.005
 mirror.datacent 192.36.144.22    2 u    1   64    1   48.287   -0.197   0.036
 pugot.canonical 17.253.34.125    2 u    -   64    1   11.398   -1.159   0.000
 chilipepper.can 145.238.203.14   2 u    1   64    1   10.779   -1.549   0.000

``` 


