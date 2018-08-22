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

