# Truffle with Typescript

Like many projects in the space, we wanted to use typescript to write our libraries and applications. We ran into a few problems understanding how to set typescript up with truffle and getting the two to play well together.

The main problem we ran into was how to ensure that the compiled truffle artifacts are available to the compiled typescript -&gt; javascript code, as the auto-magic behaviour that worked with a pure javascript setup stopped working when we moved to typescript.

## Overall strategy

We're currently following this approach to allow us to run tests against deployed contracts in our react/truffle/typescript [application](https://github.com/magmo/rps-poc):

1. Compile the smart contracts into artifacts
2. Copy artifacts into `lib/artifacts`
3. Compile typescript from `src` into `lib`
4. Run the tests inside the `lib` directory

## Current progress / still todo

At the time of writing, we have a basic test setup running.

We're still looking for a good development workflow. When running the app in development, webpackDevServer takes care of the typescript compilation / updating \(as far as I can tell\), so copying the artifacts to the `lib` folder doesn't help. We \(maybe?\) need to figure out how to copy the artifacts into whatever virtual folder webpackDevServer compiles the typescript into.

## Implementation notes

### Handling truffle.js

When Truffle runs in looks for a `truffle.js` file \(or `truffle-config.js`\) in the folder where it was run. When we renamed this to `truffle.ts`, which then compiled to `lib/truffle.js`, Truffle couldn't find it anymore.

Our solution to this was to run the the `truffle` command within the `lib` directory instead of the root directory. For example, when we `npm test` it executes the following command:

```text
npm run build && (cd lib; truffle test)
```

### Handling migrations

Truffle migrations look something like:

```javascript
// migrations/1_initial_migration.js
var Migrations = artifacts.require("./Migrations.sol");

module.exports = function(deployer) {
  deployer.deploy(Migrations);
};
```

To keep compatibility with Truffle we need to use the `module.exports = foo` syntax instead of using `export default foo`.

This is due to typescript transpiling the `export default foo` to `exports.default = foo`. When Typescript imports a default member it get transpiled to `var foo = require('foo').default`. However since Truffle is not using typescript it imports modules with `var foo = require('foo')` which will fail. This forces us to use the `module.exports` syntax.

For further details see [https://github.com/Microsoft/TypeScript/issues/2719](https://github.com/Microsoft/TypeScript/issues/2719)

### Run with ganache

We're using [a test script from OpenZepplin](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/scripts/test.sh) to run our tests against ganache \(a test local blockchain\). This provides us with some hard-coded accounts with high enough balances to run anything we want to run.

We became aware of this approach via the counterfactual team's [run with ganache repo](https://github.com/counterfactual/run-with-ganache).

## Resources

* [0xproject setup](https://github.com/0xProject/0x-monorepo/blob/development/packages/metacoin/package.json)
  * The 0xproject seem to have their own setup and don't use truffle for testing
  * They do use ganache though
  * They build everything into the \(non-committed\) `lib` directory and run the tests in there

