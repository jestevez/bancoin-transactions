# bancoin-transactions  [![npm version](https://badge.fury.io/js/%40bancoin%2Fbancoin-transactions.svg)](https://badge.fury.io/js/%40bancoin%2Fbancoin-transactions)

[![License][license-image]][license-url] ![Coverage badge gree][coverage-badge-green]

[license-url]: https://opensource.org/licenses/MIT
[license-image]: https://img.shields.io/npm/l/make-coverage-badge.svg
[coverage-badge-green]:https://img.shields.io/badge/Coverage-98.77%25-brightgreen.svg

Using this library you can easily create and sign transactions for Bancoin blockchain.
It also allows you to multi-sign existing transactions or create them without signature at all.

This library is a set of transaction constructing functions:
* [Alias](https://bancoinplatform.github.io/bancoin-transactions/globals.html#alias)
* [Issue](https://bancoinplatform.github.io/bancoin-transactions/globals.html#issue)
* [Reissue](https://bancoinplatform.github.io/bancoin-transactions/globals.html#reissue)
* [Burn](https://bancoinplatform.github.io/bancoin-transactions/globals.html#burn)
* [Lease](https://bancoinplatform.github.io/bancoin-transactions/globals.html#lease)
* [Cancel lease](https://bancoinplatform.github.io/bancoin-transactions/globals.html#cancellease)
* [Transfer](https://bancoinplatform.github.io/bancoin-transactions/globals.html#transfer)
* [Mass transfer](https://bancoinplatform.github.io/bancoin-transactions/globals.html#masstransfer)
* [Set script](https://bancoinplatform.github.io/bancoin-transactions/globals.html#setscript)
* [Data](https://bancoinplatform.github.io/bancoin-transactions/globals.html#data)
* [Sponsorship](https://bancoinplatform.github.io/bancoin-transactions/globals.html#sponsorship)
* [Set asset script](https://bancoinplatform.github.io/bancoin-transactions/globals.html#setassetscript)
* [InvokeScript](https://bancoinplatform.github.io/bancoin-transactions/globals.html#invokescript)
* [Order](https://bancoinplatform.github.io/bancoin-transactions/globals.html#order)

Check full documentation on [GitHub Pages](https://bancoinplatform.github.io/bancoin-transactions/index.html).

### Transactions

The idea is really simple - you create transaction and sign it from a minimal set of required params.
If you want to create [Transfer transaction](https://bancoinplatform.github.io/bancoin-transactions/interfaces/itransfertransaction.html) the minimum you need to provide is **amount** and **recipient** as defined in [Transfer params](https://bancoinplatform.github.io/bancoin-transactions/interfaces/itransferparams.html):
```js

const { transfer } = require('@bancoin/bancoin-transactions')
const seed = 'some example seed phrase'
const signedTranserTx = transfer({ 
  amount: 1,
  recipient: '3P6fVra21KmTfWHBdib45iYV6aFduh4WwC2',
  //Timestamp is optional but it was overrided, in case timestamp is not provided it will fallback to Date.now(). You can set any oftional params yourself. go check full docs
  timestamp: 1536917842558 
}, seed)

// or using alias

const signedTranserTx = transfer({ 
  amount: 1,
  recipient: 'alias:W:aliasForMyAddress'
}, seed)
```

Output will be a signed transfer transaction:
```js
{
  id: '8NrUwgKRCMFbUbqXKQAHkGnspmWHEjKUSi5opEC6Havq',
  type: 4,
  version: 2,
  recipient: '3P6fVra21KmTfWHBdib45iYV6aFduh4WwC2',
  attachment: undefined,
  feeAssetId: undefined,
  assetId: undefined,
  amount: 1,
  fee: 100000,
  senderPublicKey: '6nR7CXVV7Zmt9ew11BsNzSvVmuyM5PF6VPbWHW9BHgPq',
  timestamp: 1536917842558,
  proofs: [
    '25kyX6HGjS3rkPTJRj5NVH6LLuZe6SzCzFtoJ8GDkojY9U5oPfVrnwBgrCHXZicfsmLthPUjTrfT9TQL2ciYrPGE'
  ]
}
```

You can also create transaction, but not sign it:
```javascript
const unsignedTransferTx = transfer({ 
  amount: 1,
  recipient: '3P6fVra21KmTfWHBdib45iYV6aFduh4WwC2',
  //senderPublicKey is required if you omit seed
  senderPublicKey: '6nR7CXVV7Zmt9ew11BsNzSvVmuyM5PF6VPbWHW9BHgPq' 
})
```

Now you are able to POST it to Bancoin API or store for future purpose or you can add another signature from other party:
```js
const otherPartySeed = 'other party seed phrase'
const transferSignedWithTwoParties = transfer(signedTranserTx, seed)
```

So now there are two proofs:
```js
{
  id: '8NrUwgKRCMFbUbqXKQAHkGnspmWHEjKUSi5opEC6Havq',
  type: 4,
  version: 2,
  recipient: '3P6fVra21KmTfWHBdib45iYV6aFduh4WwC2',
  attachment: undefined,
  feeAssetId: undefined,
  assetId: undefined,
  amount: 1,
  fee: 100000,
  senderPublicKey: '6nR7CXVV7Zmt9ew11BsNzSvVmuyM5PF6VPbWHW9BHgPq',
  timestamp: 1536917842558,
  proofs: [
    '25kyX6HGjS3rkPTJRj5NVH6LLuZe6SzCzFtoJ8GDkojY9U5oPfVrnwBgrCHXZicfsmLthPUjTrfT9TQL2ciYrPGE',
    'CM9emPzpe6Ram7ZxcYax6s7Hkw6698wXCMPSckveFAS2Yh9vqJpy1X9nL7p4RKgU3UEa8c9RGXfUK6mFFq4dL9z'
  ]
}
```

### Broadcast
To send transaction you can use either node [REST API](https://nodes.bancoinplatform.com/api-docs/index.html#!/transactions/broadcast) or [broadcast](https://bancoinplatform.github.io/bancoin-transactions/globals.html#broadcast) helper function:
```javascript
const {broadcast} =  require('@bancoin/bancoin-transactions');
const nodeUrl = 'https://nodes.bancoinplatform.com';

broadcast(signedTx, nodeUrl).then(resp => console.log(resp))
```
You can send tx to any bancoin node you like:. E.g.:
* https://testnodes.bancoinnodes.com - bancoin TESTNET nodes hosted by Bancoinplatform
* https://nodes.bancoinplatform.com - bancoin MAINNET nodes hosted by Bancoinplatform
#### Important!!!
Most transactions require chainId as parameter, e.g: [IBurnParams](https://bancoinplatform.github.io/bancoin-transactions/interfaces/iburnparams.html). By default chainId is 'W', which means MAINNET. To make transaction in TESTNET be sure to pass chainId if it is present in params interface and then send it to TESTNET node

