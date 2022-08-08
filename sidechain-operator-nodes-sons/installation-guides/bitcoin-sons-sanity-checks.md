---
description: CLI checks to ensure the successful installation of Bitcoin-SON node.
---

# Bitcoin-SONs Sanity Checks

After installing a Bitcoin SON node, you might want to run some basic tests to ensure everything is running smoothly with your Bitcoin node. Here are a few bitcoin-cli commands that you can run to check your node's functionality.

You can use these commands to get an overview of the Bitcoin network, how your node connects to the network, and your configured wallet and address settings.

## 1. bitcoin-cli help

List all commands, or get help for a specified command.

```
bitcoin-cli help "command"

# For example...
# bitcoin-cli help "getblockchaininfo"
```

The `"command"` in the above code block can be one of any bitcoin-cli commands listed in the reference doc. It's also optional and if left out will list all available commands. The help command is a good place to start to ensure the bitcoin-cli is actually available on your system.

## 2. getblockchaininfo

Returns an object containing various state info regarding blockchain processing.

```
bitcoin-cli getblockchaininfo
```

This command doesn't have any parameters. Running this command will list a lot of important information about the chain and your node. This is the command to use to see how much of the chain your node has validated and that you are connected to **Bitcoin's mainnet**. Here's what is returned in the call:

```javascript
{                                         (json object)
  "chain" : "str",                        (string) current network name (main, test, regtest)
  "blocks" : n,                           (numeric) the height of the most-work fully-validated chain. The genesis block has height 0
  "headers" : n,                          (numeric) the current number of headers we have validated
  "bestblockhash" : "str",                (string) the hash of the currently best block
  "difficulty" : n,                       (numeric) the current difficulty
  "mediantime" : n,                       (numeric) median time for the current best block
  "verificationprogress" : n,             (numeric) estimate of verification progress [0..1]
  "initialblockdownload" : true|false,    (boolean) (debug information) estimate of whether this node is in Initial Block Download mode
  "chainwork" : "hex",                    (string) total amount of work in active chain, in hexadecimal
  "size_on_disk" : n,                     (numeric) the estimated size of the block and undo files on disk
  "pruned" : true|false,                  (boolean) if the blocks are subject to pruning
  "pruneheight" : n,                      (numeric) lowest-height complete block stored (only present if pruning is enabled)
  "automatic_pruning" : true|false,       (boolean) whether automatic pruning is enabled (only present if pruning is enabled)
  "prune_target_size" : n,                (numeric) the target size used by pruning (only present if automatic pruning is enabled)
  "softforks" : {                         (json object) status of softforks
    "xxxx" : {                            (json object) name of the softfork
      "type" : "str",                     (string) one of "buried", "bip9"
      "bip9" : {                          (json object) status of bip9 softforks (only for "bip9" type)
        "status" : "str",                 (string) one of "defined", "started", "locked_in", "active", "failed"
        "bit" : n,                        (numeric) the bit (0-28) in the block version field used to signal this softfork (only for "started" status)
        "start_time" : xxx,               (numeric) the minimum median time past of a block at which the bit gains its meaning
        "timeout" : xxx,                  (numeric) the median time past of a block at which the deployment is considered failed if not yet locked in
        "since" : n,                      (numeric) height of the first block to which the status applies
        "statistics" : {                  (json object) numeric statistics about BIP9 signalling for a softfork (only for "started" status)
          "period" : n,                   (numeric) the length in blocks of the BIP9 signalling period
          "threshold" : n,                (numeric) the number of blocks with the version bit set required to activate the feature
          "elapsed" : n,                  (numeric) the number of blocks elapsed since the beginning of the current period
          "count" : n,                    (numeric) the number of blocks with the version bit set in the current period
          "possible" : true|false         (boolean) returns false if there are not enough blocks left in this period to pass activation threshold
        }
      },
      "height" : n,                       (numeric) height of the first block which the rules are or will be enforced (only for "buried" type, or "bip9" type with "active" status)
      "active" : true|false               (boolean) true if the rules are enforced for the mempool and the next block
    },
    ...
  },
  "warnings" : "str"                      (string) any network and blockchain warnings
}
```

## 3. getmempoolinfo

Returns details on the active state of the TX memory pool.

