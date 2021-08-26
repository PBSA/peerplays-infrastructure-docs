---
title: Private Testnets
description: How to set up a Peerplays private testnet.
---

# Private Testnets

## 1. Overview

Creating your own private Peerplays testnet has many perks. You can develop new dapps, troubleshoot network issues, or experiment on some new ideas on a chain that you can customize and fully control.

## 2. Requirements

### 2.1. Hardware Requirements

Installing a private testnet requires building the Peerplays program from source code. This is because the initial state of the blockchain is embedded into the binaries. Since we need to begin a new chain from the very first block, we'll need to build from source and \(if desired\) embed your own custom genesis file into the built binaries.

Building the Peerplays chain from source code is memory intensive. This means the initial hardware requirements are quite high. But after all the building is done, the requirements to run the private chain are much lower.

Here's a guideline for the hardware requirements for the running of the private testnet:

| Node Type? | CPU | Memory | Storage | Bandwidth | OS |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Witness | 4 Cores | 16GB ⚠  | 100GB SSD | 1Gbps | Ubuntu 18.04 |

{% hint style="danger" %}
The memory requirements shown in the table above are adequate to operate the node. Building and installing the node from source code \(as with this guide\) will require more memory. You may run into errors during the build and install process if the system memory is too low. See [Installing vs Operating](../the-basics/hardware-requirements.md#4-2-installing-vs-operating) for more details.
{% endhint %}

Of course, the requirements will be highly dependent on what you're using the testnet for. Intensive development of an enterprise-level application will need much more resources than simply exploring your own private testnet.

### 2.2. Install Required Software

The following packages are required for this guide.

```text
sudo apt-get update
sudo apt-get -y install autoconf bash build-essential ca-certificates cmake \
      vim dnsutils doxygen git graphviz libbz2-dev libcurl4-openssl-dev \
      libncurses-dev libreadline-dev libssl-dev libtool libzmq3-dev \
      locales ntp pkg-config wget autotools-dev libicu-dev python-dev
```

## 3. Install Boost

Boost is a comprehensive C++ library for common development tasks. It's required for Peerplays.

```text
mkdir $HOME/src
cd $HOME/src
export BOOST_ROOT=$HOME/src/boost_1_67_0
sudo apt-get update
sudo apt-get install -y autotools-dev build-essential  libbz2-dev libicu-dev python-dev
wget -c 'http://sourceforge.net/projects/boost/files/boost/1.67.0/boost_1_67_0.tar.bz2/download'\
     -O boost_1_67_0.tar.bz2
tar xjf boost_1_67_0.tar.bz2
cd boost_1_67_0/
./bootstrap.sh "--prefix=$BOOST_ROOT"
./b2 install
```

## 4. Install Peerplays

This is very similar to installing a Witness node. The key difference here is the `-DGRAPHENE_EGENESIS_JSON=""` setting. This means we're choosing to **not** embed a genesis file \(yet\). It's an override because the install will embed a default genesis file unless we tell it not to.

Since we want to potentially customize the genesis file, we'll choose not to embed one now.

```text
cd $HOME/src
export BOOST_ROOT=$HOME/src/boost_1_67_0
git clone https://github.com/peerplays-network/peerplays.git
cd peerplays
git checkout develop
git submodule update --init --recursive
git submodule sync --recursive
cmake -DBOOST_ROOT="$BOOST_ROOT" -DGRAPHENE_EGENESIS_JSON="" -DCMAKE_BUILD_TYPE=Release .
```

Before entering this next command, now would be the time to move on to section 4.1. if you wish to customize hard-coded parts of the chain. Otherwise, proceed.

```text
# the following command will install the executable files under /usr/local/bin
sudo make install
```

### 4.1. \(optional\) Customization to the Hard-Coded Chain Config

Before we install Peerplays with the `sudo make install` command, we have the opportunity to make some customization to the hard-coded configuration. This config is located in this file:

`$HOME/src/peerplays/libraries/chain/include/graphene/chain/config.hpp`

If you edit this file, with `sudo vim $HOME/src/peerplays/libraries/chain/include/graphene/chain/config.hpp`, for example, you can tweak dozens of settings. Things like:

* The symbol of the core token. \(`PPY` in Production, `TEST` in Develop\)
* The prefix of the account addresses. \(`PPY` in Production, `TEST` in Develop\)
* Min / Max lengths of strings.
* Various time limits.
* Token precision and decimal places.
* Many fee related settings.
* Block and transaction sizes.

These may or may not be useful for you to change given that we're making a testnet designed to roughly mimic the conditions in production, but they are interesting nonetheless. If you make any changes here, save the file and then move on to `sudo make install` from the `$HOME/src/peerplays` directory.

## 5. Generate a Genesis \(and Config\) File

We're going to create a directory to store the custom genesis file. And then use the `witness_node` program to generate a genesis template in the directory.

```text
cd $HOME
mkdir genesis
witness_node --create-genesis-json $HOME/genesis/my-genesis.json
```

This will also create the `witness_node_data_dir` directory that stores the `config.ini` file we'll need later.

### 5.1. \(optional\) Edit the Genesis File

The generated genesis file is good enough on its own to run the testnet. But you can edit the `my-genesis.json` file if you wish. In this file, you can specify accounts that exist from the beginning of the chain as well as their account balance. You can add assets, change the fees for operations, add witness accounts, add committee member accounts, and change some initial parameters.

To create the keys to use for accounts in the genesis file, the `get_dev_keys` program is used.

```text
# To use this:
# get_dev_key <any string as a prefix> <any number of strings for suffixes>
get_dev_key test-account -owner -active
```

So the keys will be generated using the strings you put into the program. In the example above, the `get_dev_key` program will use `test-account-owner` and then `test-account-active` to generate a public/private key pair and address, each.

The strings don't really mean anything other than just being used as seeds to create keys and accounts. The strings are not used for account names.

```text
[{"private_key":"5HstR6A92Fzi7kytK4UNGHfEqZay7Ts8GqmcqmJd1yF3JQBMQiw","public_key":"TEST6wfsgrebVdeE5LDVp93YuM6R25P9Fs655sG6GerV1WrhPPh8QA","address":"TESTGVMr1Uf5Kz1LWmaftYHXSuUUHh8Ss6eDo"},
{"private_key":"5K4JoUCHmgMXLWQQw3KcHz6re57RN6zES3VCjvwDkLJ9sANPFS9","public_key":"TEST72wPj38PrFVPeuFDWppE1fDSthhYWA2FsLd7pWk7oCpZuGT2QA","address":"TEST3nEXzhPaF5jkCwGiZo5Uh4Lx1YLmMnDzq"}]
```

Then using the output, you can make new accounts in the `my-genesis.json` file like the following:

`sudo vim $HOME/genesis/my-genesis.json`

```text
...
"initial_accounts": [{
      More accounts here...
    },{
      "name": "account-test",
      "owner_key": "TEST6wfsgrebVdeE5LDVp93YuM6R25P9Fs655sG6GerV1WrhPPh8QA",
      "active_key": "TEST72wPj38PrFVPeuFDWppE1fDSthhYWA2FsLd7pWk7oCpZuGT2QA",
      "is_lifetime_member": true
    }],
  "initial_assets": [],
  "initial_balances": [{
      More balances here...
    },{
      "owner": "TEST3nEXzhPaF5jkCwGiZo5Uh4Lx1YLmMnDzq",
      "asset_symbol": "TEST",
      "amount": "500000000000000"
    }
  ],
...
```

The most important part is to use the generated keys given to the initial witnesses for any witnesses you use. These keys are hard-coded into the program and are used for the initial block producing accounts. The public/private key pair the witnesses use will also be generated in the `config.ini` file as the `private-key`. It's best to leave these alone.

And of course, you should have an account with an initial balance so we can import the balance to the `cli_wallet` later.

### 5.2. \(optional\) Reinstall Peerplays to Embed the Genesis File

While this step is not required, there are a couple of benefits to embedding the genesis file into the Peerplays build. First, you won't have to supply the genesis file location in the `config.ini` file. And second, you won't have to supply the chain id to the `cli_wallet` program when interacting with the testnet.

But the cost of embedding the genesis file is that it takes a lot of time and computer resources to rebuild Peerplays. And with the genesis "baked into" the binaries, it won't be available for future customization if you wanted to make some changes and reset the chain back to block 1.

Ultimately the decision to embed the file is a matter of taste; Is it more important to have the convenience of doing so, or the flexibility of not?

In the set of commands below, we're setting the `-DGRAPHENE_EGENESIS_JSON="$HOME/genesis/my-genesis.json"` option in the make cache to use this file in the build process.

```text
cd $HOME/src/peerplays
export BOOST_ROOT=$HOME/src/boost_1_67_0
cmake -DBOOST_ROOT="$BOOST_ROOT" -DGRAPHENE_EGENESIS_JSON="$HOME/genesis/my-genesis.json" -DCMAKE_BUILD_TYPE=Release .

sudo make install
```

## 6. Edit the Config File

We need to make a few edits to the `config.ini` file to finish setting up the testnet.

`sudo vim $HOME/witness_node_data_dir/config.ini`

Here are the settings we need to add / change:

```text
# Opening the p2p endpoint will make this server into a seed node.
p2p-endpoint = 127.0.0.1:11101

# Opening the rpc endpoint will make this server into an API node.
rpc-endpoint = 127.0.0.1:11201

# Reminder:
# an IP of 127.0.0.1 will only allow connections from this machine (Localhost).
# an IP of 0.0.0.0 will allow connections from ANY machine, even from the internet.

# If you have NOT embedded the genesis file, you'll need this. Otherwise, leave it out.
genesis-json = $HOME/genesis/my-genesis.json

# Stale production is needed to produce blocks on a chain with no blocks.
enable-stale-production = true

# These are the ids of the init0 to init10 witness accounts.
witness-id = "1.6.1"
witness-id = "1.6.2"
witness-id = "1.6.3"
witness-id = "1.6.4"
witness-id = "1.6.5"
witness-id = "1.6.6"
witness-id = "1.6.7"
witness-id = "1.6.8"
witness-id = "1.6.9"
witness-id = "1.6.10"
witness-id = "1.6.11"
```

Everything else should be left as-is.

## 7. Run the Witness\_Node

We'll need to tell the program not to use any seed nodes with `--seed-nodes="[]"`. If we don't do this, the program will attempt to use some hard-coded default seed nodes which don't have anything to do with our private testnet.

```text
cd $HOME
witness_node --seed-nodes="[]"
```

If the program runs successfully you'll see your own unique chain id. The chain id is a hash of the genesis file you're using when running your testnet. If you change anything in your genesis file, the chain id will be different! This is used by the `cli_wallet` program to prevent unintended transactions from happening on the wrong chain.

```text
********************************
*                              *
*   ------- NEW CHAIN ------   *
*   - Welcome to Graphene! -   *
*   ------------------------   *
*                              *
********************************

3501235ms th_a main.cpp:165 main] Started witness node on a chain with 0 blocks.
3501235ms th_a main.cpp:166 main] Chain ID is < -- your own unique chain id will be here -- >
```

Take note of this chain id and keep it for your future reference. If you have not embedded the genesis file, you will need the chain id when running the `cli_wallet` program.

After that, your testnet should be producing blocks!

```text
2324613ms th_a  witness.cpp:185  block_production_loo ] Generated block #1 with timestamp 2016-01-21T22:38:40 at time 2016-01-21T22:38:40
2325604ms th_a  witness.cpp:194  block_production_loo ] Not producing block because slot has not yet arrived
2342604ms th_a  witness.cpp:194  block_production_loo ] Not producing block because slot has not yet arrived
2343609ms th_a  witness.cpp:194  block_production_loo ] Not producing block because slot has not yet arrived
2344604ms th_a  witness.cpp:185  block_production_loo ] Generated block #2 with timestamp 2016-01-21T22:39:00 at time 2016-01-21T22:39:00
2345605ms th_a  witness.cpp:194  block_production_loo ] Not producing block because slot has not yet arrived
2349616ms th_a  witness.cpp:185  block_production_loo ] Generated block #3 with timestamp 2016-01-21T22:39:05 at time 2016-01-21T22:39:05
2350602ms th_a  witness.cpp:194  block_production_loo ] Not producing block because slot has not yet arrived
2353612ms th_a  witness.cpp:194  block_production_loo ] Not producing block because slot has not yet arrived
2354605ms th_a  witness.cpp:185  block_production_loo ] Generated block #4 with timestamp 2016-01-21T22:39:10 at time 2016-01-21T22:39:10
```

## 8. Running the CLI Wallet

We are now ready to connect the CLI to your testnet witness node. Keep your witness node running and in another terminal window you'll run the CLI Wallet.

If you have not embedded the genesis file, you'll need the chain id to run this command:

```text
cd $HOME
cli_wallet --wallet-file=my-wallet.json --chain-id 8b7bd36a146a03d0e5d0a971e286098f41230b209d96f92465cd62bd64294824 --server-rpc-endpoint=ws://127.0.0.1:11201
```

Just be sure to replace the example `8b7bd36a146a03d0e5d0a971e286098f41230b209d96f92465cd62bd64294824` with your own chain id from earlier.

Or if you embedded the genesis file, there's no need for the chain id in this command:

```text
cd $HOME
cli_wallet --wallet-file=my-wallet.json --server-rpc-endpoint=ws://127.0.0.1:11201
```

If you see the `new >>>` prompt, you have successfully connected to your node and you're ready to create a password with `set_password`.

```text
new >>> set_password yoursupersecretpassword
```

Now you can unlock the newly created wallet:

```text
unlock yoursupersecretpassword
```

### 8.1. Get Access to Genesis Funds

In Peerplays, balances are contained in accounts. To import an account into your wallet, all you need to know its name and its private key. We will now import into the wallet an account called `nathan` using the `import_key` command:

```text
import_key nathan 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
```

{% hint style="info" %}
Note that `nathan` happens to be the account name defined in the genesis file. If you had edited your `my-genesis.json` file just after it was created, you could have put a different name there. Also, note that `5KQwrPbwdL...P79zkvFD3` is the private key defined in the `config.ini` file.
{% endhint %}

Now we have the private key imported into the wallet but still no funds associated with it. Funds are stored in genesis balance objects. These funds can be claimed, with no fee, using the `import_balance` command:

```text
import_balance nathan ["5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"] true
```

As a result, we have one account \(named `nathan`\) imported into the wallet and this account is well funded with `TEST` as we have claimed the funds stored in the genesis file. You can view this account by using this command:

```text
get_account nathan
```

...and its balance by using this command:

```text
list_account_balances nathan
```

### 8.2. Create Another Account

We will now create another account \(named `alpha`\) so that we can transfer funds back and forth between `nathan` and `alpha`.

Creating a new account is always done by using an existing account. We need it because someone \(the registrar\) has to fund the registration fee. Also, there is the requirement for the registrar account to have a lifetime member \(LTM\) status. Therefore we need to upgrade the account `nathan` to LTM, before we can proceed with creating other accounts. To upgrade to LTM, use the `upgrade_account` command:

```text
upgrade_account nathan true
```

Verify that `nathan` has now a LTM status:

```text
get_account nathan
```

In the response, next to `membership_expiration_date` you should see something similar to `2106-02-07T06:28:15`. If you get `1970-01-01T00:00:00` something is wrong and `nathan` has not been successfully upgraded.

We can now register an account by using `nathan` as registrar. But first we need to get hold of the public key for the new account. We do it by using the `suggest_brain_key` command:

```text
suggest_brain_key
```

And the response should be something similar to this:

```text
suggest_brain_key
{
  "brain_priv_key": "MYAL SOEVER UNSHARP PHYSIC JOURNEY SHEUGH BEDLAM WLOKA FOOLERY GUAYABA DENTILE RADIATE TIEPIN ARMS FOGYISH COQUET",
  "wif_priv_key": "5JDh3XmHK8CDaQSxQZHh5PUV3zwzG68uVcrTfmg9yQ9idNisYnE",
  "pub_key": "TEST78CuY47Vds2nfw2t88ckjTaggPkw16tLhcmg4ReVx1WPr1zRL5"
}
```

So in this example:

* the public key is `TEST78CuY47V...WPr1zRL5`
* the private key is `5JDh3XmH...9idNisYnE`
* and let's assume our new account will be called `alpha`

Copy those keys as we will need them soon.

{% hint style="info" %}
Your public and private keys will be different \(as the result of the `suggest_brain_key` command is random\) so make sure you use those. Also, you are free to choose any other name different from `alpha`.
{% endhint %}

The `register_account` command allows you to register an account using only a public key.

```text
register_account alpha TEST78CuY47Vds2nfw2t88ckjTaggPkw16tLhcmg4ReVx1WPr1zRL5 TEST78CuY47Vds2nfw2t88ckjTaggPkw16tLhcmg4ReVx1WPr1zRL5 nathan nathan 0 true
```

{% hint style="info" %}
Make sure you replace `TEST78CuY4...WPr1zRL5` with your version of it.
{% endhint %}

The new account has been created but it's not in your wallet at this stage. We need to import it using the `import_key` command and `alpha`'s private key:

```text
import_key alpha 5JDh3XmHK8CDaQSxQZHh5PUV3zwzG68uVcrTfmg9yQ9idNisYnE
```

{% hint style="info" %}
Make sure you replace `5JDh3XmH...9idNisYnE` with your version of it.
{% endhint %}

### 8.3. Transfer Funds Between Accounts

As a final step, we will transfer some money from `nathan` to `alpha`. For that we use the `transfer` command:

```text
transfer nathan alpha 2000000000 TEST "here is some cash" true
```

{% hint style="info" %}
The text `here is some cash` is an arbitrary memo you can attach to a transfer. If you don't need a memo, just use `""` instead.
{% endhint %}

And now you can verify that `alpha` has indeed received the money:

```text
list_account_balances alpha
```

## 9. Set Up a Second Node

If you want to set up a second node \(with the same genesis file\) and connect it to the first node, use the p2p-endpoint of the first node as the seed-node for the second. The below are example settings.

**In the first node `config.ini`:**

```text
p2p-endpoint = 127.0.0.1:11101

# seed-node = // seed-node is intentionally left blank!
# seed-nodes = // seed-nodes is also intentionally left blank!

rpc-endpoint = 127.0.0.1:11201
```

**In the second node `config.ini`:**

We set the first node's `p2p-endpoint` as the second node's `seed-node`.

```text
p2p-endpoint = 127.0.0.1:11102

seed-node = 127.0.0.1:11101

rpc-endpoint = 127.0.0.1:11202
```

Lastly, the same witness IDs can be used in the second node, but the keys used for node production must be different. This allows you to swap block production between the two nodes by updating the witness accounts' signing keys \(with `update_witness`\).

Another option is to use different witnesses on each node so that block production alternates between the nodes. The log output of each node should show blocks received from the other node. \(i.e., `got_block`\)

## 10. Related Documents

{% page-ref page="../the-basics/hardware-requirements.md" %}

{% page-ref page="../the-basics/using-the-cli-wallet.md" %}

{% page-ref page="../witnesses/installation-guides/manual-install.md" %}

{% page-ref page="../the-basics/auto-starting-a-node.md" %}

