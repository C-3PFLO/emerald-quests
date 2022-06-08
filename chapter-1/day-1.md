# Chapter-1 : Day-1

*Response to the questions posted here: https://github.com/emerald-dao/beginner-cadence-course/tree/main/chapter1.0/day1#quests*

## Question 1

> Explain what the Blockchain is in your own words.

A blockchain is a decentralized [^decentralized], highly-available [^available] and permission-less [^permissionless] server with an immutable database.

[^decentralized]: Control of traditional web2 systems are typically centralized into one company, but control over a blockchain is decentralized across it's node maintainers.  Theoretically anyone can become a node maintainer (although proof of stake blockchains like Flow can require a staking threshold prohibitive to many).  A blockchain is as decentralized as it's node maintainers.

[^available]: In theory blockchains are publicly available at all times.  However, like all systems they are subject to planned and unplanned downtime where the system may be partially or completely unavailable.

[^permissionless]: Permissionless in that anyone can execute a transaction or script or deploy a new contract.  However, blockchain resources can require permission to modify (ex: transferring an NFT must be signed by the private key of the holder) secured by cryptography, and some blockchains like Flow have guardrails limiting who can deploy a contract (as the blockchain is bootstrapped).

## Question 2

> Explain what a Smart Contract is.

A smart contract is a blockchain-based (programming) class.  They often have a set of functions that are executed by the blockchain and state that is persisted by the blockchain (as is the contract code itself), making them well suited as the back-end for a decentralized application.  The supported code varies by blockchain.

## Question 3

> Explain the difference between a script and a transaction.

A transaction is a function call (or sequence of calls) that modifies resources persisted on the blockchain.  Transactions are paid, in that node maintainers collect a fee for executing the transaction.

A script is also a blockchain function but is read-only and does not require fees to execute.