{% hint style="info" %}
The TX memory pool, or "mempool", is the pool of unverified transactions that don't yet belong to a block in the chain. These transactions are basically waiting for miners to verify and include them in blocks to make them official.
{% endhint %}

```javascript
bitcoin-cli getmempoolinfo
```

This command is useful to view the network backlog of transactions. Here's what is returned:

```javascript
{                            (json object)
  "loaded" : true|false,     (boolean) True if the mempool is fully loaded
  "size" : n,                (numeric) Current tx count
  "bytes" : n,               (numeric) Sum of all virtual transaction sizes as defined in BIP 141. Differs from actual serialized size because witness data is discounted
  "usage" : n,               (numeric) Total memory usage for the mempool
  "maxmempool" : n,          (numeric) Maximum memory usage for the mempool
  "mempoolminfee" : n,       (numeric) Minimum fee rate in BTC/kB for tx to be accepted. Is the maximum of minrelaytxfee and minimum mempool fee
  "minrelaytxfee" : n,       (numeric) Current minimum relay fee for transactions
  "unbroadcastcount" : n     (numeric) Current number of transactions that haven't passed initial broadcast yet
}
```

## 4. getnetworkinfo

Returns an object containing various state info regarding P2P networking.

```
bitcoin-cli getnetworkinfo
```

This command is important for understanding the network connections of your node. Here is what is returned in this call:

```javascript
{                                                    (json object)
  "version" : n,                                     (numeric) the server version
  "subversion" : "str",                              (string) the server subversion string
  "protocolversion" : n,                             (numeric) the protocol version
  "localservices" : "hex",                           (string) the services we offer to the network
  "localservicesnames" : [                           (json array) the services we offer to the network, in human-readable form
    "str",                                           (string) the service name
    ...
  ],
  "localrelay" : true|false,                         (boolean) true if transaction relay is requested from peers
  "timeoffset" : n,                                  (numeric) the time offset
  "connections" : n,                                 (numeric) the total number of connections
  "connections_in" : n,                              (numeric) the number of inbound connections
  "connections_out" : n,                             (numeric) the number of outbound connections
  "networkactive" : true|false,                      (boolean) whether p2p networking is enabled
  "networks" : [                                     (json array) information per network
    {                                                (json object)
      "name" : "str",                                (string) network (ipv4, ipv6 or onion)
      "limited" : true|false,                        (boolean) is the network limited using -onlynet?
      "reachable" : true|false,                      (boolean) is the network reachable?
      "proxy" : "str",                               (string) ("host:port") the proxy that is used for this network, or empty if none
      "proxy_randomize_credentials" : true|false     (boolean) Whether randomized credentials are used
    },
    ...
  ],
  "relayfee" : n,                                    (numeric) minimum relay fee for transactions in BTC/kB
  "incrementalfee" : n,                              (numeric) minimum fee increment for mempool limiting or BIP 125 replacement in BTC/kB
  "localaddresses" : [                               (json array) list of local addresses
    {                                                (json object)
      "address" : "str",                             (string) network address
      "port" : n,                                    (numeric) network port
      "score" : n                                    (numeric) relative score
    },
    ...
  ],
  "warnings" : "str"                                 (string) any network and blockchain warnings
}

Examp
```

## 5. getwalletinfo

Returns an object containing various wallet state info.

```
bitcoin-cli getwalletinfo
```

This shows the configuration of any wallets belonging to your node. In our case this will show us the "son-wallet" we should have set up. Here's what's returned:

