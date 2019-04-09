---
title: Multiparty Encryption
position: 1
---

Multiparty encryption operates in a closed group of participants, managed by one admin. Multiparty messaging is implemented as an application on top of SophiaTX's `custom_json_operation`.

# Use cases

## Group creation
Group is created by assigning it a new random name ("group address"), a group key, and individually inviting initial participants to it. The invite is encrypted and readable only to the sender (admin) and recipient (new group member).

## Group update
Group can be updated any time with new name and/or new group key. Whenever new participants are being added or deleted, the group key must be updated. Group name might be optionally updated. Regular updates of the group will prevent external observers to map group activity, i.e. the potential attacker might not even learn of the existence of the group, its lifetime, and/or members. The group updates are updated to the group address and encrypted using the group key, and thus readable only to group members. 

## Group messaging
Any member of the group can send message to the group address. This message is encrypted using the group key.

## Participant list change
Whenever admin adds new group member(s), he updates the group key and sends invite to these new members. Updating the group key prevents new members to be able to read old messages.
Whenever admin removes participants from the group, new group address and group key has to be generated. The new group key is encrypted in the way that only remaining participants can read it.

## Group disband
Admin can discontinue the group by disbanding it. No new messages, updates or messages, can be sent to the group anymore.

## Receiving group messages
Group participants can read the new group messages only if they know the actual group name (group address) and group key. To keep track, the group updates needs to be monitored and group parameters updated accordingly. 

# Message format
There are three basic types of messages: 
* Group invites
* Group updates
* Group message

First two are so called system message. They don't carry the actual communication between participants, just the various group parameters. The last one, group message, is carrying actual information between users.

## Group invite
Group invite is sent from admin to individual user. The format of the JSON payload is as follows:
```json
{
  "type" : 0,                       //<aka "system message"
  "operation_data" : {
    "version" : 1,                  //<always 1
    "type" : "add",
    "new_group_name" : "<NEW GROUP NAME>",
    "description" : "<A SHORT DESCRIPTION OF THE PURPOSE OF THE GROUP>",
    "user_list" : ["<OPTIONAL LIST OF OTHER PARTICIPANTS>"],
    "senders_pubkey" : "<ADMIN PUBLIC MEMO KEY>",
    "new_key" : [["<RECIPIENT PUBLIC MEMO KEY>", "<GROUP KEY ENCRYPTED WITH SENDER-RECIPIENT SHARED SECRET>"]]
  }
}
```
NOTE: Public memo keys are stored in the structure for references. In case one of the participants changes her memo key, one can easily find which key was used in construction of the shared secret. 

This payload is then encrypted using sender-recipient shared secret, and packed into a JSON wrapper:
```json
{
  "sender": "<ADMIN PUBLIC MEMO KEY>",
  "recipient": "<RECIPIENT PUBLIC MEMO KEY>",
  "data" : "<HEX ENCODED BYTE ARRAY OF THE ENCRYPTED PAYLOAD>"
}
```

## Group update
Group update is sent from admin to the actual group address. 
The format of the JSON payload is as follows:
```json
{
  "type" : 0,                       //<aka "system message"
  "operation_data" : {
    "version" : 1,                  //<always 1
    "type" : "update",             
    "new_group_name" : "<NEW GROUP NAME>", //optional parameter, in case group name changes. Can be omitted.
    "description" : "<A SHORT DESCRIPTION OF THE PURPOSE OF THE GROUP>", //optional parameter, in case group description changes. Can be omitted.
    "user_list" : ["<OPTIONAL LIST OF OTHER PARTICIPANTS>"],
    "senders_pubkey" : "<ADMIN PUBLIC MEMO KEY>",
    "new_key" : [["SEE BELOW"]]
  }
}
```

Would the group key change, the new key is encoded either with group key or with shared secrets for each individual group participant. The later must be used in case of deleting participants. 
The first case has then the following format:
```json
"new_key" : [["SPH1111111111111111111111111111111114T1Anm", "<ENCRYPTED_GROUP_KEY>"]]
```

