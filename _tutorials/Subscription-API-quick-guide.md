---
title: Subscription API guide
position: 1
---

# Subscription API quick guide
Subscription API is accessible as one of the daemon APIs. In order to work, it has to be enabled, as well as dependant APIs with params
```
--plugin custom_api --plugin subscribe_api
```

Once it is enabled, WebSocket subscriptions can be used. The only call supported now is ```custom_object_subscription```. Example of usage is:

```
wscat  --connect 127.0.0.1:8095
connected (press CTRL+C to quit)
> {"jsonrpc": "2.0", "method": "subscribe_api.custom_object_subscription", "params":{"return_id":22, "app_id":1, "account_name":"initminer", "search_type":"by_sender", "start":1},  "id": 1}
< {"jsonrpc":"2.0","result":1,"id":1}
(now the messages comes)
< {"method":"notice","params":[22,[2,{"id":3,"sender":"initminer","recipients":["initminer"],"app_id":1,"data":"{}","received":"2018-08-23T11:55:12","binary":false}]]}
```