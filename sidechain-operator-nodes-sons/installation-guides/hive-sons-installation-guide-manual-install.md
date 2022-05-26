---
description: How to set up a HIVE Sidechain Operator Node (SON) by building the source code
---

# HIVE SONs Installation Guide - Manual Install

The process of manually installing a HIVE SON is similar to installing a Witness Node. This is a step-by-step guide for setting up a new HIVE SON and having it running on Peerplays blockchain.

SONs are valuable as they allow the transfer of assets between the Peerplays and HIVE.

We ask that you review the requirements for setting up a SON before proceeding and running a manual installation following this guide.

Here’s an outline of the steps for installing a HIVE-enabled SON:

1. Preparing the Environment
2. Build Peerplays
3. Connect to the HIVE Network and Generate an Address
4. Create a SON Account
5. Configure the SON
6. Start the SON
7. (Optional) Automatically Start the Node as a Service

{% hint style="info" %}
In order to set up a SON node, you must have 110 PPY. This amount covers the upgraded account (which costs 5 PPY) and funds two vesting balances (50 PPY each). The remaining funds are for paying the various transaction fees while setting up the node. Please see [Obtaining Your First Tokens](https://app.gitbook.com/o/-Lpj4bCPw8iNnqCwrLCQ/s/-McxuwdOwK1wrj2xdejn/the-basics/obtaining-your-first-tokens) for more information.

Note that these fees will likely change over time as recommended by the Committee of Advisors.
{% endhint %}

### **1. Preparing the Environment** <a href="#_sxi2boxcxsag" id="_sxi2boxcxsag"></a>

### **1.1. Hardware Requirements** <a href="#_luoaui1jhpxa" id="_luoaui1jhpxa"></a>

Please review the general SON [hardware requirements](https://app.gitbook.com/o/-Lpj4bCPw8iNnqCwrLCQ/s/-McxuwdOwK1wrj2xdejn/the-basics/hardware-requirements).

For the manual install, we'll be using a self-hosted HIVE node. The requirements that we'll need for this guide would be as follows (as per the hardware requirements doc):

| HIVE node type               | CPU     | Memory | Storage   | Bandwidth | OS           |
| ---------------------------- | ------- | ------ | --------- | --------- | ------------ |
| Self-Hosted, Reduced Storage | 2 Cores | 16GB   | 150GB SSD | 1Gbps     | Ubuntu 20.04 |

This table shows the bare minimum memory requirements for operating the node. If you run into some errors, chances are, you need more memory. We ask that you review [Installing vs Operating](https://app.gitbook.com/o/-Lpj4bCPw8iNnqCwrLCQ/s/-McxuwdOwK1wrj2xdejn/the-basics/hardware-requirements#4-2-installing-vs-operating) for more details.

### &#x20;<a href="#_vl8o87tf576f" id="_vl8o87tf576f"></a>

### **1.2. Installing the required dependencies** <a href="#_ssm54j2oajcg" id="_ssm54j2oajcg"></a>

The following dependencies are important for a clean install on Ubuntu 20.04 LTS:

```
sudo apt update
sudo apt install -y apt-utils autoconf bash build-essential ca-certificates \
     cmake dnsutils doxygen expect git graphviz libboost1.67-all-dev libbz2-dev \
     libcurl4-openssl-dev libncurses-dev libreadline-dev libsnappy-dev \
     libssl-dev libtool libzip-dev libzmq3-dev locales mc nano net-tools \
     ntp openssh-server pkg-config perl python3 python3-jinja2 sudo wget
```

### **2. Build Peerplays** <a href="#_jp9tyzvf74vv" id="_jp9tyzvf74vv"></a>

Now you must build Peerplays with the official source code from GitLab.

```
cd $HOME
git clone https://gitlab.com/PBSA/peerplays.git
cd peerplays
git checkout master # --> replace with most recent tag
git submodule update --init --recursive
git submodule sync --recursive
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j4 cli_wallet witness_node
```

{% hint style="info" %}
**Note**: "master" can be replaced with the most recent release tag.

For example:

`git checkout 1.5.13`

where 1.5.13 is the latest production release tag as of July 2021. The list of releases is [located here](https://github.com/peerplays-network/peerplays/releases).
{% endhint %}

{% hint style="info" %}
**Note**: Your binaries will be located at:

* `$HOME/peerplays/build/programs/cli_wallet/cli_wallet`
* `$HOME/peerplays/build/programs/witness_node/witness_node`

Copy them to an empty directory:

```
mkdir $HOME/peerplays-mainnet
cp $HOME/peerplays/build/programs/cli_wallet/cli_wallet $HOME/peerplays-mainnet/
cp $HOME/peerplays/build/programs/witness_node/witness_node $HOME/peerplays-mainnet/
```
{% endhint %}

### **2.1. Start the node to generate the config.ini file** <a href="#_et6cotlyz9m1" id="_et6cotlyz9m1"></a>

We start the SON Node with the `witness_node` command even though we want to set up this node as a HIVE SON. This is because the same program is used to operate different types of nodes depending on how we configure the program. For more information on this, see[ Peerplays Node Types](https://infra.peerplays.tech/the-basics/peerplays-node-types).

If you have installed the blockchain following the above steps, the HIVE node can be started as follows:

```
cd $HOME/peerplays-mainnet/
./witness_node
```

Running the witness\_node program will create a `config.ini file` with some default settings. We'll need to edit the config file so we'll stop the program for now. Stop the program with `ctrl + c`.

### **2.2. Editing the config file** <a href="#_l7qsbzhjj3a2" id="_l7qsbzhjj3a2"></a>

This is a checklist of what you need for successfully running a SON:

1. Your SON ID
2. SON owner Peerplays account active public and private key
3. SON owner Peerplays account should be funded
4. Bitcoin node IP address
5. Bitcoin node RPC port
6. Bitcoin node ZMQ port
7. Encrypted empty bitcoin wallet
8. Bitcoin wallet password
9. Bitcoin public/private keys used by your SON node
10. Hive node RPC endpoint (check the list of available public nodes at https://developers.hive.io/quickstart/)
11. Hive node RPC basic auth user name, if used
12. Hive node RPC basic auth password, if used
13. Hive account name and active private key used by your SON

You can get Hive account here [https://signup.hive.io/](https://signup.hive.io/)

Depending on the account provider you choose, the process of obtaining private keys is different. For 3Speak provider, you can get them from your 3Speak account settings

[https://support.3speak.tv/support/solutions/articles/67000590768-requesting-private-keys](https://support.3speak.tv/support/solutions/articles/67000590768-requesting-private-keys)

These are the sections of the default config file you need to change:

```
# Space-separated list of plugins to activate
plugins = account_history accounts_list affiliate_stats bookie market_history witness

...
# ==============================================================================
# peerplays_sidechain plugin options
# ==============================================================================
# ID of SON controlled by this node (e.g. "1.33.5", quotes are required)
# son-id =
# IDs of multiple SONs controlled by this node (e.g. ["1.33.5", "1.33.6"], quotes are required)
# son-ids =
# Tuple of [PublicKey, WIF private key] (may specify multiple times)
peerplays-private-key = ["PPY6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
# Sidechain retry throttling threshold
sidechain-retry-threshold = 150
# Outputs RPC calls to console
debug-rpc-calls = 0
# Bitcoin sidechain handler enabled
bitcoin-sidechain-enabled = 0
# IP address of Bitcoin node
bitcoin-node-ip = 127.0.0.1
# ZMQ port of Bitcoin node
bitcoin-node-zmq-port = 11111
# RPC port of Bitcoin node
bitcoin-node-rpc-port = 8332
# Bitcoin RPC user
bitcoin-node-rpc-user = 1
# Bitcoin RPC password
bitcoin-node-rpc-password = 1
# Bitcoin wallet
# bitcoin-wallet =
# Bitcoin wallet password
# bitcoin-wallet-password =
# Tuple of [Bitcoin public key, Bitcoin private key] (may specify multiple times)
bitcoin-private-key = ["02d0f137e717fb3aab7aff99904001d49a0a636c5e1342f8927a4ba2eaee8e9772","cVN31uC9sTEr392DLVUEjrtMgLA8Yb3fpYmTRj7bomTm6nn2ANPr"]
# Hive sidechain handler enabled
hive-sidechain-enabled = 0
# Hive node RPC URL [http[s]://]host[:port]
hive-node-rpc-url = 127.0.0.1:28090
# Hive node RPC user
# hive-node-rpc-user =
# Hive node RPC password
# hive-node-rpc-password =
# Tuple of [Hive public key, Hive private key] (may specify multiple times)
hive-private-key = ["TST6LLegbAgLAy28EHrffBVuANFWcFgmqRMW13wBmTExqFE9SCkg4","5JNHfZYKGaomSFvd4NUdQ9qMcEAC43kujbfjueTHpVapX1Kzq2n"]
```

This is how you should edit your config file:

```
---> Add peerplays_sidechain to plugins option
# Space-separated list of plugins to activate
plugins = account_history accounts_list affiliate_stats bookie market_history witness peerplays_sidechain
# ==============================================================================
# peerplays_sidechain plugin options
# ==============================================================================
---> Add your son id here
# ID of SON controlled by this node (e.g. "1.33.5", quotes are required)
# son-id =
---> Leave as it is (dev feature, for multiple sons support)
# IDs of multiple SONs controlled by this node (e.g. ["1.33.5", "1.33.6"], quotes are required)
# son-ids =
---> Add your son owner account public/private key pair (account needs to be funded, for paying fees). Public key should be the same one as in SON object's signing_key field
# Tuple of [PublicKey, WIF private key] (may specify multiple times)
peerplays-private-key = ["PPY6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]
---> Leave as it is
# Sidechain retry throttling threshold
sidechain-retry-threshold = 150
---> Leave as it is (dev feature, if set to 1, you will see RPC communication in the logs, which is very extensive)
# Outputs RPC calls to console
debug-rpc-calls = 0
---> Set to 1. Enables bitcoin sidechain handler
# Bitcoin sidechain handler enabled
bitcoin-sidechain-enabled = 0
---> Set to your bitcoin node ip address
# IP address of Bitcoin node
bitcoin-node-ip = 127.0.0.1
---> Set to your bitcoin node zmq port
# ZMQ port of Bitcoin node
bitcoin-node-zmq-port = 11111
---> Set to your bitcoin node RPC port
# RPC port of Bitcoin node
bitcoin-node-rpc-port = 8332
---> Set to your bitcoin node RPC user, if needed (for basic http auth)
# Bitcoin RPC user
bitcoin-node-rpc-user = 1
---> Set to your bitcoin node RPC password, if needed (for basic http auth)
# Bitcoin RPC password
bitcoin-node-rpc-password = 1
---> Set to a son bitcoin wallet name (you need to create it on your own)
# Bitcoin wallet
# bitcoin-wallet =
---> Set to a son bitcoin wallet password (you need to set it up on your own)
# Bitcoin wallet password
# bitcoin-wallet-password =
---> Set to your SON node bitcoin public/private key pair. Public key should be the same as one in SON object's sidechain_public_keys map
# Tuple of [PublicKey, WIF private key] (may specify multiple times)
bitcoin-private-key = ["02d0f137e717fb3aab7aff99904001d49a0a636c5e1342f8927a4ba2eaee8e9772","cVN31uC9sTEr392DLVUEjrtMgLA8Yb3fpYmTRj7bomTm6nn2ANPr"]
---> Set to 1. Enables Hive sidechain handler
# Hive sidechain handler enabled
hive-sidechain-enabled = 0
---> Set to your Hive node RPC endpoint. You can also use public ones (https://developers.hive.io/quickstart/)
# Hive node RPC URL [http[s]://]host[:port]
hive-node-rpc-url = 127.0.0.1:28090
---> Set to your Hive node RPC user, if needed (for basic http auth)
# Hive node RPC user
# hive-node-rpc-user =
---> Set to your Hive node RPC password, if needed (for basic http auth)
# Hive node RPC password
# hive-node-rpc-password =
---> Set to your SON node Hive account/active private key. Account name should be the same as one in SON object's sidechain_public_keys map
# Tuple of [Hive public key, Hive private key] (may specify multiple times)
hive-private-key = ["my-hive-account-name","5JNHfZYKGaomSFvd4NUdQ9qMcEAC43kujbfjueTHpVapX1Kzq2n"]
```

You can always take a look at the Peerplays QA environment, as an example of a fully and properly configured Peerplays runtime environment. However, keep in mind that this is mainly a developer tool, and may differ from the current production software:

[https://gitlab.com/PBSA/tools-libs/peerplays-utils/-/blob/master/peerplays-qa-environment/peerplays/config01.ini](https://gitlab.com/PBSA/tools-libs/peerplays-utils/-/blob/master/peerplays-qa-environment/peerplays/config01.ini)

### **4. Use the CLI Wallet to Create a Peerplays SON Account** <a href="#_5jguknqpodk6" id="_5jguknqpodk6"></a>

### **4.1. Becoming a HIVE SON** <a href="#_m60whvf33cog" id="_m60whvf33cog"></a>

Becoming a SON is very similar to becoming a witness. You will need:

* An active user account, upgraded to lifetime member, which will be the owner of the SON account
* Create two vesting balances (types "son" and "normal") of 50 PPY, and get their IDs
* The HIVE account created for the SON account (Follow the instructions here: [https://signup.hive.io/](https://signup.hive.io/))
* Create the SON account, and get its ID
* Set the signing key for the SON account (usually, its a signing key of the owner account)
* Set the HIVE address as a sidechain address for the SON account

### **4.2. Running the cli\_wallet program** <a href="#_vam889mp7vv" id="_vam889mp7vv"></a>

Our Peerplays node will have to be completely in sync with the blockchain before we can use the cli wallet so we'll start the node and wait for it to download all the data.

```
mkdir $HOME/peerplays-mainnet
./witness_node
# If there is an error, try adding the --resync-blockchain or --replay-blockchain flags...
# witness_node --replay-blockchain
```

downloading all the transaction and block data will take hours. Unfortunately this is unavoidable the first time the node syncs with the blockchain. You might want to let this run overnight.

If you just can't wait for your node to sync, you can run the cli\_wallet program on someone else's node. Simply pass the IP address of the other node like so. (In another command line window)

cli\_wallet --server-rpc-endpoint=wss://api.eifos.org

A good resource for server-rpc-endpoints is[ https://beta.eifos.org/status](https://beta.eifos.org/status). They will be listed as API nodes and use the wss:// protocol.

### &#x20;<a href="#_s5s7rpj231se" id="_s5s7rpj231se"></a>

### **4.3. Using the cli\_wallet** <a href="#_zd19z5nllckt" id="_zd19z5nllckt"></a>

Now that we have the cli\_wallet running, you'll notice a new prompt.

```
cd $HOME/peerplays-mainnet
./cli_wallet
new >>>
```

This means we're in a cli\_wallet session. First we'll make a new wallet and unlock it.

```
set_password password
unlock password
```

{% hint style="info" %}
A list of CLI wallet commands is available here: [https://www.peerplays.tech/api/peerplays-wallet-api/wallet-calls](https://www.peerplays.tech/api/peerplays-wallet-api/wallet-calls)
{% endhint %}

Assuming we're starting without any account, it's easiest to create an account with the Peerplays GUI Wallet. The latest release is located here[ https://github.com/peerplays-network/peerplays-core-gui/releases/latest](https://github.com/peerplays-network/peerplays-core-gui/releases/latest). When you create an account with the GUI wallet, you should have a username and password. We'll need those for the next steps. First we'll get the private key for the new account.

```
# In the cli_wallet...
get_private_key_from_password <put your username here> active <put your password here>
# For example:
# get_private_key_from_password mynew-son active LExu4QtSapqzdEaly2RwMugul3GhedTf234IiF2zzzfU4nuKXow8
# The program will return a tuple of the public and private keys for your account.
# That will look something like this:
# [
# "PPY...random.numbers.and.letters...",
# "...random.numbers.and.letters..."
# ]
```

The key beginning with "PPY" is the public key. The other key is the private key. We'll need to import this private key into the cli\_wallet.

```
# In the cli_wallet...

import_key "mynew-son" ...random.numbers.and.letters...

# If this is successful, the return is simply
# true
```

Next we'll upgrade the account to a lifetime membership.

At the time of writing this guide, this costs 5 PPY to perform this operation. You'll need that in your account first! To this end, see[ Obtaining Your First Tokens](https://infra.peerplays.tech/the-basics/obtaining-your-first-tokens).

```
# In the cli_wallet...

upgrade_account mynew-son true
```

Next we'll create the vesting balances.

```
# In the cli_wallet...
create_vesting_balance mynew-son 50 PPY son true
create_vesting_balance mynew-son 50 PPY normal true
get_vesting_balances mynew-son
# The return here will show us the IDs of the vesting balances.
# For example:
# [{
# "id": "1.13.79",
# "owner": "1.2.58",
# ...
# },{
# "id": "1.13.80",
# "owner": "1.2.58",
# ...
# }
# ]
```

Now we have all the info we need to create a SON account.

```
# In the cli_wallet...

create_son mynew-son "https://www.mynew-son.com" 1.13.79 1.13.80 [[bitcoin, 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9]] true

# The above command is structured like this:
# create_son <username> "<SON proposal url>" <son vesting balance ID> <normal vesting balance ID> [[bitcoin, <bitcoin public key>]] true
```

To get the SON ID:

```
# In the cli_wallet...

get_son mynew-son

# Which returns:
# {
#   "id": "1.27.16",
#   ...
# }
```

We'll set the signing key using the active key from the owning account:

```
# In the cli_wallet...

# First we'll get the active key of the owning account.
get_account mynew-son

# In the return, we're looking for the 
# "active": 
# ...
# { ... "key_auths": 
# [[ "PPY7SUmjftH3jL5L1YCTdMo1hk5qpZrhbo4MW6N2wWyQpjXkT7ByB",
# ...

# Using the active public key we just found:
get_private_key PPY7SUmjftH3jL5L1YCTdMo1hk5qpZrhbo4MW6N2wWyQpjXkT7ByB

# This will return the private key.
# "5JKvPJkerMNVEubsbKN8Xd8wGaU1ifhv7xAwy9gFJP6yMEoTkSd"

# Then we update the signing key using the public key.
update_son mynew-son "" "PPY7SUmjftH3jL5L1YCTdMo1hk5qpZrhbo4MW6N2wWyQpjXkT7ByB" [[bitcoin, 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9]] true
```

Now we have our SON account ID and the public and private keys for the SON account. We'll need this for the `config.ini` file.

### **6. Start the SON** <a href="#_nnlqb984enfk" id="_nnlqb984enfk"></a>

After setting up the config.ini file for SON operation, we'll start the node back up.

```
witness_node
```

{% hint style="success" %}
Your HIVE SON is active! 👏
{% endhint %}

### **7. What's Next?** <a href="#_rlgczncszhrs" id="_rlgczncszhrs"></a>

### **7.1. Auto-starting your node** <a href="#_ydniew94pi4e" id="_ydniew94pi4e"></a>

Starting the node as a service when the system boots up is much better than running the node in the foreground, which is unstable and inconvenient.

{% content-ref url="../../the-basics/auto-starting-a-node.md" %}
[auto-starting-a-node.md](../../the-basics/auto-starting-a-node.md)
{% endcontent-ref %}

### **7.2. Creating a backup node** <a href="#_tq1i45jk6nf4" id="_tq1i45jk6nf4"></a>

It would be wise to create a backup server that will allow you to make software changes/updates, troubleshoot node issues, or take down your node without causing any service outages.

{% content-ref url="../../the-basics/backup-servers.md" %}
[backup-servers.md](../../the-basics/backup-servers.md)
{% endcontent-ref %}

### **7.3. Configure more sidechains** <a href="#_r4d86stzosgv" id="_r4d86stzosgv"></a>

Why stop at Hive?

{% content-ref url="../enabling-components.md" %}
[enabling-components.md](../enabling-components.md)
{% endcontent-ref %}

### **7.4. Fire up another node 🔥** <a href="#_mnb0odc8392" id="_mnb0odc8392"></a>

Since you’re not a SON, why not become a Witness as it’s relatively easy since you’ve set up a SON.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

### **7.5. Enable SSL to encrypt your node traffic** <a href="#_dfvg4479cktc" id="_dfvg4479cktc"></a>

Consider enabling SSL connections to your node if your node is accessible from the internet (for example, an API node).

{% content-ref url="../../advanced-topics/reverse-proxy-for-enabling-ssl.md" %}
[reverse-proxy-for-enabling-ssl.md](../../advanced-topics/reverse-proxy-for-enabling-ssl.md)
{% endcontent-ref %}

### **8. Glossary** <a href="#_i0gd34i1360j" id="_i0gd34i1360j"></a>

**SON**: Sidechain Operator Node - An independent server operator which facilitates the transfer of off-chain assets (like Bitcoin or Ethereum tokens) between the Peerplays chain and the asset's native chain.

**Witness:** An independent server operator which validates network transactions.

**Witness Node:** Nodes with a closed RPC port. They don't allow external connections. Instead these nodes focus on processing transactions into blocks.

**Vim**: A text editing program available for Ubuntu 18.04. See[ vim.org](https://www.vim.org/)​
