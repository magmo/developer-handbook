# Debugging solidity

When we first started working with solidity, we didn't know there were debugging tools
available. We were basically trying to debug by commenting out lines and occasionally
trying to emit debugging events, all of which was incredibly painful. Don't do this - use
a debugger!

## Using the truffle debugger

TODO: These instructions come from watching a workshop. Check this works in practice(!) and/or update instructions.

It seems that to use the truffle debugger you have to grab the transaction you want to debug
from your test (or wherever). You then fire up the truffle console and paste in the
transaction to step through it.

Use `it.only` to filter down to one test, then grab and log the transaction you want to debug
```js
// in your test file, grab the problematic transaction
let { tx: setTx } = await ….
console.log(tx)
```

I *think* you then do `truffle develop` to start out the truffle console.

And then `debug {tx}` to step through the transaction.

## References

[Debugging your contracts | Truffle Suite](https://truffleframework.com/docs/getting_started/debugging)