The second case will end in a list of encrypted keys:
```json
"new_key" : [
    ["<RECIPIENT1 PUBLIC MEMO KEY>", "<GROUP KEY ENCRYPTED WITH SENDER-RECIPIENT1 SHARED SECRET>"],
    ["<RECIPIENT2 PUBLIC MEMO KEY>", "<GROUP KEY ENCRYPTED WITH SENDER-RECIPIENT2 SHARED SECRET>"],
(...)
    ["<RECIPIENT_N PUBLIC MEMO KEY>", "<GROUP KEY ENCRYPTED WITH SENDER-RECIPIENT_N SHARED SECRET>"]
]
```

This payload is then encrypted using group key, and packed into a JSON wrapper:
```json
{
  "iv": "<INITIAL VECTOR FOR AES>",
  "data" : "<HEX ENCODED BYTE ARRAY OF THE ENCRYPTED PAYLOAD>"
}
```

## Group message
Group update is sent from a group member to the actual group address. 

```json
{
  "type" : 1,
  "message_data" : "<HEX ENCODED COMMUNICATION>"
}
```

This payload is then encrypted using group key, and packed into a JSON wrapper:
```json
{
  "iv": "<INITIAL VECTOR FOR AES>",
  "data" : "<HEX ENCODED BYTE ARRAY OF THE ENCRYPTED PAYLOAD>"
}
```

# Encryption

As a base, AES256 is used. 

Group key is of length 256 bits. Initialisation vector of length 256 bits is randomly generated and added as a parameter to the messages where group key encryption is used. 

Shared secret between sender and recipient is derived from sender's private and recipient's public keys or recipient's private and sender's public keys using Elliptic curve integrated encryption scheme (ECIES) over SECP256K1 curve. The resulting 512 bits are split to halfs, one half used as IV and the other as key in AES256 schema.

# API
The multiparty messaging plugin was developed as a reference implementation. Its usability is limited to be run on end-user's node, and the node must not expose its API to general public. The plugin is started when the following parameters are added to the daemon when starting:
```
--plugin multiparty_messaging --mpm-app-id 2 --mpm-account <MYACCOUNT> --mpm-private-key <PRIVATE_MEMO_KEY>
```
NOTE: The `mpm-account` and `mpm-private-key` can be repeated more than once. 

Once the plugin is started, it starts listening for `custom_json_operations` with the given application ID and executes the logic as described above. It stores in database information about all past and active groups the user has been involved in, as well as all decoded messages. 
The API calls can be split into two logical part: 
* Retrieving the group and messages info from the database, namely `get_group`, `get_group_name`, `list_my_groups` and `list_messages`.  
* Preparing encrypted `custom_json_operations` payloads. The returned payloads has to be sent to the provided addresses either with Alexandria SDKs or cli_wallet. 

