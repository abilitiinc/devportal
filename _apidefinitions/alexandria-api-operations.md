---
title: Supported Operations
position: 2
exclude: true
---

Operation is internal type that blockchain is working with. Every transaction consists of some static fields like "ref_block_num",
 "expiration", "signatures", etc... that are always present + list of "operations" that may vary and actually express what is such transaction really supposed to write into blockchain.<br>

<blockquote class="warning">
    <h5>We provide helper methods that return the required format of some of the supported operations, but applications should not call these methods at all as the format does not change. Instead, SDK should take care of that.</h5>
</blockquote> 

List of supported operations by SophiaTX blockchain:
* [account_create](/apidefinitions/#alexandria_api_operations.create_account)
* [account_update](/apidefinitions/#alexandria_api_operations.update_account)
* [account_delete](/apidefinitions/#alexandria_api_operations.delete_account)
* [transfer](/apidefinitions/#alexandria_api_operations.transfer)
* [transfer_to_vesting](/apidefinitions/#alexandria_api_operations.transfer_to_vesting)
* [witness_update](/apidefinitions/#alexandria_api_operations.update_witness)
* [witness_stop](/apidefinitions/#alexandria_api_operations.stop_witness)
* [account_witness_vote](/apidefinitions/#alexandria_api_operations.vote_for_witness)
* [account_witness_proxy](/apidefinitions/#alexandria_api_operations.set_voting_proxy)
* [withdraw_vesting](/apidefinitions/#alexandria_api_operations.withdraw_vesting)
* [custom_json](/apidefinitions/#alexandria_api_operations.make_custom_json)
* [custom_binary](/apidefinitions/#alexandria_api_operations.make_custom_binary)
* feed_publish
* escrow_transfer
* escrow_approve
* escrow_dispute
* escrow_release
* request_account_recovery
* recover_account
* change_recovery_account
* reset_account
* set_reset_account
* [application_create](/apidefinitions/#alexandria_api_operations.create_application)
* [application_update](/apidefinitions/#alexandria_api_operations.update_application)
* [application_delete](/apidefinitions/#alexandria_api_operations.delete_application)
* [buy_application](/apidefinitions/#alexandria_api_operations.buy_application)
* [cancel_application_buying](/apidefinitions/#alexandria_api_operations.cancel_application_buying)
* witness_set_properties
* transfer_from_promotion_pool
* [sponsor_fees](/apidefinitions/#alexandria_api_operations.sponsor_account_fees)
* admin_witness_update
<br>
<br>

{% include api-template-operations.html api_data=site.data.apidefinitions.alexandria_api %}
