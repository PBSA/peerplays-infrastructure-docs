---
title: CLI Commands for Witnesses
description: CLI commands that witnesses use.
---

# CLI Commands for Witnesses

## 1. Witnesses CLI Command Reference

### 1.1. create\_witness

Creates a witness object owned by the given account. An account can have at most one witness object.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::create_witness(
    string owner_account, 
    string url,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| owner\_account | string | The name or id of the account which is creating the witness. | no quotes required. |
| url | string | a URL to include in the witness record in the blockchain. Clients may display this when showing a list of witnesses. | May be blank. |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
create_witness myaccountname1 "www.my-awesome-witness.com" true
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

### 1.2. update\_witness

Update a witness object owned by the given account.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::update_witness(
    string witness_name,
    string url,
    string block_signing_key,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| witness\_name | string | The name of the witness's owner account. Also accepts the ID of the owner account or the ID of the witness. | no quotes required. |
| url | string | Same as for create\_witness. An empty string makes it remain the same. | n/a |
| block\_signing\_key | string | The new block signing public key. The empty string makes it remain the same. | n/a |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
update_witness myaccountname-witness "www.my-awesome-witness.com" PPY62L3VYXS8XqorUe2kn63j4TAQpX4YkMJkfFfmmD1nd8Qjo4Bxz true
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
update_witness myaccountname-witness "www.my-awesome-witness.com" PPY62L3VYXS8XqorUe2kn63j4TAQpX4YkMJkfFfmmD1nd8Qjo4Bxz true
{
  "ref_block_num": 10412,
  "ref_block_prefix": 1003801100,
  "expiration": "2015-02-05T00:11:00",
  "operations": [[
      21,{
        "fee": {
          "amount": 50000,
          "asset_id": "1.3.0"
        },
        "witness": "1.6.99",
        "witness_account": "1.2.1234",
        "new_signing_key": "PPY62L3VYXS8XqorUe2kn63j4TAQpX4YkMJkfFfmmD1nd8Qjo4Bxz",
        "new_initial_secret": "5n00bd4aus04a9057c09b19b05f723f2e23deb32"
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

### 1.3. get\_witness

Returns information about the given witness.

{% code title="return type, namespace, & method" %}
```cpp
witness_object graphene::wallet::wallet_api::get_witness(
    string owner_account);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| owner\_account | string | The name or id of the witness account owner, or the id of the witness. | No quotes required. |

**Example Call**

```cpp
get_witness 1.6.99
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
{
  "id": "string",
  "witness_account": "string",
  "last_aslot": number,
  "signing_key": "string",
  "next_secret_hash": "string",
  "previous_secret": "string",
  "vote_id": "string",
  "total_votes": number,
  "url": "string",
  "total_missed": number,
  "last_confirmed_block_num": number
}
```

**Example Successful Return**

```cpp
get_witness 1.6.99
{
  "id": "1.6.99",
  "witness_account": "1.2.1234",
  "last_aslot": 0,
  "signing_key": "PPY62L3VYX...",
  "next_secret_hash": "7197f...",
  "previous_secret": "0000000000000000000000000000000000000000",
  "vote_id": "1:99",
  "total_votes": 0,
  "url": "www.my-awesome-witness.com",
  "total_missed": 0,
  "last_confirmed_block_num": 0
}
```
{% endtab %}
{% endtabs %}

### 1.4. vote\_for\_witness

Vote for a given witness. An account can publish a list of all witnesses they approve of. This command allows you to add or remove witnesses from this list. Each account's vote is weighted according to the number of PPY owned by that account at the time the votes are tallied. Note that you can't vote against a witness, you can only vote for the witness or not vote for the witness.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::vote_for_witness(
    string voting_account,
    string witness,
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
| witness | string | The name or id of the witness' owner account. | No quotes required. |
| approve | bool | `true` if you wish to vote in favor of that witness, `false` to remove your vote in favor of that witness. | n/a |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
vote_for_witness 1.2.12345 some-witness true true
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

