---
title: Hello World on SophiaTX
position: 1
---

# Hello World example
This is just simple example of sending Hello World message proper way in our blockchain with help of our alexandria_deamon
## Creating account
First step is to create second account (recipient of message). For this step you need to have one account already created, if not please use our wallet.
### Generating keys
New account requires new pair of keys, we use 3 different key_pairs in our blockchain, but for this simple example I will same key for all 3 different keys. So to generate key pair you need to execute this command 

``` >>>generate_key_pair ```

response:
```
{
  "pub_key": "SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ",
  "wif_priv_key": "5KdvUxpR2NTdQHV91qT9K7g8oHvNz6F6vyhnGDUNUC1KYpaYH9r"
}
```
### Creating account
So now, when the key pair is ready I can create create_account operation
```
create_account registered_account test_account "{}" SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ
```
response:
```
[
  "account_create",{
    "fee": "0.100000 SPHTX",
    "creator": "4PMRaUBMJmL89eWcb1XrpL7DFGhJ",
    "name_seed": "test_account",
    "owner": {
      "weight_threshold": 1,
      "account_auths": [],
      "key_auths": [[
          "SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ",
          1
        ]
      ]
    },
    "active": {
      "weight_threshold": 1,
      "account_auths": [],
      "key_auths": [[
          "SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ",
          1
        ]
      ]
    },
    "memo_key": "SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ",
    "json_metadata": "{}"
  }
]
```
### Signing and broadcasting transaction
Now when operation is ready, we need to create transaction from it, sign it and broadcast to blockchain node. It is possible to use specific function for each of these steps, but there is one function that will do everything for us, but do not use this function on network, because it will expose your private key!
```
>>>send_and_sign_operation operation_from_last_step registered_account_private_key
```
### Checking if account was created
Now we should check if account was successfully created and for that we can use function:
```
>>>get_account test_account
```
response:
```
{
  "id": 3,
  "name": "4PMRaUBMJmL89eWcb1XrpL7DFGhJ",
  "owner": {
    "weight_threshold": 1,
    "account_auths": [],
    "key_auths": [[
        "SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ",
        1
      ]
    ]
  },
  "active": {
    "weight_threshold": 1,
    "account_auths": [],
    "key_auths": [[
        "SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ",
        1
      ]
    ]
  },
  "memo_key": "SPH667jA4gwFxRkSpKFAyWTDbzydRF1JPQrDc4ReqtpeujwvVUDfQ",
  "json_metadata": "",
  "voting_proxy": "11111111111111111111",
  "balance": "0.000000 SPHTX",
  "vesting_shares": "0.000000 VESTS",
  "vesting_withdraw_rate": "0.000000 VESTS",
  "to_withdraw": 0,
  "witness_votes": [],
  "sponsored_accounts": [],
  "sponsoring_account": "11111111111111111111"
}
```
## Creating application
Next step is to create application, that we will use for sending custom document
```
>>>create_application 4PMRaUBMJmL89eWcb1XrpL7DFGhJ TESTAPP "www.sophiatx.com" "" 0
```

result:
```
[
  "application_create",{
    "fee": "0.000000 SPHTX",
    "author": "4PMRaUBMJmL89eWcb1XrpL7DFGhJ",
    "name": "TESTAPP",
    "url": "www.sophiatx.com",
    "metadata": "",
    "price_param": 0
  }
]
```
Now you need to sign and broadcast it as in create account.
### Checking if account was created
```
>>>get_applications ["TESTAPP"]
```
result:
```
[{
    "id": 1,
    "name": "TESTAPP",
    "author": "4PMRaUBMJmL89eWcb1XrpL7DFGhJ",
    "url": "www.sophiatx.com",
    "metadata": "",
    "price_param": "permanent"
  }
]
```
## Creating custom json operation
Now the final step sending message Hello world. For that we will use make_custom_json_operation.
```
make_custom_json_operation 1 GN3Ug8ou6tiE4kWvyopJBJmZgcw ["4PMRaUBMJmL89eWcb1XrpL7DFGhJ"] "{\"Hello World\"}"
```
result:
```
[
  "custom_json",{
    "fee": "0.000000 SPHTX",
    "sender": "GN3Ug8ou6tiE4kWvyopJBJmZgcw",
    "recipients": [
      "4PMRaUBMJmL89eWcb1XrpL7DFGhJ"
    ],
    "app_id": 1,
    "json": "{\"Hello World\"}"
  }
]
```
Now we need to sign and send it 
### Checking message
```
get_received_documents 1 GN3Ug8ou6tiE4kWvyopJBJmZgcw "by_sender" 10 1
```

result:
```
[[
    1,{
      "sender": "GN3Ug8ou6tiE4kWvyopJBJmZgcw",
      "recipients": [
        "4PMRaUBMJmL89eWcb1XrpL7DFGhJ"
      ],
      "app_id": 1,
      "data": "{\"Hello World\"}",
      "received": "2018-07-10T11:37:42",
      "binary": false
    }
  ]
]
```


