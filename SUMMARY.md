# Table of contents

* [Infrastructure Documentation](README.md)

## The Basics

* [Peerplays Node Types](the-basics/peerplays-node-types.md)
* [Hardware Requirements](the-basics/hardware-requirements.md)
* [Obtaining private keys for cli\_wallet](the-basics/obtaining-private-keys-for-cli\_wallet.md)
* [Using the CLI Wallet](the-basics/using-the-cli-wallet/README.md)
  * [CLI Wallet Fundamentals](the-basics/using-the-cli-wallet/cli-wallet-fundamentals.md)
  * [CLI Commands for All Nodes](the-basics/using-the-cli-wallet/cli-commands-for-all-nodes.md)
  * [CLI Commands for Witnesses](the-basics/using-the-cli-wallet/cli-commands-for-witnesses.md)
  * [CLI Commands for SONs](the-basics/using-the-cli-wallet/cli-commands-for-sons.md)
* [Auto-Starting a Node](the-basics/auto-starting-a-node.md)
* [Backup Servers](the-basics/backup-servers.md)
* [Obtaining Your First Tokens](the-basics/obtaining-your-first-tokens.md)
* [Updating a Witness Node](the-basics/updating-a-witness-node.md)

## Advanced Topics

* [Private Testnets](advanced-topics/private-testnets/README.md)
  * [Peerplays QA environment](advanced-topics/private-testnets/peerplays-qa-environment.md)
  * [Private Testnets - Manual Install](advanced-topics/private-testnets/private-testnets-manual-install.md)
* [Reverse Proxy for Enabling SSL](advanced-topics/reverse-proxy-for-enabling-ssl.md)
* [Enabling Elasticsearch on a Node](advanced-topics/enabling-elasticsearch-on-a-node.md)
* [Introduction to Faucet](advanced-topics/introduction-to-faucet.md)
* [Configuring Witness Node as Delayed Node](advanced-topics/configuring-witness-node-as-delayed-node.md)

## Witnesses

* [Installation Guides](witnesses/installation-guides/README.md)
  * [Manual Install (Ubuntu 18.04)](witnesses/installation-guides/manual-install.md)
  * [Manual Install (Ubuntu 20.04)](witnesses/installation-guides/manual-install-1.md)
  * [Docker Install](witnesses/installation-guides/docker-install.md)
  * [GitLab Artifact Install](witnesses/installation-guides/gitlab-artifact-install.md)
  * [Peerplays API nodes](witnesses/installation-guides/peerplays-api-nodes.md)

## Sidechain Operator Nodes (SONs)

* [Installation Guides](sidechain-operator-nodes-sons/installation-guides/README.md)
  * [Manual Install](sidechain-operator-nodes-sons/installation-guides/manual-install.md)
  * [Docker Install](sidechain-operator-nodes-sons/installation-guides/docker-install.md)
  * [SON Configuration](sidechain-operator-nodes-sons/installation-guides/son-configuration.md)
  * [Bitcoin-SONs Sanity Checks](sidechain-operator-nodes-sons/installation-guides/bitcoin-sons-sanity-checks.md)

## Bookie Oracle Suite (BOS)

* [Introduction to BOS](bookie-oracle-suite-bos/introduction-to-bos.md)
* [BOS Installation](bookie-oracle-suite-bos/installation-guides/README.md)
  * [Installing MongoDB](bookie-oracle-suite-bos/installation-guides/untitled.md)
  * [Installing Redis](bookie-oracle-suite-bos/installation-guides/installing-redis.md)
  * [Configuration of bos-auto](bookie-oracle-suite-bos/installation-guides/configuration-of-bos-auto.md)
  * [Spinning Up bos-auto](bookie-oracle-suite-bos/installation-guides/spinning-up-bos-auto.md)
