---
title: Supported Operations
position: 2
exclude: true
---

An Operation is internal type that blockchain is working with. Every transaction consists of some static fields like "ref_block_num",
 "expiration", "signatures", etc... that are always present + list of "operations" that may vary and actually express what is such transaction really supposed to write into blockchain.<br><br>
 
Each type of operation express different data structure that can be saved into blockchain. The main API call to send transaction with user defined operations is: 
[alexandria_api.broadcast_transaction](/apidefinitions/#alexandria_api.broadcast_transaction)<br>
There are also simplified methods that enable this functionality as well but are more suited for testing than production development:<br>
[alexandria_api.send_and_sign_operation](/apidefinitions/#alexandria_api.send_and_sign_operation)<br>
[alexandria_api.send_and_sign_transaction](/apidefinitions/#alexandria_api.send_and_sign_transaction)<br><br>

All other [alexandria_api method's](/apidefinitions/#alexandria_api/) (except of a few helper method to create transaction, etc...) are <b>ONLY FOR READING DATA</b> from blockchain !!!


<blockquote class="success">
    <h5>Final required format of the operations is always in "Example. op" section.</h5>
</blockquote> 

<blockquote class="warning">
    <h5>We provide helper methods that return the required format of some of the supported operations, but applications should not call these methods at all as the format does not change. Instead, SDK should take care of that.</h5>
</blockquote> 

{% include api-template-operations.html api_data=site.data.apidefinitions.alexandria_api %}
