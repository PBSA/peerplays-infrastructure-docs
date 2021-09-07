---
title: CLI Commands for All Nodes
description: CLI commands that all node operators use.
---

# CLI Commands for All Nodes

## 1. Wallet Command Reference for all Node Operators

### 1.1. suggest\_brain\_key

Suggests a safe brain key to use for creating your account keys. A **brain key** is a long passphrase that provides enough entropy to generate cryptographic keys. This function will suggest a suitably random string that should be easy to write down \(and, with effort, memorize\).

{% hint style="info" %}
The GUI Wallet generates a brain key for your password when creating a new account. But in the case of the GUI Wallet, rather than a long passphrase \(i.e. set of words\), it generates a string of 52 random letters \(a-z & A-Z\) and numbers \(0-9\).

For example, the `suggest_brain_key` method could give you:

`"EDIFICE PALLID ANOESIA STRIDE PARREL SPORTY AXIFORM INOPINE SWOONED TONETIC CORKER OATEN PUSHER MIN CERN TACT"`

And the GUI Wallet could produce the password of:

`EyqFQDRpydZJDgTV8EJIcpmPLhfmdq6Yjbo45pNsBe7wSJSpvq0v`

Although they look different, both are brain keys and will work for generating public and private keys.
{% endhint %}

{% code title="return type, namespace, & method" %}
```cpp
brain_key_info graphene::wallet::wallet_api::suggest_brain_key()const;
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| ℹ This command has no parameters! | n/a | n/a | n/a |

**Example Call**

```cpp
suggest_brain_key
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
struct brain_key_info
{
   string brain_priv_key;
   string wif_priv_key;
   public_key_type pub_key;
};
```

**Example Successful Return**

```cpp
suggest_brain_key
{
  "brain_priv_key": "EDIFICE PALLID ANOESIA STRIDE PARREL SPORTY AXIFORM INOPINE SWOONED TONETIC CORKER OATEN PUSHER MIN CERN TACT",
  "wif_priv_key": "5JyK47o1xFmA6fE4Z4Pgzcdd7dGhQnorW2sTPzPxeXH7QaT4eN6",
  "pub_key": "PPY6TK9mXBaFr8ewaJa9zpgbRQVN7VkvAKay4hHGA2hDceDQvrRBv"
}
```

{% hint style="info" %}
Note that the returned "`pub_key`" value will be prefixed with:

* "`PPY`" in mainnet \(Alice\) as per the example above
* "`TEST`" in the public testnet \(Beatrice\)
{% endhint %}
{% endtab %}
{% endtabs %}

### 1.2. get\_private\_key\_from\_password

Returns the public-private key-pair for the `owner`, `active`, or `memo` role for a given account and its password.

{% code title="return type, namespace, & method" %}
```cpp
pair<public_key_type, string> graphene::wallet::wallet_api::get_private_key_from_password(
    string account,
    string role,
    string password
    )const;
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| account | string | The account name we're creating keys for. | no quotes required. |
| role | string | The role we're creating keys for. One of: `owner`, `active`, or `memo` | no quotes required. |
| password | string | A brain key. It might be the password provided by the GUI wallet, or obtained from the `suggest_brain_key`command \(`brain_priv_key`\). | **quotes required if there are spaces!** |

**Example Call**

```cpp
get_private_key_from_password myaccountname1 owner "EDIFICE PALLID ANOESIA STRIDE PARREL SPORTY AXIFORM INOPINE SWOONED TONETIC CORKER OATEN PUSHER MIN CERN TACT"
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
pair<public_key_type, string>
[
  //the public key,
  //the private key
]
```

**Example Successful Return**

```cpp
get_private_key_from_password myaccountname1 owner "EDIFICE PALLID ANOESIA STRIDE PARREL SPORTY AXIFORM INOPINE SWOONED TONETIC CORKER OATEN PUSHER MIN CERN TACT"
[
  "PPY8fAzCBgJhfL6YWsJUycw6SvU7VZnNgq4t5vuzEVP168PHVKJXn",
  "5Kbhg1xaW7hceZhqJSqg3yZ4gsAUdFaA5uD4yBiHME2SYLsa7bU"
]
```
{% endtab %}
{% endtabs %}

