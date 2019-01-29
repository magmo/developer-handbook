---
description: Explanation of the pre_deployed_artifacts
---

# Pre-deploying contracts to testnets

Normally when building the application against a local development network  we deploy the contracts at compile time. However when targeting a test network this isn't very practical as deployment takes a long time.

To speed up the developer workflow, we have deployed the contracts in advance to the following test networks using truffle:

* Ropsten
* Kovan
* Rinkeby

\(TODO: ttt contracts have not been deployed to these networks yet\)

The truffle artifacts created from the deployments are located here: 

`/contracts/pre_deployed_artifacts/` \(TODO: Rename artifacts to pre\_deployed\_artifacts\)

When the `TARGET_NETWORK` environment variable is set to a test network, webpack will use the predeployed artifacts instead of trying to deploy the contracts at compile time:

{% code-tabs %}
{% code-tabs-item title="webpack.config.prod.js" %}
```javascript
    new webpack.NormalModuleReplacementPlugin(
      /.*\/build\/contracts\/.*\.json/,
      function (resource) {
        if (process.env.TARGET_NETWORK !== 'development') {
          resource.request = resource.request.replace(/.*\/build\/contracts/, paths.appContractArtifacts);
        }
      }),
```
{% endcode-tabs-item %}
{% endcode-tabs %}

If we are targeting a development network, this snippet will use the artifacts that truffle just created at compile time. Otherwise it replaces all references to `/build/contracts/*.json` files  to the predeployed artifacts in `/contract/pre_deployed_artifacts` folder. 

### Deploying Contracts to a Test Network

There's a few steps to follow to deploy and create the pre-deployed artifacts:

1. Delete the `/build/contract` folder. This removes any existing artifacts that might be there and contain references to development network.
2. Set `TARGET_NETWORK` in your `.env` file to your desired test network.
3. Run `yarn deployContracts`
4. Repeat step 2 and 3 for any other test networks you want to pre deploy to.
5. Copy the `.json` files from `/build/contracts` to the `/contract/pre_deployed_artifacts` folder.
6. Check in the updated artifacts to source control.

