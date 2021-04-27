# Ethereum nonces

One complication to consider when crafting an ethereum DApp concerns the nature of the nonces that must be included in each transaction. A [nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) is a word (or in this case, a number) that is used only once, and the concept plays an important role in preventing the verification of *fraudulent* transactions, by ensuring that all transactions originating from a given address are unique. 

## What you need to know
Nonces must be consecutive integers, and this seems to limit the rate at which you can send transactions from a single account.

## Why does it work like this?

If I try to re-announce a transaction that (for example) previously benefited my address, it will be marked invalid. A new transaction would need to be generated and signed with a new nonce. 

The simplest way to keep track of nonces is to use a consecutively incrementing integer. That way, if a miner ecounters a transaction with nonce of '2', but can see that a transaction with a greater nonce '3' has already been mined, they will reject it. 

This feature has some interesting implications: for example, transactions must be mined one-at-a-time, and the nonce of an invalid transaction must be re-used before subsequent transactions can be mined. Since there may not be any gaps in the nonces, a single invalid transaction in a batch of transactions may invalidate the whole batch. 




