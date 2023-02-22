# Introduction to Faucet

## What is a Faucet?

A faucet is an API service and associated blockchain account, controlled by an automated script, that listens for **new account creation** requests. As a response, the faucet will register a new account on the chain for the recipient. In some cases, the faucet might also send a small amount of token to the new account to cover the transaction fees or other needs.

## What is the necessity of a Faucet?

* A Faucet acts as a platform to allow any User to self-create an account even before they have funds to pay the fees for account creation.&#x20;
* A Faucet is significant in the process of onboarding new Users to the Peerplays blockchain and other related chains (**Example:** Testnet Chains).

## Where does Faucet fit into the Peerplays infrastructure?

Any wallet or blockchain-connected application or service that offers its users free account registration will need to rely on a faucet.  In order to request new account creation, the wallet or other blockchain-enabled application communicates with the Faucet API. The Faucet runs as an **API endpoint** to connect with the application. As the User's client software handles the Faucet, there is no necessity for the user to directly connect with Faucet.

## Who runs Faucet?

1. Anyone can run Faucet.
2. PBSA runs faucet as public service to facilitate on-boarding requests.
3. Private parties executing private business logic on-chain, who might want to run their own faucet exclusive to their users.

## How to Install Faucet?

PBSA maintains the Faucet installation guide. Click the below link to learn the steps in detail,\
[https://gitlab.com/PBSA/tools-libs/faucet](https://gitlab.com/PBSA/tools-libs/faucet)

## What are the features of Faucet?

* Direct account creation
* Fund transfer
* Send PPY/token for account creation
