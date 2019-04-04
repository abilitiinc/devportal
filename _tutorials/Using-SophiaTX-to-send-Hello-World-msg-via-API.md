---
title: Hello World via API calls
position: 1
---

# Hello World example
This is just simple example of sending Hello World message proper way in our blockchain via public API.

## Creating custom json operation
To send Hello World via our API function to our blockchain, you need to fill this JSON structure.
JSON struct:
```
[
  "custom_json",{
    "sender": "USERNAME", //actual user account on blockchain, that will sign this transaction
    "recipients": [ //array of recipients, does not to be actuall account on blockchain, but you are free to used for your app logic
      "MSG_BOX" 
    ],
    "app_id": 777, //App id of your app
    "json": "{\"Hello World\"}" //escaped msg
  }
]
```
Then you can use this curl commnad to send it to blockchain.
private_key is private key of USERNAME
```
curl -v -s --data '{"id":0,"jsonrpc":"2.0","method":"alexandria_api.send_and_sign_operation", "params":{"op": ["custom_json",{"sender": "USERNAME", "recipients": [ "MSG_BOX" ],"app_id": 777, "json": "{ \"msg\" : \"Hello World\"}"}], "pk" : private_key}}' https://API_URL
```
resposne
```
{"jsonrpc":"2.0","result":{"signed_tx":{"ref_block_num":15641,"ref_block_prefix":973882209,"expiration":"2019-04-04T12:39:12","operations":[["custom_json",{"fee":"0.010000 SPHTX","sender":"user1","recipients":["MSG_BOX"],"app_id":777,"json":"{ \"msg\" :\"Hello World\"}"}]],"extensions":[],"signatures":["20044265aecd561bd3cf05bae0eab195698a16f5cba8eb8f69e930b4992be0144558437db2503fbbec748c29c8a667d44399e38d75e215138d1eb2ccd10c8f73f7"],"transaction_id":"418cf08ac9192aa309c31357d3caf0d3bc214362","block_num":81178,"transaction_num":0}},"id":0}
```

### Checking message
```
curl -v -s --data '{"id":0,"jsonrpc":"2.0","method":"alexandria_api.get_received_documents", "params":{ "app_id": 777, "account_name": "USERNAME", "search_type": "by_sender_reverse", "start": "1", "count": 10 }}' https://API_URL
```

response:
```
{"jsonrpc":"2.0","result":{"received_documents":[[1,{"id":0,"sender":"user1","recipients":["MSG_BOX"],"app_id":777,"data":"{ \"msg\" :\"Hello World\"}","received":"2019-04-04T12:38:42","binary":false}]]},"id":0}
```


