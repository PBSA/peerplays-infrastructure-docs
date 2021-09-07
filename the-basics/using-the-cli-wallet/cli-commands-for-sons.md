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
| block\_signing\_key | string | vesting balance id for SON deposit. | This is the `son` vesting balance. |
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



### 1.7. list\_sons



### 1.8. list\_active\_sons



### 1.9. request\_son\_maintenance



### 1.10. cancel\_request\_son\_maintenance



### 1.11. get\_son\_wallets



### 1.12. get\_active\_son\_wallet



### 1.13. get\_son\_wallet\_by\_time\_point



## 2. Sidechains CLI Command Reference

### 2.1. add\_sidechain\_address



### 2.2. delete\_sidechain\_address



### 2.3. get\_sidechain\_address\_by\_account\_and\_sidechain



### 2.4. get\_sidechain\_addresses\_by\_account



### 2.5. get\_sidechain\_addresses\_by\_sidechain



### 2.6. get\_sidechain\_addresses\_count





