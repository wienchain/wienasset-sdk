# wienasset-sdk
[![Standard - JavaScript Style Guide](https://cdn.rawgit.com/feross/standard/master/badge.svg)](https://github.com/feross/standard)

Easy to use SDK for issuing and transferring digital assets using [Wienasset protocol](https://github.com/Wienasset/Wienasset-Protocol-Specification) on top of blockchain technology.
Coupled with state-of-the-art [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) & [BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) hierarchical deterministic wallet to hold your assets.

## Usage

```js
var WienAsset = require('wienassets-sdk')
const config = {
  network: 'testnet',
  wienAssetHost: 'https://explorer.wienchain.com',
  blockExplorerHost: 'https://explorer.wienchain.com',
  metadataServerHost: 'https://asset.wienchain.com/metadata',
  // Uncomment this line and replace with your mnemonic. A new mnemonic will be generated
  // mnemonic: ''
};

const wa = new WienAsset(config);
```

The ```config``` object configure the package to integrate mainnet or testnet. The example of the parameters are as below:

| Parameter  | Testnet  | Mainnet  |
| ------------ | ------------ | ------------ |
| network  | testnet  | mainnet  |
| wienAssetHost  | https://<span/>explorer-testnet.wienchain.com  | https://<span/>explorer.wienchain.com  |
| blockExplorerHost  | https://<span/>explorer-testnet.wienchain.com  | https://<span/>explorer.trivechain.com  |
| metadataServerHost  | https://<span/>asset-testnet.wienchain.com/metadata  | https://<span/>asset.wienchain.com/metadata  |
| mnemonic  | -  | -  |

You can generate new mnemonic by removing ```mnemonic``` from the ```config```.
Now, initiate the package.

```js
wa.init(function (err) {
  // WienAsset SDK is now ready
  // Get your mnemonic
  console.log("mnemonic: ", wa.hdwallet.getMnemonic())
  const address = wa.hdwallet.getAddress(0, 0) // Derive address in position 0,0 from mnemonic
  wa.hdwallet.getAddressPrivateKey(address, function(err, privkey) {
    // Derive the Private Key
    console.log("Address: ", address, ", privkey: ", privkey.getFormattedValue());
  })
  
  // Issue the Address
  const watIssuanceParams = {
    amount: 100000000000000, // Amount = 1000000.00000000 (with divisibility)
    divisibility: 8, // The number of decimal (from 0 - 15 only)
    // Metadata will be uploaded to IPFS and the IPFS hash will be stored in the transaction
    metadata: {
        assetName: 'WienAsset Test',
        assetSymbol: 'WAT',
        issuer: 'Wienchain Limited',
        description: "The WienAsset Test (WAT) is used to ensure that the asset is issued correctly",
        urls:[
          {
            name: "website",
            url: "https://wienchain.com",
            mimeType: "text/html",
          },
          {
            name: "icon_large", 
            url:"ipfs://QmcE93H8ejgG9AVFKnkMbRM61b9vsWX6QQyx4B2rvNaaa",
            mimeType:"image/png",
          },{
            name: "icon",
            url:"ipfs://QmeuVseyqhH6gyVRvw9Xtvf6UqA51ZEfRezJAbykwE7vP",
            mimeType:"image/png",
          }
        ]
    },
    lockStatus: true, // boolean
    issueAddress: "Issuance Address", //string
  }

  wa.issueAsset(watIssuanceParams, function (err, body) {
    if (err) return console.error(err)
      console.log("Body: ",body)
  })

  //create a variable of the transaction you want to send
  let send = {
    from: [ "Sender Address", ], //array
    to: [
      {
        address: "Receiver Address", //string
        assetId: "Asset ID", //string
        amount: 100000000, //Amount to send is without divisibility //int
      },
    ], //array object
    coloredChangeAddress: "Address to receive balance of the asset", //string
    financeChangeAddress: 'Address to receive balance of WIEN', //string
    metadata: {
      description: "Any description", //string
      userData: {
        meta: [{
          key: "key", //string
          value: { example: "abc", }, //object
          type: 'object' //string
        }]
      } //object (Optional)
    },
    transmit: false, // default: true. When it is false, it will return an signedTx Hex, else it will broadcast your transction straightaway.
  }
  
  //This will create, sign and/or broadcast your transaction
  wa.sendAsset(send, function (err, body) {
      console.log(JSON.parse(err))
      console.log(body)
  })
})
```

## License

[Apache-2.0](http://www.apache.org/licenses/LICENSE-2.0)
