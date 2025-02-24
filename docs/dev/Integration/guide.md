---
tags:
  - Tutorial
---

# Ergo Platform Blockchain Integration Guide

This guide is designed to assist developers in integrating the Ergo Blockchain Platform. For suggestions on improvements, please contact us at team@ergoplatform.org or join our [`#development` on Discord](https://discord.gg/kj7s7nb).

## Getting Started

Here's a brief overview of the key features of Ergo:

* Transactions in Ergo, similar to Bitcoin, involve multiple *inputs* and *outputs*. Unspent outputs are used once, termed as **single-use entities**. Despite being built from scratch, Ergo's scripts and transaction formats differ from Bitcoin's. For more details, refer to the [Ergo 'Box' model](../data-model/box.md).
* Ergo includes standard scripts, associated with addresses like `P2PK` addresses, similar to Bitcoin. [Learn more about the address scheme here.](/dev/wallet/address)
* An ergo eutxo box stores [registers](registers.md) to hold arbitrary values, such as its native tokens, instead of a single amount (like BTC). Thus, each box contains an ERG amount and may include {tokenid, token amount} pairs, all following the UTXO model.
* Ergo's built-in wallet API is sufficient for most use cases. The API uses a Swagger interface on `127.0.0.1:9053` by default in the mainnet (`9052` on the testnet).
* A comprehensive guide on [setting up a node](install.md) and a dedicated [troubleshooting page](troubleshooting.md) are available.

### Node Wallet

- **Web interface**: Access the web interface at [127.0.0.1:9053/panel](https://127.0.0.1:9053/panel) (`9052` on the testnet). 

The major functionalities include:

* Wallet creation (`/wallet/init`) and mnemonic generation
* Wallet restoration (`/wallet/restore`) from mnemonic
* Wallet unlock (`/wallet/unlock`) for transaction signing
* Wallet lock (`/wallet/lock`)
* Sending a simple payment (`/wallet/payment/send`)
* Checking wallet status (`/wallet/status`)
* Deriving a new key according to EIP-3 (BIP 44 implementation for Ergo) (`/wallet/deriveNextKey`)
* Checking wallet balance (`/wallet/balances`) for all addresses
* Retrieving wallet transactions (`/wallet/transactions`) for all addresses 

### Creating an External Wallet

You can develop your wallet logic externally using one of the available libraries and the block explorer. 

**Please ensure to take into consideration mempool transactions to avoid double-spending.**

Available libraries include:

* [*ergo-appkit*](https://github.com/ergoplatform/ergo-appkit): A complete Java SDK.
* [*ergo-wallet*](https://mvnrepository.com/artifact/org.ergoplatform/ergo-wallet): Developed in Java, the source code is available at the [Ergo node repository](https://github.com/ergoplatform/ergo/tree/master/ergo-wallet).
* [*sigma-rust*](https://github.com/ergoplatform/sigma-rust/): A Rust library with WASM bindings for JavaScript/TypeScript.
* [*ergo-golang*](https://github.com/azhiganov/ergo-golang): This is a Go library, still in early development stages.


### Offline Signing

- A demo for transaction assembly and offline signing using ergo-wallet and Java is provided in [AdressGenerationDemo.java](https://gist.github.com/kushti/c040f244865a451b94df01032c7a3456 )
- Transaction assembly and signing in Rust: [tx_builder.rs](https://github.com/ergoplatform/sigma-rust/blob/d70bea875792c4e383bfdd71754338695bdb37f8/ergo-lib/src/wallet/tx_builder.rs#L552-L592) and [signing.rs](https://github.com/ergoplatform/sigma-rust/blob/d70bea875792c4e383bfdd71754338695bdb37f8/ergo-lib/src/wallet/signing.rs#L133-L161)
- [Transaction assembly and signing in JavaScript](https://github.com/ergoplatform/sigma-rust/blob/d70bea875792c4e383bfdd71754338695bdb37f8/bindings/ergo-lib-wasm/tests/test_transaction.js#L9-L69)


### Address generation

A demo for secret seed and derived addresses generation using ergo-wallet and Java is provided in [AdressGenerationDemo.java](https://gist.github.com/kushti/70dcfa841dfb504721f09c911b0fc53d)


You can use the `/wallet/deriveNextKey` API to generate new addresses in the same wallet.

```bash
curl -X GET "http://localhost:9053/wallet/deriveNextKey" -H  "accept: application/json" -H  "api_key: hello"
```

### Address validation

For an exchange, you can restrict people to only withdraw to P2PK addresses and invalidate any other address. Supporting other types is not recommended. See [address.md] for more information on the types of addresses. 

[ergo-simple-addresses](https://github.com/kushti/ergo-simple-addresses) contains a few zero-dependencies Java-friendly utils for working with addresses.




### Composing transactions outside the node


To get unspent UTXOs for some address, please use the `transactions/boxes/byAddress/unspent` Explorer API method: 

```bash
https://api.ergoplatform.com/transactions/boxes/byAddress/unspent/9gAE5e454UT5s3NB1625u1LynQYPS2XzzBEK4xumvSZdqnXT35M 
```

It would be best if you excluded UTXOs spent in the mempool. Use the`/transactions/unconfirmed/byAddress` Explorer API method for that:

```bash
https://api.ergoplatform.com/transactions/unconfirmed/byAddress/9gAE5e454UT5s3NB1625u1LynQYPS2XzzBEK4xumvSZdqnXT35M
```

### Broadcasting transaction


To broadcast a transaction made outside the node, the easiest way is to serialize it into `JSON`; in Java, it could be like this:

```Java
Json json = JsonCodecsWrapper.ergoLikeTransactionEncoder().apply(tx);
System.out.println(json.toString());
```

And then send this `JSON` via a POST request to the public Explorer. 

```bash
https://api.ergoplatform.com/api/v0/transactions/send*
```

your private Explorer or a node with open API (`POST` to `http://{node_ip}:9053/transactions` )



## Infrastructure

### Node

There is a public node available at [213.239.193.208:9053](http://213.239.193.208:9053). If a redundancy is needed please contact us at team@ergoplatform.org or via the established group chat for access. A dynamic list of available public nodes can be found at [api.tokenjay.app/peers/list](https://api.tokenjay.app/peers/list)

### Testnet

See [this page](/node/testnet) for information on the public testnets.

### Explorer

There is a public explorer hosted at [explorer.ergoplatform.com](https://explorer.ergoplatform.com/)

You can use [*ergo-bootstrap*](https://github.com/ergoplatform/ergo-bootstrap) to easily install the Explorer backend (and not rely on public ones). 

### GQL 

GraphQL queries allow flexible data fetching, reducing over-fetching and under-fetching. [gql.ergoplatform.com](https://gql.ergoplatform.com) is a GraphQL server on top of Ergo Platform's explorer database schema.

[explore.sigmaspace.io/api/graphql](https://explore.sigmaspace.io/api/graphql) is the most reliable instance ATM

## Troubleshooting

### Dust Collection

Please collect dust from miners' deposits periodically (which creates many small UTXOs).

Ergo is based on the extended-UTXO model. A side-effect of UTXOs is a term coined *dust*. Dust refers to fractional values of ERG and is usually below the protocol fee; miner wallets are prone to becoming *dusty* with the stream of rewards coming into their wallet. All these excess UTXOs can cause a slowdown and ultimately impact your node functionality. **This is important to set up for big exchanges that will receive a lot of mining traffic. You must collect dust aggressively; new dust arrives all the time for miners**

There are some node specific settings


**To solve**

- Get utxos from `/wallet/boxes/unspent` with min number of confirmations
- Get their `ids` and `total sum`
- Get their *binary representations* of utxos via `/utxo/byIdBinary/{boxId}`


Finally, construct the payment transaction like this:
  
```JSON
{
  "requests": [
    {
      "address": "3WwbzW6u8hKWBcL1W7kNVMr25s2UHfSBnYtwSHvrRQt7DdPuoXrt",
      "value": 10000000000
    }
  ],
  "fee": 1000000,
  "inputsRaw": [
    "utxo1", "utxo2"
  ],
  "dataInputsRaw": [
  ]
} 
```

And post to `/wallet/transaction/send`.

Set value and fee accordingly, `value + fee = total sum of utxos`

You can query with specific parameters like this, 

```bash
curl -X GET "http://127.0.0.1:9053/wallet/boxes/unspent?minConfirmations=10&minInclusionHeight=0" -H  "accept: application/json" -H  "api_key: hello"
```

Another (and simple) way to collect is to set 

```conf
ergo {
 wallet {
   maxInputs = 300 
   optimalInputs = 100
 }
}
```

And send `1 ERG` to the change address; however, the node will attach 100 dust inputs (so send any large amounts of change to the change address as well)

#### Failed to sign boxes

This error can occur due to too many inputs collected in a transaction for dusty wallets. 

```bash
Failed to sign boxes due to Estimated execution cost 1001580 exceeds the limit 1000000: Vector(ErgoBox(0275eb3a125bc02fe997cb98c0de8131bd9b2e4617110d
```



### Native Assets

In the case of large airdrops, many users mistakenly end up putting exchange addresses to receive native assets. An *auto-burn* method will be in future versions of the node to reduce the manual component of this task. See this [Issue](https://github.com/ergoplatform/ergo/issues/1604) for more information.

Send the following request via `/wallet/payment/send`, replacing the `tokenId` with the IDs from the tokens spamming your wallets. 

```JSON
[
  {
    "address": "4MQyMKvMbnCJG3aJ",
    "value": 100000000,
    "assets": [
      {"tokenId":"e55adbda4e42f2bd21b1cb9498c105ff3bc1069012942d6158412f55759369c3","amount":1},
      {"tokenId":"6dd3e0ac4edd9702094aa4e3cad7c0c73d5437292c11805adf5d5068312748b4","amount":1}
    ]
  }
]
```

#### Tokens with Value

- SigUSD ($1): `472c3d4ecaa08fb7392ff041ee2e6af75f4a558810a74b28600549d5392810e8` 
- SigRSV
- ergopad
- NETA
- LunaDog
- Erdoge

### FAQ

**Are P2S and P2SH two address formats for the same script?**

It can be. So you can create a p2s and a p2sh address from the same script. In p2s, the script is serialized into the Address; in p2sh, you only have a hash of that serialized script.

**Will there be any problems with supporting addresses other than P2PK?**

No problem if the user knows what he is doing, which is not true in many cases. It also adds more complexity. Especially for p2s Addresses since you cannot validate the input size in your form. 

**ergoTree<->address related conversions**

When you use appkit, Address.create() takes the address string. You can get the ergotree from the resulting object.

**Some transactions don't seem to pay fees?**

Fees are not part of the core protocol, but if you miss them, the transaction will not be propagated around the network by default.

**What is the generation algorithm of boxid?**

It is a hash of the box contents.

[See the code in AppKit](https://github.com/ergoplatform/ergo-appkit/blob/9e19c13d82966eaee59433d16c4fb987bea363a7/lib-impl/src/main/java/org/ergoplatform/appkit/impl/OutBoxBuilderImpl.scala#L66)

Bytes are unique as the box contains
- The `id` of the parent transaction, 
- the output position in the transaction
- a unique transaction id. 


