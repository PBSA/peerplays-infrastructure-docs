---
description: Set up a Sidechain Operator Node (SON) using a pre-configured Docker container
---

# Docker Install

This document assumes that you are running Ubuntu 18.04. Other Debian based releases may also work with the provided script.

{% hint style="info" %}
This tutorial will take you through the steps required to have an operating SON. Since SONs serve the purpose of facilitating transfers of assets between the Peerplays blockchain and other blockchains, we'll need to connect to another chain to be of any use...

Let's use **Bitcoin**! 😎 
{% endhint %}

The following steps outline the Docker installation of a \(Bitcoin enabled\) SON:

1. Preparing the Environment
2. Installing Docker
3. The Bitcoin node
4. Installing the `peerplays:son` image
5. Starting the environment
6. Using the CLI wallet
7. Update `config.ini` with SON Account Info

## 1. Preparing the Environment

{% hint style="info" %}
Before we begin, to set up a SON node requires about 110 PPY. This is to pay for an upgraded account \(5 PPY\) and to fund two vesting balances \(50 PPY each\). The remaining funds are to pay for various transaction fees while setting up the node. Please see [Obtaining Your First Tokens](../../the-basics/obtaining-your-first-tokens.md) for more info.
{% endhint %}

### 1.1. Hardware requirements

Please see the SON [hardware requirements](../../the-basics/hardware-requirements.md).

For the docker install, we'll be using a self-hosted **Bitcoin** node. The requirements that we'll need for this guide are as follows \(as per the hardware requirements docs\):

| Bitcoin node type | CPU | Memory | Storage | Bandwidth | OS |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Self-Hosted, Reduced Storage | 2 Cores | 16GB | 150GB SSD | 1Gbps | Ubuntu 18.04 |

### 1.2. Installing the required dependencies

```text
sudo apt-get update
sudo apt-get install vim git curl
```

Then we'll clone the Peerplays Docker repository.

```text
git clone https://gitlab.com/PBSA/tools-libs/peerplays-docker.git -b release
```

## 2. Installing Docker

{% hint style="info" %}
It is required to have Docker installed on the system that will be performing the steps in this document.
{% endhint %}

Docker can be installed using the `run.sh` script inside the Peerplays Docker repository:

```text
cd peerplays-docker
./run.sh install_docker
```

Since the script has added the currently logged in user to the Docker group, you'll need to re-login \(or close and reconnect SSH\) for Docker to function correctly. You can check to see if the current user belongs to the Docker group with the `groups` command. If the Docker group is still not listed after a re-login, you'll have to reboot the machine with `sudo reboot`  \(This will be the case if your using Ubuntu 20.04\).

