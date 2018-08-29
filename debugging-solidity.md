# Debugging solidity

When we first started working with solidity, we didn't know there were debugging tools
available. We were basically trying to debug by commenting out lines and occasionally
trying to emit debugging events, all of which was incredibly painful. Don't do this - use
a debugger!

## Using the truffle debugger

To use the truffle debugger, you'll want a long-running local network.

### `truffle develop`

The simplest way to achieve this is through `truffle develop`,
1. This will start a local development network at `localhost:9545`, or attach to one if already running on this port.
It will then open a node console, in which you can run javascript, with a web3 instance made available to you, connected to this network.
Call this console `RUN`.
2. If you go this way, you should run `truffle develop --log` in another terminal window, which will attach to the network started in the previous step.
Call this console `LOG`.
3. You can then run eg. `migrate --reset` (or just `migrate`) in console `RUN`.
In console `LOG`, you'll see a list of ethereum operations.
These are called anytime you run `truffle migrate` from the command line.
4. You can then run eg
```
SomeMigratedContract.deployed() // gives you a Web3 contract, attached to the local network
SomeMigratedContract.deployed().then((instance) => {console.log( instance.someContractFunction(*args))}) // I don't think the truffle console deals well with linebreaks
```
5. In console `LOG`, you'll see  something like
```
  develop:testrpc eth_sendTransaction +21ms
  develop:testrpc  +63ms
  develop:testrpc   Transaction: 0x250394cde17cb4d29098e3e8f2d7bb97de1990c549644aff746d4c229f56a248 +0ms <-- grab this
  develop:testrpc   Contract created: 0x82d50ad3c1091866e258fd0f1a7cc9674609d254 +1ms
  develop:testrpc   Gas usage: 3312354 +0ms
  develop:testrpc   Block Number: 13 +1ms
  develop:testrpc   Block Time: Tue Aug 28 2018 15:58:18 GMT-0600 (Mountain Daylight Time) +0ms
  develop:testrpc  +0ms
  ```
6. Grab the transaction hash, and in console `RUN`:
```
truffle(develop)> debug 0x250394cde17cb4d29098e3e8f2d7bb97de1990c549644aff746d4c229f56a248
```
This will put you into a debug session.
You're on your own now.
Good luck.

### `truffle debug`

If you wish to debug a transaction from your development network, you can run
```
truffle debug $TX_HASH
```
directly from the command line.
You have to run this from a folder containing `truffle.js`, and it will look for the specified transaction on the network labeled `development` in this configuration file.

You might wish to do this, for instance, if you're running some tests against a long-running development network.
I this case, use `it.only` to filter down to one test, then grab and log the transaction you want to debug
```js
// in your test file, grab the problematic transaction
let { tx: setTx } = await ….
console.log(tx)
```
Then run `truffle debug $TX_HASH` from the command line.

## Notes
You can only debug _transactions_, which means you can't debug external calls to pure or constant functions.
As a workaround, you can mark these functions as not pure.
This will cause a compiler error in solidity 0.5.x, but we're still on 0.4.x, where it is just a compiler warning.

An alternative method:

>@andrewgordstewart you can't debug eth_call, but you can use truffle-contract to send calls as transactions, doing something like myContract.myViewFunction.sendTransaction(...args)

>the other option, @andrewgordstewart, is to petition the Ethereum community to standardize a debug_traceCall JSON-RPC method. whatever's easier.

## References

[Debugging your contracts | Truffle Suite](https://truffleframework.com/docs/getting_started/debugging)

[Ethereum Development On Windows — Web3, and Truffle Debugging](https://medium.com/edgefund/ethereum-development-on-windows-web3-and-truffle-debugging-9d47dda77c1e) -- maybe there's something different in windows?