```javascript
{                                         (json object)
  "walletname" : "str",                   (string) the wallet name
  "walletversion" : n,                    (numeric) the wallet version
  "format" : "str",                       (string) the database format (bdb or sqlite)
  "balance" : n,                          (numeric) DEPRECATED. Identical to getbalances().mine.trusted
  "unconfirmed_balance" : n,              (numeric) DEPRECATED. Identical to getbalances().mine.untrusted_pending
  "immature_balance" : n,                 (numeric) DEPRECATED. Identical to getbalances().mine.immature
  "txcount" : n,                          (numeric) the total number of transactions in the wallet
  "keypoololdest" : xxx,                  (numeric) the UNIX epoch time of the oldest pre-generated key in the key pool. Legacy wallets only.
  "keypoolsize" : n,                      (numeric) how many new keys are pre-generated (only counts external keys)
  "keypoolsize_hd_internal" : n,          (numeric) how many new keys are pre-generated for internal use (used for change outputs, only appears if the wallet is using this feature, otherwise external keys are used)
  "unlocked_until" : xxx,                 (numeric, optional) the UNIX epoch time until which the wallet is unlocked for transfers, or 0 if the wallet is locked (only present for passphrase-encrypted wallets)
  "paytxfee" : n,                         (numeric) the transaction fee configuration, set in BTC/kvB
  "hdseedid" : "hex",                     (string, optional) the Hash160 of the HD seed (only present when HD is enabled)
  "private_keys_enabled" : true|false,    (boolean) false if privatekeys are disabled for this wallet (enforced watch-only wallet)
  "avoid_reuse" : true|false,             (boolean) whether this wallet tracks clean/dirty coins in terms of reuse
  "scanning" : {                          (json object) current scanning details, or false if no scan is in progress
    "duration" : n,                       (numeric) elapsed seconds since scan start
    "progress" : n                        (numeric) scanning progress percentage [0.0, 1.0]
  },
  "descriptors" : true|false              (boolean) whether this wallet uses descriptors for scriptPubKey management
}
```

## 6. getaddressinfo

Return information about the given bitcoin address.

{% hint style="info" %}
Some of the information will only be present if the address is in the active wallet.
{% endhint %}

```
bitcoin-cli getaddressinfo "address"

# For example...
# bitcoin-cli getaddressinfo "bc1q09vm5lfy0j5reeulh4x5752q25uqqvz34hufdl"
```

This is how you can view the pubkey for your Bitcoin addresses. Much more than that is returned:

```javascript
{                                   (json object)
  "address" : "str",                (string) The bitcoin address validated.
  "scriptPubKey" : "hex",           (string) The hex-encoded scriptPubKey generated by the address.
  "ismine" : true|false,            (boolean) If the address is yours.
  "iswatchonly" : true|false,       (boolean) If the address is watchonly.
  "solvable" : true|false,          (boolean) If we know how to spend coins sent to this address, ignoring the possible lack of private keys.
  "desc" : "str",                   (string, optional) A descriptor for spending coins sent to this address (only when solvable).
  "isscript" : true|false,          (boolean) If the key is a script.
  "ischange" : true|false,          (boolean) If the address was used for change output.
  "iswitness" : true|false,         (boolean) If the address is a witness address.
  "witness_version" : n,            (numeric, optional) The version number of the witness program.
  "witness_program" : "hex",        (string, optional) The hex value of the witness program.
  "script" : "str",                 (string, optional) The output script type. Only if isscript is true and the redeemscript is known. Possible
                                    types: nonstandard, pubkey, pubkeyhash, scripthash, multisig, nulldata, witness_v0_keyhash,
                                    witness_v0_scripthash, witness_unknown.
  "hex" : "hex",                    (string, optional) The redeemscript for the p2sh address.
  "pubkeys" : [                     (json array, optional) Array of pubkeys associated with the known redeemscript (only if script is multisig).
    "str",                          (string)
    ...
  ],
  "sigsrequired" : n,               (numeric, optional) The number of signatures required to spend multisig output (only if script is multisig).
  "pubkey" : "hex",                 (string, optional) The hex value of the raw public key for single-key addresses (possibly embedded in P2SH or P2WSH).
  "embedded" : {                    (json object, optional) Information about the address embedded in P2SH or P2WSH, if relevant and known.
    ...                             Includes all getaddressinfo output fields for the embedded address, excluding metadata (timestamp, hdkeypath, hdseedid)
                                    and relation to the wallet (ismine, iswatchonly).
  },
  "iscompressed" : true|false,      (boolean, optional) If the pubkey is compressed.
  "timestamp" : xxx,                (numeric, optional) The creation time of the key, if available, expressed in UNIX epoch time.
  "hdkeypath" : "str",              (string, optional) The HD keypath, if the key is HD and available.
  "hdseedid" : "hex",               (string, optional) The Hash160 of the HD seed.
  "hdmasterfingerprint" : "hex",    (string, optional) The fingerprint of the master key.
  "labels" : [                      (json array) Array of labels associated with the address. Currently limited to one label but returned
                                    as an array to keep the API stable if multiple labels are enabled in the future.
    "str",                          (string) Label name (defaults to "").
    ...
  ]
}
```

## 7. References

{% embed url="https://developer.bitcoin.org/reference/rpc/index.html" %}