* [BookieSports](bookie-oracle-suite-bos/bookiesports/README.md)
  * [Installing Bookiesports](bookie-oracle-suite-bos/bookiesports/installing-bookiesports.md)
  * [Synchronizing BOS with BookieSports](bookie-oracle-suite-bos/bookiesports/synchronizing-bos-with-bookiesports.md)
  * [BookieSports Module Contents](bookie-oracle-suite-bos/bookiesports/bookiesports-module-contents/README.md)
    * [Sub Modules](bookie-oracle-suite-bos/bookiesports/bookiesports-module-contents/sub-modules.md)
  * [Schema](bookie-oracle-suite-bos/bookiesports/schema.md)
  * [Naming Scheme](bookie-oracle-suite-bos/bookiesports/naming-scheme.md)
* [Manual Intervention Tool (MINT)](bookie-oracle-suite-bos/manual-intervention-tool-mint/README.md)
  * [Installing MINT](bookie-oracle-suite-bos/manual-intervention-tool-mint/installing-mint.md)

## DATA PROXIES

* [Introduction to Data Proxies](data-proxies/introduction-to-data-proxies.md)
* [How Data Proxies Work](data-proxies/how-data-proxies-work.md)
* [Data Proxy Set Up](data-proxies/data-proxy-set-up.md)

## COUCH POTATO

* [Introduction](couch-potato/introduction.md)
* [Installation](couch-potato/installation.md)
* [Functional Requirements](couch-potato/functional-requirements/README.md)
  * [Flow Diagrams](couch-potato/functional-requirements/flow-diagrams.md)
  * [Home Page](couch-potato/functional-requirements/home-page.md)
  * [Create Account](couch-potato/functional-requirements/create-account.md)
  * [Dashboard](couch-potato/functional-requirements/dashboard/README.md)
    * [Header](couch-potato/functional-requirements/dashboard/header.md)
    * [Sports Tabs](couch-potato/functional-requirements/dashboard/sports-tabs.md)
    * [League Tabs](couch-potato/functional-requirements/dashboard/league-tabs.md)
    * [Calendar](couch-potato/functional-requirements/dashboard/calendar.md)
    * [Notifications](couch-potato/functional-requirements/dashboard/notifications.md)
    * [Replay](couch-potato/functional-requirements/dashboard/replay.md)
    * [Account Menu](couch-potato/functional-requirements/dashboard/account-menu.md)
  * [Game Selector](couch-potato/functional-requirements/game-selector.md)
  * [Change Password](couch-potato/functional-requirements/change-password.md)
* [Help](couch-potato/help/README.md)
  * [User Guide](couch-potato/help/user-guide/README.md)
    * [Introduction](couch-potato/help/user-guide/introduction.md)
    * [Home Page](couch-potato/help/user-guide/home-page.md)
    * [Creating an Account](couch-potato/help/user-guide/creating-an-account.md)
    * [Dashboard](couch-potato/help/user-guide/dashboard/README.md)
      * [Replay](couch-potato/help/user-guide/dashboard/replay.md)
      * [Account Menu](couch-potato/help/user-guide/dashboard/account-menu/README.md)
        * [Change Password](couch-potato/help/user-guide/dashboard/account-menu/change-password.md)
    * [Game Selector](couch-potato/help/user-guide/game-selector.md)
* [Database](couch-potato/database/README.md)
  * [Schema](couch-potato/database/schema.md)
  * [Objects](couch-potato/database/objects/README.md)
    * [Tables](couch-potato/database/objects/tables.md)
    * [Views](couch-potato/database/objects/views.md)
* [API](couch-potato/api/README.md)
  * [Using the API](couch-potato/api/using-the-api.md)
  * [API Reference](couch-potato/api/api-reference/README.md)
    * [Objects](couch-potato/api/api-reference/objects.md)
    * [Error Codes](couch-potato/api/api-reference/error-codes.md)
  * [BOS Schema](couch-potato/api/bos-schema.md)
* [Proxy Payment Considerations](couch-potato/proxy-payment-considerations.md)

## Other Documentation

***

* [Peerplays Home](https://peerplays.com)
* [Community Docs](https://community.peerplays.com)
* [Developer Docs](https://devs.peerplays.com)