{% hint style="info" %}
You can look at [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/) to learn more on how to install Docker. Or if you are having permission issues trying to run Docker, use `sudo` or look at [https://docs.docker.com/engine/install/linux-postinstall/](https://docs.docker.com/engine/install/linux-postinstall/).
{% endhint %}

### 2.1. Setting up the .env file

Copy the `example.env` to `.env` located in the root of the repository:

```text
cd ~/peerplays-docker
cp example.env .env
```

We're going to have to make some changes to the `.env` file so we'll open that now using the Vim editor.

```text
vim .env
```

Here are the important parts of the `.env` file. These will be the parts that need to be edited or optionally edited. The rest of the file should be unchanged.

```text
# Default SON wallet used inside bitcoind-node with ./run.sh start_son_regtest
# ***NOTE:*** You can change this if you like, but you'll also need to change the "bitcoin-wallet"
# setting in the Peerplays config.ini file to the same value!
SON_WALLET="son-wallet"

# Comma separated port numbers to expose to the internet (binds to 0.0.0.0)
# Expose 9777 to the internet, but only expose RPC ports 8090 and 8091 onto 127.0.0.1 (localhost)
# allowing the host machine access to the container's RPC ports via 127.0.0.1:8090 and 127.0.0.1:8091
# We'll need ports 8090 and 8091 open to our localhost to interact with the Peerplays CLI Wallet.
PORTS=9777,127.0.0.1:8090:8090,127.0.0.1:8091:8091

# Websocket RPC node to use by default for ./run.sh remote_wallet
REMOTE_WS=""

```

## 3. Connect to Bitcoin Network and Generate an Address

There are two options available to connect to the Bitcoin network.

1. Run a Bitcoin node yourself
2. Find an open Bitcoin node to connect to

For the purposes of this guide, I'll discuss how to run a node yourself as that will be a more reliable connection for now. Either way you go, you'll need to collect the following information to use in the `config.ini` file:

* The IP address of a Bitcoin node you can connect to \(127.0.0.1 if self-hosting\)
* ZMQ port of the Bitcoin node \(default is 1111\)
* RPC port of the Bitcoin node \(default is 8332\)
* Bitcoin RPC connection username \(default is 1\)
* Bitcoin RPC connection password \(default is 1\)
* Bitcoin wallet label \(default is son-wallet\)
* Bitcoin wallet password
* A new Bitcoin address
* The Public key of the Bitcoin address
* The Private key of the Bitcoin address

### 3.1. Install Bitcoin-core

First we'll download and install one of the official Bitcoin Core binaries:

```text
cd ~
wget https://bitcoincore.org/bin/bitcoin-core-0.21.1/bitcoin-0.21.1-x86_64-linux-gnu.tar.gz
# Or if you're using ARM architecture...
# wget https://bitcoincore.org/bin/bitcoin-core-0.21.1/bitcoin-0.21.1-aarch64-linux-gnu.tar.gz

tar xzf bitcoin-0.21.1-x86_64-linux-gnu.tar.gz
sudo install -m 0755 -o root -g root -t /usr/local/bin bitcoin-0.21.1/bin/*
```

{% hint style="warning" %}
The official Bitcoin Core binaries can be found here: [https://bitcoincore.org/en/download/](https://bitcoincore.org/en/download/)

The latest version is 0.21.1 as of July 2021. You may want to find and download the latest version of the binaries just like you would for the 0.21.1 version above.
{% endhint %}

Then we make a config file to manage the settings of our new Bitcoin node.

```text
cd ~
mkdir .bitcoin
cd .bitcoin
vim bitcoin.conf
```

in the Vim text editor we'll set the following \(**You can copy and paste the content of this complete config file**\):

```text
# This config should be placed in following path:
# /home/ubuntu/.bitcoin/bitcoin.conf

# [core]
# Only download and relay blocks - ignore unconfirmed transaction
blocksonly=1
# Run in the background as a daemon and accept commands.
daemon=1
# Set database cache size in megabytes; machines sync faster with a larger cache. Recommend setting as high as possible based upon machine's available RAM.
dbcache=1024
# Reduce storage requirements by only storing most recent N MiB of block. This mode is incompatible with -txindex and -rescan. WARNING: Reverting this setting requires re-downloading the entire blockchain. (default: 0 = disable pruning blocks, 1 = allow manual pruning via RPC, greater than 550 = automatically prune blocks to stay under target size in MiB).
prune=10000

# [network]
# Bind to given address and always listen on it. (default: 0.0.0.0). Use [host]:port notation for IPv6. Append =onion to tag any incoming connections to that address and port as incoming Tor connections
bind=0.0.0.0
# Listen for incoming connections on non-default port.
port=8333

# [rpc]
# Accept command line and JSON-RPC commands.
server=1
# Accept public REST requests.
rest=1
# Bind to given address to listen for JSON-RPC connections. This option is ignored unless -rpcallowip is also passed. Port is optional and overrides -rpcport. Use [host]:port notation for IPv6. This option can be specified multiple times. (default: 127.0.0.1 and ::1 i.e., localhost, or if -rpcallowip has been specified, 0.0.0.0 and :: i.e., all addresses)
rpcbind=0.0.0.0
# Listen for JSON-RPC connections on this port
rpcport=8332
# Allow JSON-RPC connections from specified source. Valid for <ip> are a single IP (e.g. 1.2.3.4), a network/netmask (e.g. 1.2.3.4/255.255.255.0) or a network/CIDR (e.g. 1.2.3.4/24). This option can be specified multiple times.
rpcallowip=0.0.0.0/32
rpcuser=1
rpcpassword=1

# [zeromq]
# Enable publishing of block hashes to <address>.
zmqpubhashblock=tcp://0.0.0.0:11111
# Enable publishing of transaction hashes to <address>.
zmqpubhashtx=tcp://0.0.0.0:11111
# Enable publishing of raw block hex to <address>.
zmqpubrawblock=tcp://0.0.0.0:11111
# Enable publishing of raw transaction hex to <address>.
zmqpubrawtx=tcp://0.0.0.0:11111


# [Sections]
# Most options automatically apply to mainnet, testnet, and regtest networks.
# If you want to confine an option to just one network, you should add it in the relevant section.
# EXCEPTIONS: The options addnode, connect, port, bind, rpcport, rpcbind and wallet
# only apply to mainnet unless they appear in the appropriate section below.

# Options only for mainnet
[main]

# Options only for testnet
[test]

# Options only for regtest
[regtest]
```

Save and quit the Vim editor.

{% hint style="info" %}
The settings in the config file above are set to reduce the requirements of the server. Block pruning and setting the node to Blocks Only save network and storage resources. For more information, see [https://bitcoin.org/en/full-node\#reduce-storage](https://bitcoin.org/en/full-node#reduce-storage).
{% endhint %}

Lastly we'll set a Cron job to ensure the Bitcoin node starts up every time the server starts.

```text
cd ~
sudo crontab -e
```

At the bottom of the crontab file, add the following:

```text
@reboot bitcoind -daemon
# note: there must be a new line at the end of the crontab file! Otherwise, it won't work.

```

Save and quit the crontab file. Now we're ready to fire up the Bitcoin node!

```text
bitcoind -daemon
```

{% hint style="info" %}
If successful, you'll see `Bitcoin Core starting`. As an extra check to see if everything is working, try the `bitcoin-cli -version` or `bitcoin-cli getblockchaininfo` commands. 

You can also use this website to check the status of your node: [https://bitnodes.io/](https://bitnodes.io/)

If you use the Bitnodes website, your node will appear as "down" until it's almost done downloading and verifying the Bitcoin chain. This can take a while.
{% endhint %}

Your Bitcoin node should now be downloading the Bitcoin blockchain data from other nodes. This might take a few hours to complete even though we cut down the requirements with block pruning. It's a lot of data after all.

### 3.2. Use the bitcoin-cli program to make a new wallet

We'll need a wallet to store your Bitcoin address.

```text
bitcoin-cli createwallet "son-wallet"

# To create a wallet with a password, you'd do it like this:
# bitcoin-cli createwallet "son-wallet" false false "mycoolpassword"
# Note the two false parameters in the line above.
# For more information on this command, see https://developer.bitcoin.org/reference/rpc/cre
```

{% hint style="danger" %}
At this point we hit a fork in the road! You'll need to do _one_ of the following:

Option 1: Generate a **new** Bitcoin address to use for your SON node. \(see **3.2.a.** below\)

Option 2: Import an **existing** Bitcoin address to use for your SON node. \(see **3.2.b.** below\)

Either way, you'll need the Bitcoin address, its public key, and its private key.
{% endhint %}

### 3.3.a. Make a new Bitcoin address

Now we will create a Bitcoin address.

```text
bitcoin-cli -rpcwallet="son-wallet" getnewaddress

# This will return something like:
# bc1qsx7as3r9d92tjvxrgwue7z66f2r3pw04j67lht
```

Then we'll use this address to get its keys.

```text
bitcoin-cli -rpcwallet="son-wallet" getaddressinfo bc1qsx7as3r9d92tjvxrgwue7z66f2r3pw04j67lht

# This returns a lot of info but what we're looking for is the "pubkey".
# In this case, it's "pubkey": "023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9"
```

Now we get the private key.

```text
bitcoin-cli -rpcwallet="son-wallet" dumpprivkey bc1qsx7as3r9d92tjvxrgwue7z66f2r3pw04j67lht

# This returns the private key like this:
# KzD2WHeG49aYhYVcxBwfknm58YqDc7WEg7aWWU8P8BJ8gp1g3AuD
```

### 3.3.b. Import an existing Bitcoin address

{% hint style="info" %}
You don't need to do this if you made a new address in step **3.3.a.** above!
{% endhint %}

Now we will import an existing Bitcoin address. You'll need the private key of the existing address which should be obtainable from your current wallet. You may not be able to get the private key from online or cloud wallet providers \(contact their support teams for assistance with this.\) The `importprivkey` command might take a few minutes to finish because this will cause the node to rescan the Bitcoin network.

```text
bitcoin-cli -rpcwallet="son-wallet" importprivkey "yourprivatekeygoeshere"
```

Then you can get the public key with the `getaddressinfo` command.

```text
bitcoin-cli -rpcwallet="son-wallet" getaddressinfo "existingaddressyouhaveimported"

# This returns a lot of info but what we're looking for is the "pubkey".
# It looks like this, "pubkey": "023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9"
```

### 3.4. A quick recap

That was a lot to go over. Let's collect our data. Here's an example:

```text
Bitcoin Address for the SON Account = bc1qsx7as3r9d92tjvxrgwue7z66f2r3pw04j67lht
The public key = 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9
the private key = KzD2WHeG49aYhYVcxBwfknm58YqDc7WEg7aWWU8P8BJ8gp1g3AuD

And we'll convert this info into a tuple ["Bitcoin public key", "Bitcoin private key"]
["023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9","KzD2WHeG49aYhYVcxBwfknm58YqDc7WEg7aWWU8P8BJ8gp1g3AuD"]
```

Keep your tuple handy. We'll need it in the Peerplays config file.

## 4. Installing the peerplays:son image

Use `run.sh` to pull the SON image:

```text
cd ~/peerplays-docker
sudo ./run.sh install son
```

### 4.1. Setting up config.ini file

{% hint style="warning" %}
There are many example configuration files, make sure to copy the right one. In this case it is: **`config.ini.son-exists.example`**
{% endhint %}

Copy the correct example configuration:

```text
cd ~/peerplays-docker
cd data/witness_node_data_dir
sudo cp config.ini.son-exists.example config.ini
```

We'll need to make an edit to the `config.ini` file as well.

```text
sudo vim config.ini
```

The important parts of the `config.ini` file \(for now!\) should look like the following. But don't forget to add your own Bitcoin public and private keys!

```text
# Endpoint for P2P node to listen on
p2p-endpoint = 0.0.0.0:9777

# P2P nodes to connect to on startup (may specify multiple times)
# seed-node =

# JSON array of P2P nodes to connect to on startup

## Empty seed nodes means new network
seed-nodes = ["96.46.49.1:9777","witness.serverpit.com:9777","173.249.23.108:9777","seed01.eifos.org:7777","seed.mainnet.peerblock.trade:9777"]

## Connect to other SON nodes
# seed-nodes =

# Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints.
# checkpoint = 

# Endpoint for websocket RPC to listen on
rpc-endpoint = 127.0.0.1:8090

# Endpoint for TLS websocket RPC to listen on
# rpc-tls-endpoint = 

# The TLS certificate file for this server
# server-pem = 

# Password for this certificate
# server-pem-password = 

# File to read Genesis State from
# genesis-json = /peerplays/son-genesis.json

# Block signing key to use for init witnesses, overrides genesis file
# dbg-init-key = 

# JSON file specifying API permissions
# api-access = 

# Space-separated list of plugins to activate
plugins = witness account_history market_history accounts_list affiliate_stats bookie peerplays_sidechain

# ==============================================================================
# peerplays_sidechain plugin options
# ==============================================================================

# ID of SON controlled by this node (e.g. "1.27.5", quotes are required)
# son-id = ""

# IDs of multiple SONs controlled by this node (e.g. ["1.27.5", "1.27.6"], quotes are required)
# son-ids = [""]

# Tuple of [PublicKey, WIF private key] (may specify multiple times)
# peerplays-private-key = ["", ""]

# IP address of Bitcoin node
bitcoin-node-ip = 172.18.0.2

# ZMQ port of Bitcoin node
bitcoin-node-zmq-port = 11111

# RPC port of Bitcoin node
bitcoin-node-rpc-port = 8332

# Bitcoin RPC user
bitcoin-node-rpc-user = 1

# Bitcoin RPC password
bitcoin-node-rpc-password = 1

# Bitcoin wallet
bitcoin-wallet = son-wallet

# Bitcoin wallet password
bitcoin-wallet-password = ""

# Tuple of [Bitcoin public key, Bitcoin private key] (may specify multiple times)
# This should be the public and private keys of the Bitcoin wallet you'll use. You already entered the private key in the .env file above.
bitcoin-private-key = ["", ""]
```

Save the file and quit.

## 5. Starting the environment

Once the configuration is set up, use `run.sh` to start the peerplaysd and bitcoind containers:

```text
cd ~/peerplays-docker

# You'll also want to set the shared memory size (use sudo if not logged in as root). 
# Adjust 64G to whatever size is needed for your type of server and make sure to leave growth room.
# Please be aware that the shared memory size changes constantly. Ask in a witness chatroom if you're unsure.
./run.sh shm_size 64G

# It's recommended to set vm.swappiness to 1, which tells the system to avoid using swap 
# unless absolutely necessary. To persist on reboot, place in /etc/sysctl.conf
sysctl -w vm.swappiness=1

# Start the SON environment
./run.sh start_son_regtest
```

The SON network will be created and the seed \(peerplaysd\) and bitcoind-node \(bitcoind\) containers will be launched. To check the status, inspect the logs:

```text
./run.sh logs
```

If the logs are throwing errors, perform a replay.

```text
# replay the blockchain
./run.sh replay_son
```

## 6. Using the CLI wallet

After starting the environment, the CLI wallet for the seed \(peerplaysd\) will be available.

### 6.1. Connecting to the blockchain with the CLI Wallet

Open another terminal and use `docker exec` to connect to the wallet.

```text
# In the local terminal
docker exec -it seed cli_wallet
```

{% hint style="info" %}
If an exception is thrown and contains `Remote server gave us an unexpected chain_id`, then copy the `remote_chain_id` that is provided by it. Pass the chain ID to the CLI wallet:

```text
# In the local terminal
docker exec -it seed cli_wallet --chain-id=<CHAIN-ID>
```
{% endhint %}

Set a password for the wallet and then unlock it:

```text
# In the CLI wallet
set_password <YOUR-WALLET-PASSWORD>
unlock <YOUR-WALLET-PASSWORD>
```

The CLI wallet will show `unlocked >>>` when successfully unlocked

{% hint style="info" %}
A list of CLI wallet commands is available here: [https://www.peerplays.tech/api/peerplays-wallet-api/wallet-calls](https://www.peerplays.tech/api/peerplays-wallet-api/wallet-calls)
{% endhint %}

Assuming we're starting without any account, it's easiest to create an account with the Peerplays GUI Wallet. The latest release is located here: [https://github.com/peerplays-network/peerplays-core-gui/releases/latest](https://github.com/peerplays-network/peerplays-core-gui/releases/latest). When you create an account with the GUI wallet, you should have a username and password. We'll need those for the next steps. First we'll get the private key for the new account.

```text
# In the cli_wallet...

get_private_key_from_password <put your username here> active <put your password here>

# For example:
# get_private_key_from_password mynew-son active LExu4QtSapqzdEaly2RwMugul3GhedTf234IiF2zzzfU4nuKXow8

# The program will return a tuple of the public and private keys for your account. 
# That will look something like this:
# [
#   "PPY...random.numbers.and.letters...",
#   "...random.numbers.and.letters..."
# ]
```

The key beginning with "PPY" is the public key. The other key is the private key. We'll need to import this private key into the cli\_wallet.

```text
# In the cli_wallet...

import_key "mynew-son" ...random.numbers.and.letters...

# If this is successful, the return is simply
# true
```

Next we'll upgrade the account to a lifetime membership.

{% hint style="info" %}
At the time of writing this guide, it costs 5 PPY to perform this operation. You'll need that in your account first! To this end, check out [Obtaining Your First Tokens](../../the-basics/obtaining-your-first-tokens.md).
{% endhint %}

```text
# In the cli_wallet...

upgrade_account mynew-son true
```

Next we'll create the vesting balances.

```text
# In the cli_wallet...

create_vesting_balance mynew-son 50 PPY son true
create_vesting_balance mynew-son 50 PPY normal true
get_vesting_balances mynew-son

# The return here will show us the IDs of the vesting balances.
# For example:
# [{
#     "id": "1.13.79",
#     "owner": "1.2.58",
# ...
#   },{
#     "id": "1.13.80",
#     "owner": "1.2.58",
# ...
#   }
# ]
```

Now we have all the info we need to create a SON account.

```text
# In the cli_wallet...

create_son mynew-son "https://www.mynew-son.com/my-son-proposal" 1.13.79 1.13.80 [[bitcoin, 023b907586045625367ecd62c5d889591586c87e57fa49be21614209489f00f1b9]] true

# The above command is structured like this:
# create_son <username> "<SON proposal url>" <son vesting balance ID> <normal vesting balance ID> [[bitcoin, <bitcoin public key>]] true
```

To get the SON ID:

```text
# In the cli_wallet...

get_son mynew-son

# Which returns:
# {
#   "id": "1.27.16",
#   ...
# }
```

We'll set the signing key using the active key from the owning account:

```text
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

## 7. Update config.ini with SON Account Info

Lets stop the node for now so we can finish up the `config.ini`.

```text
cd ~/peerplays-docker
./run.sh stop
```

Ensure the following config settings are in the `config.ini` file under the peerplays\_sidechain plugin options.

```text
cd data/witness_node_data_dir
vim config.ini
```

```text
# In the config.ini file...

# ID of SON controlled by this node (e.g. "1.27.5", quotes are required)
son-id = "1.27.16"

# Tuple of [PublicKey, WIF private key] (may specify multiple times)
peerplays-private-key = ["PPY7SUmjftH3jL5L1YCTdMo1hk5qpZrhbo4MW6N2wWyQpjXkT7ByB", "5JKvPJkerMNVEubsbKN8Xd8wGaU1ifhv7xAwy9gFJP6yMEoTkSd"]
```

Then it's just a matter of starting the node back up!

```text
# replay the blockchain
./run.sh replay_son
```

{% hint style="success" %}
Your SON is now good to go!
{% endhint %}

## 8. What's Next?

### 8.1. Auto-starting your node

Up until this point we have been running the node in the foreground which is fragile and inconvenient. So let's start the node as a service when the system boots up instead.

{% page-ref page="../../the-basics/auto-starting-a-node.md" %}

### 8.2. Creating a backup node

After that, it would be smart to create a backup server to enable you to make software updates, troubleshoot issues with the node, and otherwise take your node offline without causing service outages.

{% page-ref page="../../the-basics/backup-servers.md" %}

### 8.3. Join the Testnet

As we all know, testing should never be done in production. This is why all node operators must also participate in the public testnet.

{% page-ref page="../../the-basics/joining-the-public-testnet.md" %}

### 8.4. Configure more sidechains

Why stop at Bitcoin?

{% page-ref page="../enabling-components.md" %}

### 8.5. Fire up another node 🔥 

Now you have a SON, but have you thought about becoming a Witness? It will be a piece of cake for you since you've already set up a SON.

{% hint style="warning" %}
When you make any node, don't forget [Testnet](../../the-basics/joining-the-public-testnet.md)!
{% endhint %}

### 8.6. Enable SSL to encrypt your node traffic

If you have a node that is accessible from the internet \(for example, an API or Seed node\) it would be wise to enable SSL connections to your node.

{% page-ref page="../../advanced-topics/reverse-proxy-for-enabling-ssl.md" %}

## 9. Glossary

**SON**: Sidechain Operator Node - An independent server operator which facilitates the transfer of off-chain assets \(like Bitcoin or Ethereum tokens\) between the Peerplays chain and the asset's native chain.

**Witness:** An independent server operator which validates network transactions.

**Witness Node:** Nodes with a closed RPC port. They don't allow external connections. Instead these nodes focus on processing transactions into blocks.

## 10. Related Documents

* [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)
* [https://docs.docker.com/engine/install/linux-postinstall/](https://docs.docker.com/engine/install/linux-postinstall/)

**Vim** is a text editing program available for Ubuntu 18.04. See [vim.org](https://www.vim.org/)