### Example 
First create the group invitation by user `initminer` using wscat:
```json
> {"jsonrpc":"2.0", "id":1, "method":"multiparty_messaging.create_group", "params":{"admin":"initminer", "description":"blah", "members":["kpX6yHkAb_RIEuQ7g7UVoGwpUKo"]}}
< {"jsonrpc":"2.0","result":{"group_name":"wY8SqXZDuH3SL0Im0G5vuZOch6g","operation_payloads":[["kpX6yHkAb_RIEuQ7g7UVoGwpUKo",{"sender":"SPH8HWPUynRQnH8QCHdCvebNKbKGuVpZ9FZpg3EWv9UN5qayA8v7k","recipient":"SPH7t5298LP6CDuC8SHT7PGaH7rvqCYgV6sCsrT55VLJXEuQXWnPw","data":"396a3370a56a188403c1185b6ac98d69d01a2c42699a986da80f1d762e54671d82a9097983a228da8635d157d24c01bd9fe20ea0c1a390a6d6c7a247eea80ab3b6f4649c1dc1d3539307a36e9f525f8bab3416ccaf1bac26ee539ea0e289489a1f0261ac9a9f5a95317c3fe62bd49b3b0a5f85db3708eab406e6f181deb6a6fce851154f7ded1495befd308a33abe7002798f4a3d45adbb0c52a97175dbe14e76850d40b05d1147e4d070320051c6c46de600307e5ad59aa0290deb6368e92974a98047f96c5b70a7be005dd455c1557"}]]},"id":1}
```
Group name `"wY8SqXZDuH3SL0Im0G5vuZOch6g"` was generated, as well as one invitation.
The returned invitation payload is then sent using cli_wallet:
```json
unlocked >>> send_custom_json_document 2 initminer ["kpX6yHkAb_RIEuQ7g7UVoGwpUKo"] "{\"sender\":\"SPH8HWPUynRQnH8QCHdCvebNKbKGuVpZ9FZpg3EWv9UN5qayA8v7k\",\"recipient\":\"SPH7t5298LP6CDuC8SHT7PGaH7rvqCYgV6sCsrT55VLJXEuQXWnPw\",\"data\":\"396a3370a56a188403c1185b6ac98d69d01a2c42699a986da80f1d762e54671d82a9097983a228da8635d157d24c01bd9fe20ea0c1a390a6d6c7a247eea80ab3b6f4649c1dc1d3539307a36e9f525f8bab3416ccaf1bac26ee539ea0e289489a1f0261ac9a9f5a95317c3fe62bd49b3b0a5f85db3708eab406e6f181deb6a6fce851154f7ded1495befd308a33abe7002798f4a3d45adbb0c52a97175dbe14e76850d40b05d1147e4d070320051c6c46de600307e5ad59aa0290deb6368e92974a98047f96c5b70a7be005dd455c1557\"}" true
{
  "ref_block_num": 2417,
  "ref_block_prefix": 3747953573,
  "expiration": "2018-12-13T13:34:00",
  "operations": [[
      "custom_json",{
        "fee": "0.000000 SPHTX",
        "sender": "initminer",
        "recipients": [
          "kpX6yHkAb_RIEuQ7g7UVoGwpUKo"
        ],
        "app_id": 2,
        "json": "{\"sender\":\"SPH8HWPUynRQnH8QCHdCvebNKbKGuVpZ9FZpg3EWv9UN5qayA8v7k\",\"recipient\":\"SPH7t5298LP6CDuC8SHT7PGaH7rvqCYgV6sCsrT55VLJXEuQXWnPw\",\"data\":\"396a3370a56a188403c1185b6ac98d69d01a2c42699a986da80f1d762e54671d82a9097983a228da8635d157d24c01bd9fe20ea0c1a390a6d6c7a247eea80ab3b6f4649c1dc1d3539307a36e9f525f8bab3416ccaf1bac26ee539ea0e289489a1f0261ac9a9f5a95317c3fe62bd49b3b0a5f85db3708eab406e6f181deb6a6fce851154f7ded1495befd308a33abe7002798f4a3d45adbb0c52a97175dbe14e76850d40b05d1147e4d070320051c6c46de600307e5ad59aa0290deb6368e92974a98047f96c5b70a7be005dd455c1557\"}"
      }
    ]
  ],
  "extensions": [],
  "signatures": [
    "1f6b1c7281e2d875aed831d9f117a1c65f84c5b806dcac88f395a9635247418a104a0c69b0935dcc848be8171c288ff56b8e3e944041c3e4000d93afb3d10bf8d1"
  ],
  "transaction_id": "e76c37b71a3ca23ddc044edd2224880295a0bce0",
  "block_num": 2418,
  "transaction_num": 0
}
unlocked >>>
```
We can check success of the operation again with wscat:
```json
> {"jsonrpc":"2.0", "id":2, "method":"multiparty_messaging.get_group", "params":{"group_name":"wY8SqXZDuH3SL0Im0G5vuZOch6g"}}
< {"jsonrpc":"2.0","result":{"group_name":"wY8SqXZDuH3SL0Im0G5vuZOch6g","current_group_name":"wY8SqXZDuH3SL0Im0G5vuZOch6g","description":"blah","members":["kpX6yHkAb_RIEuQ7g7UVoGwpUKo","initminer"],"admin":"initminer","group_key":"b019a8e962d111b020f0fd0181f3e59e6f7727076fce2486bc28cf8c0270a589","messages":1},"id":2}
```


