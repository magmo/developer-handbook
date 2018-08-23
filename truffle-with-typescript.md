# Truffle with Typescript

Like many projects in the space, we wanted to use typescript to write our libraries and
applications. We ran into a few problems understanding how to set typescript up with
truffle and getting the two to play well together.

## Overall strategy

1. Compile the smart contracts into artifacts
2. Copy artifacts into `lib/artifacts`
3. Compile typescript from `src` into `lib`
4. Run the tests inside the `lib` directory

## Resources

* [0xproject setup](https://github.com/0xProject/0x-monorepo/blob/development/packages/metacoin/package.json)
  * The 0xproject seem to have their own setup and don't use truffle for testing
  * They do use ganache though
  * They build everything into the (non-committed) `lib` directory and run the tests in there


## Other gotchas

### Handling truffle.js

When Truffle runs in looks for a `truffle.js` file (or `truffle-config.js`) in the folder
where it was run. When we renamed this to `truffle.ts`, which then compiled to `lib/truffle.js`,
Truffle couldn't find it anymore.

Our solution to this was to run the the `truffle` command within the `lib` directory instead
of the root directory. For example, when we `npm test` it executes the following command:
```
npm run build && (cd lib; truffle test)
```

### Handling migrations

Truffle migrations look something like:
```js
// migrations/1_initial_migration.js
var Migrations = artifacts.require("./Migrations.sol");

module.exports = function(deployer) {
  deployer.deploy(Migrations);
};
```
To keep compatibility with Truffle we need to use the `module.exports = foo` syntax instead of using `export default foo`.

This is due to typescript transpiling the `export default foo` to `exports.default = foo`. When Typescript imports a default member it get transpiled to `var foo = require('foo').default`. However since Truffle is not using typescript it imports modules with `var foo = require('foo')` which will fail. This forces us to use the `module.exports` syntax.

For further details see https://github.com/Microsoft/TypeScript/issues/2719
 