### 1.3. import\_key

Imports the private key for an existing account for use in the CLI Wallet. The private key must match either an owner key or an active key for the named account.

{% code title="return type, namespace, & method" %}
```cpp
bool graphene::wallet::wallet_api::import_key(
    string account_name_or_id, 
    string wif_key);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| account\_name\_or\_id | string | The name or id of the account that owns the key. | no quotes required. |
| wif\_key | string | The private key. | no quotes required. |

**Example Call**

```cpp
import_key myaccountname1 5Kbhg1xaW7hceZhqJSqg3yZ4gsAUdFaA5uD4yBiHME2SYLsa7bU
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
true
#if successful

false
#if an error
```

**Example Successful Return**

```cpp
import_key myaccountname1 5Kbhg1xaW7hceZhqJSqg3yZ4gsAUdFaA5uD4yBiHME2SYLsa7bU
true
```
{% endtab %}
{% endtabs %}

### 1.4. upgrade\_account

Upgrades an account to prime status. This makes the account holder a 'lifetime member'. This is necessary for the account to become a Witness or SON.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::upgrade_account(
    string name, 
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| name | string | The name or id of the account to upgrade. | no quotes required. |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

{% hint style="info" %}
Note that this operation currently costs 5 PPY. That fee may change in the future.
{% endhint %}

**Example Call**

```cpp
upgrade_account myaccountname1 true
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
upgrade_account myaccountname1 true
{
  "ref_block_num": 10412,
  "ref_block_prefix": 1003801100,
  "expiration": "2015-02-05T00:11:00",
  "operations": [[
      8,{
        "fee": {
          "amount": 500000,
          "asset_id": "1.3.0"
        },
        "account_to_upgrade": "1.2.1234",
        "upgrade_to_lifetime_member": true,
        "extensions": []
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

### 1.5. create\_vesting\_balance

Creates a vesting deposit owned by the given account. This is used to supply vested assets to operate certain nodes, such as a SON node. In the case of SONs, 100 PPY \(at the time of writing\) must be set aside in two separate vesting deposits \(50 PPY each\) to dedicate to the operation of the SON node transactions.

{% code title="return type, namespace, & method" %}
```cpp
signed_transaction graphene::wallet::wallet_api::create_vesting_balance(
    string owner_account, 
    string amount,
    string asset_symbol,
    vesting_balance_type vesting_type,
    bool broadcast);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| owner\_account | string | The name or id of the vesting balance owner and creator. | no quotes required. |
| amount | string | The amount to vest. | In nominal units. For example, enter 0.5 for half of a PPY. |
| asset\_symbol | string | The symbol of the asset to vest. | no quotes required. |
| vesting\_type | vesting\_balance\_type | One of `normal`, `gpos`, or `son`. | no quotes required. |
| broadcast | bool | `true` to broadcast the transaction on the network. | n/a |

**Example Call**

```cpp
create_vesting_balance myaccountname-son 50 PPY son true
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

### 1.6. get\_private\_key

Get the WIF private key corresponding to a public key. The private key must already be in the wallet.

{% code title="return type, namespace, & method" %}
```cpp
string graphene::wallet::wallet_api::get_private_key(
    public_key_type pubkey);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| pubkey | public\_key\_type | The public key you wish to get the private key for. | no quotes required. |

**Example Call**

```cpp
get_private_key PPY8kvUXLpoXE9rJHwppR48LkqSouAzFZomAPb3hW9gnkSHZCsozi
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
string
```

**Example Successful Return**

```cpp
get_private_key PPY5D13DULV2gKLQ324bmyTVFGW2wm9QYz6uVEmnUmkifRyxcSifk
"5JCLzEW4T4ALFKZeKef7qF8T2MhndmtZoj2sbcXhNTVZWen2Xn1"
```
{% endtab %}
{% endtabs %}

### 1.7. dump\_private\_keys

Displays all private keys owned by the wallet. The keys are printed in WIF format. You can import these keys into another wallet using `import_key()`.

{% code title="return type, namespace, & method" %}
```cpp
map<public_key_type, string> graphene::wallet::wallet_api::dump_private_keys();
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| ℹ This command has no parameters! | n/a | n/a | n/a |

**Example Call**

```cpp
dump_private_keys
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
[[
    "Public Key String 1",
    "Private Key String 1"
  ],[
    "Public Key String 2",
    "Private Key String 2"
  ],[
    "Public Key String 3",
    "Private Key String 3"
  ],[
    "Public Key String n...",
    "Private Key String n..."
  ]
]
```

**Example Successful Return**

```cpp
dump_private_keys
[[
    "PPY7sxBKU1...",
    "5Jh04o8q..."
  ],[
    "PPY6oAqs9...",
    "5JMR32LV..."
  ],[
    "PPY586S...",
    "5JN59ePX..."
  ],[
    And so on...
  ]
]
```
{% endtab %}
{% endtabs %}

### 1.8. get\_account

Returns information about the given account.

{% code title="return type, namespace, & method" %}
```cpp
account_object graphene::wallet::wallet_api::get_account(
    string account_name_or_id);
```
{% endcode %}

{% tabs %}
{% tab title="Function Call" %}
**Parameters**

| name | data type | description | details |
| :--- | :--- | :--- | :--- |
| account\_name\_or\_id | string | The name or id of the account to provide information about. | No quotes required. |

**Example Call**

```cpp
get_account 1.2.12345
```
{% endtab %}

{% tab title="Return" %}
**Return Format**

```cpp
{
  "id": "string",
  "membership_expiration_date": "datetime",
  "registrar": "string",
  "referrer": "string",
  "lifetime_referrer": "string",
  "network_fee_percentage": number,
  "lifetime_referrer_fee_percentage": number,
  "referrer_rewards_percentage": number,
  "name": "string",
  "owner": {
    "weight_threshold": number,
    "account_auths": [],
    "key_auths": [],
    "address_auths": []
  },
  "active": {
    "weight_threshold": number,
    "account_auths": [],
    "key_auths": [],
    "address_auths": []
  },
  "options": {
    "memo_key": "string",
    "voting_account": "string",
    "num_witness": number,
    "num_committee": number,
    "votes": [],
    "extensions": []
  },
  "statistics": "string",
  "whitelisting_accounts": [],
  "blacklisting_accounts": [],
  "whitelisted_accounts": [],
  "blacklisted_accounts": [],
  "owner_special_authority": [],
  "active_special_authority": [],
  "top_n_control_flags": number
}
```

**Example Successful Return**

```cpp
get_account 1.2.12345
{
  "id": "1.2.12345",
  "membership_expiration_date": "2106-02-07T06:28:15",
  "registrar": "1.2.12345",
  "referrer": "1.2.12345",
  "lifetime_referrer": "1.2.12345",
  "network_fee_percentage": 10000,
  "lifetime_referrer_fee_percentage": 0,
  "referrer_rewards_percentage": 0,
  "name": "myawesome-son",
  "owner": {
    "weight_threshold": 1,
    "account_auths": [],
    "key_auths": [[
        "PPY8XGp5...",
        1
      ]
    ],
    "address_auths": []
  },
  "active": {
    "weight_threshold": 1,
    "account_auths": [],
    "key_auths": [[
        "PPY2j4ySFX...",
        1
      ]
    ],
    "address_auths": []
  },
  "options": {
    "memo_key": "PPY15sZ1A...",
    "voting_account": "1.2.8",
    "num_witness": 0,
    "num_committee": 0,
    "votes": [],
    "extensions": []
  },
  "statistics": "2.6.12345",
  "whitelisting_accounts": [],
  "blacklisting_accounts": [],
  "whitelisted_accounts": [],
  "blacklisted_accounts": [],
  "owner_special_authority": [
    0,{}
  ],
  "active_special_authority": [
    0,{}
  ],
  "top_n_control_flags": 0
}
```
{% endtab %}
{% endtabs %}

