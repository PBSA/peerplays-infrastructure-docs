---
title: CLI Commands for SONs
description: CLI commands that sons use.
---

# CLI Commands for SONs

## 1. SONs CLI Command Reference

### 1.1. create\_son

Creates a SON object owned by the given account. An account can have at most one SON object.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::create_son(
    string owner_account,
    string url,
    vesting_balance_id_type deposit_id,
    vesting_balance_id_type pay_vb_id,
    flat_map<sidechain_type, string> sidechain_public_keys,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| owner\_account | string | The name or id of the account which is creating the SON. | no quotes required. |
| url | string | a URL to include in the SON record in the blockchain. Clients may display this when showing a list of SONs. | May be blank. |
| deposit\_id | vesting\_balance\_id\_type | vesting balance id for SON deposit. | This is the `son` vesting balance. |
| pay\_vb\_id | vesting\_balance\_id\_type | vesting balance id for SON pay\_vb | This is the `normal` vesting balance. |
| sidechain\_public\_keys | flat\_map | The new set of sidechain public keys. | n/a |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
create_son myaccountname-son "www.my-awesome-son.com" 1.13.79 1.13.80 [[bitcoin, 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9]] true

# The above command is structured like this:
# create_son <username> "<SON proposal url>" <son vesting balance ID> <normal vesting balance ID> [[bitcoin, <bitcoin public key>]] true
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
{
  "ref_block_num": number,
  "ref_block_prefix": number,
  "expiration": "datetime",
  "operations": [],
  "extensions": [],
  "signatures": []
}
```

**Example Successful Return**

```cpp
create_son myaccountname-son "www.my-awesome-son.com" 1.13.79 1.13.80 [[bitcoin, 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9]] true
{
  "ref_block_num": 10412,
  "ref_block_prefix": 1003801100,
  "expiration": "2015-02-05T00:11:00",
  "operations": [[
      101,{
        "fee": {
          "amount": 0,
          "asset_id": "1.3.0"
        },
        "owner_account": "1.2.12345",
        "url": "www.my-awesome-son.com",
        "deposit": "1.13.79",
        "signing_key": "PPY2j4hy47qY...",
        "sidechain_public_keys": [[
            "bitcoin",
            "bc1abc1234..."
          ]
        ],
        "pay_vb": "1.13.80"
      }
    ]
  ],
  "extensions": [],
  "signatures": [
    "268bd8....."
  ]
}
```
{% endtab %}
{% endtabs %}

### 1.2. update\_son

Update a SON object owned by the given account.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::create_son(
    string owner_account,
    string url,
    string block_signing_key,
    flat_map<sidechain_type, string> sidechain_public_keys,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| owner\_account | string | The name of the SON's owner account. Also accepts the ID of the owner account or the ID of the SON. | no quotes required. |
| url | string | Same as for create\_son. The empty string makes it remain the same. | n/a |
| block\_signing\_key | string | A new signing key to replace the currently set signing key. | n/a |
| sidechain\_public\_keys | flat\_map | The new set of sidechain public keys. An empty string makes it remain the same. | n/a |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
update_son myaccountname-son "www.my-awesome-son.com" PPY8kvUXLpoXE9rJHwppR48LkqSouAzFZomAPb3hW9gnkSHZCsozi [[bitcoin, 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9]] true

# The above command is structured like this:
# create_son <username> "<SON proposal url>" <new block signing public key> [[bitcoin, <bitcoin public key>]] true
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
{
  "ref_block_num": number,
  "ref_block_prefix": number,
  "expiration": "datetime",
  "operations": [],
  "extensions": [],
  "signatures": []
}
```

**Example Successful Return**

```cpp
update_son myaccountname-son "www.my-awesome-son.com" PPY8kvUXLpoXE9rJHwppR48LkqSouAzFZomAPb3hW9gnkSHZCsozi [[bitcoin, 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9]] true
{
  "ref_block_num": 10412,
  "ref_block_prefix": 1003801100,
  "expiration": "2015-02-05T00:11:00",
  "operations": [[
      102,{
        "fee": {
          "amount": 0,
          "asset_id": "1.3.0"
        },
        "son_id": "1.33.99",
        "owner_account": "1.2.12345",
        "new_url": "www.my-awesome-son.com",
        "new_signing_key": "PPY8kvUXLpoXE9rJHwppR48LkqSouAzFZomAPb3hW9gnkSHZCsozi",
        "new_sidechain_public_keys": [[
            "bitcoin",
            "bc1..."
          ]
        ]
      }
    ]
  ],
  "extensions": [],
  "signatures": [
    "268bd8....."
  ]
}
```
{% endtab %}
{% endtabs %}

### 1.3. update\_son\_vesting\_balances

Updates the vesting balances associated with a given SON.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::update_son_vesting_balances(
    string owner_account,
    optional<vesting_balance_id_type> new_deposit,
    optional<vesting_balance_id_type> new_pay_vb,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| owner\_account | string | The name or id of the SON account owner, or the id of the SON. | No quotes required. |
| new\_deposit | vesting\_balance\_id\_type | A vesting balance id that will replace the currently set `son` vesting balance. | Optional |
| new\_pay\_vb | vesting\_balance\_id\_type | A vesting balance id that will replace the currently set `normal` vesting balance. | Optional |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
update_son_vesting_balances 1.33.99 1.13.81 1.13.82 true

# The above command is structured like this:
# update_son_vesting_balances <owner_account> <new_deposit> <new_pay_vb> true
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
{
  "ref_block_num": number,
  "ref_block_prefix": number,
  "expiration": "datetime",
  "operations": [],
  "extensions": [],
  "signatures": []
}
```

**Example Successful Return**

```cpp
update_son_vesting_balances 1.33.99 1.13.81 1.13.82 true
{
  "ref_block_num": 10412,
  "ref_block_prefix": 1003801100,
  "expiration": "2015-02-05T00:11:00",
  "operations": [[
      102,{
        "fee": {
          "amount": 0,
          "asset_id": "1.3.0"
        },
        "son_id": "1.33.99",
        "owner_account": "1.2.12345",
        "new_deposit": "1.13.81",
        "new_pay_vb": "1.13.82"
      }
    ]
  ],
  "extensions": [],
  "signatures": [
    "268bd8....."
  ]
}
```
{% endtab %}
{% endtabs %}

### 1.4. get\_son

Returns information about the given SON.

{% code title="return type, namespace, & method" %}
```cpp
son_object graphene::wallet::wallet_api::get_son(
    string owner_account);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| owner\_account | string | The name or id of the SON account owner, or the id of the SON. | No quotes required. |

**Example Call**

```cpp
get_son 1.33.99
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
{
  "id": "string",
  "son_account": "string",
  "vote_id": "string",
  "total_votes": number,
  "url": "string",
  "deposit": "string",
  "signing_key": "string",
  "pay_vb": "string",
  "statistics": "string",
  "status": "string",
  "sidechain_public_keys": [[
      "string",
      "string"
    ]
  ]
}
```

**Example Successful Return**

```cpp
get_son 1.33.99
{
  "id": "1.33.99",
  "son_account": "1.2.12345",
  "vote_id": "3:114",
  "total_votes": 0,
  "url": "www.my-awesome-son.com",
  "deposit": "1.13.79",
  "signing_key": "PPY2j4ySF...",
  "pay_vb": "1.13.80",
  "statistics": "2.25.4",
  "status": "inactive",
  "sidechain_public_keys": [[
      "bitcoin",
      "bc1..."
    ]
  ]
}
```
{% endtab %}
{% endtabs %}

### 1.5. vote\_for\_son

Vote for a given SON. An account can publish a list of all SONs they approve of. This command allows you to add or remove SONs from this list. Each account's vote is weighted according to the number of PPY owned by that account at the time the votes are tallied. Note that you can't vote against a SON, you can only vote for the SON or not vote for the SON.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::vote_for_son(
    string voting_account,
    string son,
    bool approve,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| voting\_account | string | The name or id of the account who is voting with their PPY. | No quotes required. |
| witness | string | The name or id of the SON's owner account. | No quotes required. |
| approve | bool | `true` if you wish to vote in favor of that SON, `false` to remove your vote in favor of that SON. | n/a |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
vote_for_son 1.2.12345 some-son true true
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
{
  "ref_block_num": number,
  "ref_block_prefix": number,
  "expiration": "datetime",
  "operations": [],
  "extensions": [],
  "signatures": []
}
```
{% endtab %}
{% endtabs %}

### 1.6. update\_son\_votes

Change all your votes for SONs in one transaction. This can add and remove votes, and set the number of SONs you think should be active too.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::update_son_votes(
    string voting_account,
    std::vector<std::string> sons_to_approve,
    std::vector<std::string> sons_to_reject,
    uint16_t desired_number_of_sons,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

\*\*\*\*
{% endtab %}

{% tab title="Return" %}
**Return Format**

\*\*\*\*

**Example Successful Return**

\*\*\*\*
{% endtab %}
{% endtabs %}

### 1.7. list\_sons

List all registered SONs, active or not.

{% code title="return type, namespace, & method" %}
```cpp
map<string, son_id_type> graphene::wallet::wallet_api::list_sons(
    const string& lowerbound,
    uint32_t limit);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 1.8. list\_active\_sons

List all active SONs.

{% code title="return type, namespace, & method" %}
```cpp
map<string, son_id_type> graphene::wallet::wallet_api::list_active_sons();
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 1.9. request\_son\_maintenance

This will gracefully put a SON in a temporarily inactive state to allow for server maintenance, or a temporary break from their duties \(like a vacation or personal time\).

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::request_son_maintenance(
    string owner_account,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 1.10. cancel\_request\_son\_maintenance

This will return a SON from maintenance mode when they are ready to resume SON activities.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::cancel_request_son_maintenance(
    string owner_account,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 1.11. get\_son\_wallets

This will display the wallet information for all registered SONs. You can specify the maximum number of wallets to return.

{% code title="return type, namespace, & method" %}
```cpp
vector<optional<son_wallet_object>> graphene::wallet::wallet_api::get_son_wallets(
    uint32_t limit);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 1.12. get\_active\_son\_wallet

This will display the wallet information for the current active SON.

{% code title="return type, namespace, & method" %}
```cpp
optional<son_wallet_object> graphene::wallet::wallet_api::get_active_son_wallet();
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 1.13. get\_son\_wallet\_by\_time\_point

This will display the wallet information for the SON that was active at a specific date and time.

{% code title="return type, namespace, & method" %}
```cpp
optional<son_wallet_object> graphene::wallet::wallet_api::get_son_wallet_by_time_point(
    time_point_sec time_point);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

## 2. Sidechains CLI Command Reference

### 2.1. add\_sidechain\_address

This command allows a user to register two Bitcoin addresses: one used to create their deposit address, and one that will be used for their withdraw address.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::add_sidechain_address(
    string account,
    sidechain_type sidechain,
    string deposit_public_key,
    string withdraw_public_key,
    string withdraw_address,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 2.2. delete\_sidechain\_address

This will delete a sidechain address that was previously registered with the `add_sidechain_address` command. Only one sidechain address can exist per user and sidechain. \(A sidechain address in the case of Bitcoin consists of both a deposit and a withdraw address.\)

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::delete_sidechain_address(
    string account,
    sidechain_type sidechain,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 2.3. get\_sidechain\_address\_by\_account\_and\_sidechain

This returns a registered sidechain address for a given account and sidechain.

{% code title="return type, namespace, & method" %}
```cpp
fc::optional<sidechain_address_object> graphene::wallet::wallet_api::get_sidechain_address_by_account_and_sidechain(
    string account,
    sidechain_type sidechain);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 2.4. get\_sidechain\_addresses\_by\_account

This returns all the registered sidechain addresses for a given account.

{% code title="return type, namespace, & method" %}
```cpp
vector<optional<sidechain_address_object>> graphene::wallet::wallet_api::get_sidechain_addresses_by_account(
    string account);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 2.5. get\_sidechain\_addresses\_by\_sidechain

This returns all the registered sidechain addresses for a given sidechain.

{% code title="return type, namespace, & method" %}
```cpp
vector<optional<sidechain_address_object>> graphene::wallet::wallet_api::get_sidechain_addresses_by_sidechain(
    sidechain_type sidechain);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}

### 2.6. get\_sidechain\_addresses\_count

This returns the number of registered sidechain addresses.

{% code title="return type, namespace, & method" %}
```cpp
uint64_t graphene::wallet::wallet_api::get_sidechain_addresses_count();
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}

{% endtab %}

{% tab title="Return" %}

{% endtab %}
{% endtabs %}



