---
description: Setup a Witness Node by building from the latest source code
---

# Manual Install (Ubuntu 20.04)

This is an introduction for new Witnesses to get up to speed on the Peerplays blockchain. It is intended for Witnesses planning to join a live, already deployed, blockchain.

The following steps outline the manual installation of a Witness Node:

1. Preparing the Environment
2. Build Peerplays
3. Update the `config.ini` File
4. Create a Peerplays Account
5. Update `config.ini` with Witness Account Info
6. Start the Node and Vote for Yourself

{% hint style="info" %}
Before we begin, to set up a Witness node requires about 15 PPY. This is to pay for an upgraded account (5 PPY) and to create a new witness (8 PPY). The remaining funds are to pay for various transaction fees while setting up the node (like voting for yourself!). Please see [Obtaining Your First Tokens](../../the-basics/obtaining-your-first-tokens.md) for more info.

Note that these fees will likely change over time as recommended by the Committee of Advisors.
{% endhint %}

## 1. Preparing the Environment

### 1.1. Hardware requirements

Please see the general Witness [hardware requirements](https://infra.peerplays.tech/the-basics/hardware-requirements).

For the manual install, the requirements that we'll need for this guide would be as follows (as per the hardware requirements doc):

| Node Type? | CPU     | Memory          | Storage   | Bandwidth | OS           |
| ---------- | ------- | --------------- | --------- | --------- | ------------ |
| Witness    | 4 Cores | 16GB :warning:  | 100GB SSD | 1Gbps     | Ubuntu 18.04 |

{% hint style="danger" %}
The memory requirements shown in the table above are adequate to operate the node. Building and installing the node from source code (as with this manual installation guide) will require more memory. You may run into errors during the build and install process if the system memory is too low. See [Installing vs Operating](../../the-basics/hardware-requirements.md#4-2-installing-vs-operating) for more details.
{% endhint %}

### 1.2. Installing the required dependencies

The following dependencies are necessary for a clean install of Ubuntu 20.04:

```
sudo apt-get update
sudo apt-get install \
    apt-utils autoconf bash build-essential ca-certificates clang-format cmake \
    dnsutils doxygen expect git graphviz libboost-all-dev libbz2-dev \
    libcurl4-openssl-dev libncurses-dev libsnappy-dev \
    libssl-dev libtool libzip-dev locales lsb-release mc nano net-tools ntp \
    openssh-server pkg-config perl python3 python3-jinja2 sudo \
    systemd-coredump wget
```

### 1.3. Install libzmq and cppzmq

These components are used for relaying messages between nodes. First we'll install libzmq.

```
cd $HOME
wget https://github.com/zeromq/libzmq/archive/refs/tags/v4.3.4.zip
unzip v4.3.4.zip
cd libzmq-4.3.4
mkdir build
cd build
cmake ..
make -j$(nproc)
sudo make install
sudo ldconfig
```

Then we'll install cppzmq.

```
cd $HOME
wget https://github.com/zeromq/cppzmq/archive/refs/tags/v4.8.1.zip
unzip v4.8.1.zip
cd cppzmq-4.8.1
mkdir build
cd build
cmake ..
make -j$(nproc)
sudo make install
sudo ldconfig
```

## 2. Build Peerplays

```
cd $HOME
git clone https://github.com/peerplays-network/peerplays
cd peerplays
git checkout 1.5.18 # --> replace with most recent tag
git submodule update --init --recursive

# If you want to build Mainnet node
cmake -DCMAKE_BUILD_TYPE=Release

# If you want to build Testnet node
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_PEERPLAYS_TESTNET=1

# the following command will install the executable files under /usr/local
sudo make install

# the following isn't required if you ran the "sudo make install" command above.
# If you prefer, the "make -j$(nproc)" command will install the
# programs under $HOME/peerplays/programs
# Update the -j flag depending on your current system specs:
# Recommended 4GB of RAM per 1 CPU core
# make -j2 for 8GB RAM
# make -j4 for 16GB RAM
# make -j8 for 32GB RAM
make -j$(nproc)
```

### 2.1. Starting the Peerplays Witness Node

If we have installed the blockchain following the above steps, the node can be started as follows:

```
witness_node
```

Launching the Witness for the first time creates the directories which contain the configuration files.

{% hint style="warning" %}
Next, stop the Witness node before continuing (`Ctrl + c`).
{% endhint %}

## 3. Update the config.ini File

We need to set the endpoint and seed-node addresses so we can access the cli\_wallet and download all the initial blocks from the chain. Within the config.ini file, locate the p2p-endpoint, rpc-endpoint, and seed-node settings and enter the following addresses.

```
nano $HOME/witness_node_data_dir/config.ini

p2p-endpoint = 0.0.0.0:9777
rpc-endpoint = 127.0.0.1:8090
seed-node = 213.184.255.234:59500
```

Save the changes and start the node back up.

```
witness_node
```

## 4. Create a Peerplays Account

We'll need an account as the basis of creating a new Witness. The easiest way to do that is to use the [Peerplays DEX](https://market.peerplays.com/) & this doesn't need download of an application.

### 4.1. Download the Peerplays Core GUI Wallet to Make an Account

[Peerplays Core GUI](https://github.com/peerplays-network/peerplays-core-gui/releases)

1. Install, open, and create an account. It's pretty self-explanatory. :slight\_smile:&#x20;
2. Wait for your node to sync the blocks (about 7.3GB at the time of writing). We need to do this before we can use the CLI wallet.
3. From this point on, please note the results of the following commands as you'll need them later.

### 4.2. Use the cli\_wallet to set a password and unlock the wallet

In a **new** command line window, we can access the cli\_wallet program after all the blocks have been downloaded from the chain. Note that "your-password-here" is a password that you're creating for the cli\_wallet and doesn't necessarily have to be the password you used in the GUI wallet earlier.

```
cli_wallet
set_password your-password-here
unlock your-password-here
```

The CLI wallet will show `unlocked >>>` when successfully unlocked.

{% hint style="info" %}
A list of CLI wallet commands is available here: [https://devs.peerplays.tech/api-reference/wallet-api/wallet-calls](https://devs.peerplays.tech/api-reference/wallet-api/wallet-calls)
{% endhint %}

### 4.3. Generate OWNER private keys for the cli\_wallet and import them

This will return an array with your owner key in the form of `["PPYxxx", "xxxx"]`. Note that the "created-username" and "created-password" used here are the username and password from the GUI wallet!

```
get_private_key_from_password created-username owner created-password
```

The second value in the returned array is the private key of your owner key. Now we'll import that into the cli\_wallet.

```
import_key "created-username" SECONDVALUEFROMLASTCOMMAND
```

### 4.4. Generate ACTIVE private keys for the cli\_wallet and import them

Once again, this will return an array with your active key in the form of `["PPYxxx", "xxxx"]`. Note that the "created-username" and "created-password" used here are the username and password from the GUI wallet!

```
get_private_key_from_password created-username active created-password
```

The second value in the returned array is the private key of your active key. Now we'll import that into the cli\_wallet.

```
import_key "created-username" SECONDVALUEFROMLASTCOMMAND
```

{% hint style="info" %}
The keys that begin with "PPY" are the public keys.
{% endhint %}

### 4.5. Upgrade to lifetime membership

You will need some PPY for this command to succeed. The account must have lifetime membership status to create a new Witness.

```
upgrade_account created-username true
```

### 4.6. Create yourself as a Witness

The URL in this command is your own URL which should point to a page which describes who you are and why you want to become a Peerplays witness. Note your block signing key after you enter this command.

This command will require some PPY as well.

```
create_witness created-username "https://your-url-to-witness-proposal" true
```

The above command will also return _YOURBLOCKSIGNINGKEY_

### 4.7. Gather your Witness account info

First we'll get the private key for your block\_signing\_key.

```
get_private_key YOURBLOCKSIGNINGKEY
```

Then dump your keys to check and compare. One of the returned values from the following command should match your block\_signing\_key.

```
dump_private_keys
```

Last we'll get your witness ID.

```
get_witness created-username
```

## 5. Edit config.ini to include your Witness ID and your private key pair

Exit the cli\_wallet with the `quit` command. Back in the first command line window, we'll stop the node (`Ctrl + c`) and edit the `config.ini` file once again.

```
nano $HOME/witness_node_data_dir/config.ini

witness-id = "your_witness_id"
private-key = ["block_signing_key", "private_key_for_your_block_signing_key"]
```

## 6. Start the node and vote for yourself

```
witness_node
```

We need to wait for the node to sync the blocks to use the cli\_wallet. After the sync, you can vote for yourself. In the second command line window:

```
cli_wallet
unlock your-password-here
vote_for_witness created-username created-username true true
```

Now you can check your votes to verify it worked.

```
get_witness your_witness_account
```

{% hint style="success" %}
Success! You built a Peerplays witness node from the latest source code and now it's up and running. :tada:&#x20;
{% endhint %}

## 7. What's Next?

### 7.1. Auto-starting your node

Up until this point we have been running the node in the foreground which is fragile and inconvenient. So let's start the node as a service when the system boots up instead.

{% content-ref url="../../the-basics/auto-starting-a-node.md" %}
[auto-starting-a-node.md](../../the-basics/auto-starting-a-node.md)
{% endcontent-ref %}

### 7.2. Creating a backup node

After that, it would be smart to create a backup server to enable you to make software updates, troubleshoot issues with the node, and otherwise take your node offline without causing service outages.

{% content-ref url="../../the-basics/backup-servers.md" %}
[backup-servers.md](../../the-basics/backup-servers.md)
{% endcontent-ref %}

### 7.3. Fire up another node :fire:&#x20;

You've got a Witness node. Now you'll need a BOS node. And since you're in the node making mood, how about a SON too?

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

### 7.4. Enable SSL to encrypt your node traffic

If you have a node that is accessible from the internet (for example, an API or Seed node) it would be wise to enable SSL connections to your node.

{% content-ref url="../../advanced-topics/reverse-proxy-for-enabling-ssl.md" %}
[reverse-proxy-for-enabling-ssl.md](../../advanced-topics/reverse-proxy-for-enabling-ssl.md)
{% endcontent-ref %}

## 8. Glossary

**Witness:** An independent server operator which validates network transactions.

**Witness Node:** Nodes with a closed RPC port. They don't allow external connections. Instead these nodes focus on processing transactions into blocks.
