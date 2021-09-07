---
title: CLI Wallet Fundamentals
description: 'From install, to setup, to running.'
---

# CLI Wallet Fundamentals

## 1. Overview

When installing a node, the CLI wallet is crucial for completing the process. It's necessary for generating public-private key-pairs for security, block signing, and managing a backup node. It's also needed for creating a Witness or SON account. This guide will cover these topics and list all the most common functions that node operators use.

## 2. CLI Wallet Fundamentals

### 2.1. So... What is a CLI wallet?

**CLI** stands for "Command Line Interface" which means that the program uses the text-based command line window to take user input and show its output.

The Peerplays CLI wallet is a program \(named `cli_wallet`\) that is installed along with the node software when installing a Peerplays node on a server. It's used as a way to store your Peerplays account keys locally, and as a way to interact with the Peerplays chain for account and asset related transactions. You can also use the CLI wallet to lookup information from the chain.

### 2.2. CLI wallet security

The wallet is encrypted with a password of your choosing. Additionally it stores all keys locally, never exposing your keys to anyone as it signs transactions locally before transmitting them to the connected node. The node then broadcasts the signed transactions to the network.

The wallet creates a local wallet.json file that contains the encrypted private keys required to access the funds in your account.

### 2.3. CLI wallet prerequisites

#### 2.3.1. Installation

If you have installed a Peerplays witness, API, seed, or SON node you already have the CLI wallet installed.

#### 2.3.2. Connections

The CLI wallet requires a connection to a running node to reach the blockchain. You can run the wallet using your own node or another node which allows external connections. Either way, the node needs to be synced with the chain.

### 2.4. Running the CLI wallet

#### 2.4.1. Using your own node

Once your node has synced with the blockchain, you can simply run the program:

{% tabs %}
{% tab title="Manual or GitLab Installed Nodes" %}
```text
cli_wallet
```
{% endtab %}

{% tab title="Docker Installed Nodes" %}
```text
cd /home/ubuntu/peerplays-docker
sudo ./run.sh wallet
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Since the node must be running, you will either have to open a new command line window or run the node in the background to run the CLI wallet.
{% endhint %}

#### 2.4.2. Using an external node

You can choose to connect to someone else's running and synced node. In that case you can specify the connection as a program parameter:

{% tabs %}
{% tab title="Manual or GitLab Installed Nodes" %}
```text
cli_wallet -s <Websocket Address>
```
{% endtab %}

{% tab title="Docker Installed Nodes" %}
```text
cd /home/ubuntu/peerplays-docker
sudo ./run.sh remote_wallet <Websocket Address>
```
{% endtab %}
{% endtabs %}

The `<Websocket Address>` in the code above must be replaced with the address of some public node. The address will use the websocket or secure websocket protocol \(`ws://` or `wss://` respectively\). Some node operators may have mapped their websocket address to a more friendly looking domain name which can be used here as well.

{% hint style="info" %}
It is completely safe to use the CLI wallet with an external node because your private keys are never sent to the remote server. 👍
{% endhint %}

### 2.5. Setting the password

If you have started the CLI wallet successfully, you will receive the `new >>>` prompt. At this point you'll be asked to set a password. Here's what you'll see:

```text
Please use the set_password method to initialize a new wallet before continuing
new >>>
```

Type `set_password` followed by a password of your choice and hit enter. It will look like this:

```text
new >>> set_password supersecretpassword
```

If the password was saved successfully, the prompt will change to `locked >>>`. At this point, the CLI wallet is locked and nobody can access it without the password you just set.

{% hint style="danger" %}
Be sure to remember / back up / save / write down your password \(securely of course\) because it **can't be recovered** if you lose it.
{% endhint %}

### 2.5. Unlocking the wallet

Then to unlock your wallet, you'll use the `unlock` command with your password and hit enter.

```text
locked >>> unlock supersecretpassword
```

If successful, the prompt will now read `unlocked >>>`. Your wallet is now unlocked and ready for use.

### 2.6. Locking the wallet

After the CLI wallet has been unlocked, if there are any funds in the wallet, they are accessible. In general, `lock` the wallet and only `unlock` when it’s needed.

To lock it, type `lock` and hit enter.

```text
unlocked >>> lock
```

The prompt will return to `locked >>>`.

### 2.7. Reset the password

If the current password needs to be changed, unlock the wallet and use `set_password` to do so.

Type `set_password` and the new password, then hit enter.

```text
unlocked >>> set_password mynewsupersecretpw
```

### 2.8. Getting help

You can get more detailed information by issuing the `gethelp` command. Detailed explanations for most calls are available. For example:

```text
unlocked >>> gethelp "list_account_balances"
```

You can also use the `help` command to get a list of all commands supported by the wallet. Note that you can use the `help` and `gethelp` commands even if the wallet is locked!

```text
unlocked >>> help
```

