 <!-- markdownlint-disable no-inline-html -->

# CoolWallet Javascript SDK

<p align="center">
<img src="logo.png" width="500"/>
</p>
<p align="center"> JavaScript SDK to communicate with CoolWallet.</p>
<p align="center">
<a href="https://opensource.org/licenses/apache2.0/"><img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg"/></a>
<a href="https://discordapp.com/channels/640544929680064512/660894930604130304/"><img src="https://img.shields.io/discord/640544929680064512.svg?color=768AD4&label=Discord"/></a> <a href="https://twitter.com/coolwallet"><img src="https://img.shields.io/twitter/follow/coolwallet.svg?label=CoolWallet&style=social"/></a>

</p>

This is the monorepo of all the packages you need to build your own app with CoolWallet hardware wallet.

## Quick Start

### 1. Define your [transport](#Transport) layer

Depending on your platform, you may can choose different [transport](#Transport) object to use in you application.

### 2. Register and setup hardware wallet.

To register you application with the wallet, take a look at the [wallet module](/packages/cws-wallet). This giud you through the process of registeration and seed generation.

### 3. Build your Application

Take a look at all the supported modules at [Coin Apps](#Coin-Apps). Used the keys generated in the previous step to initiate coin instances, then you can sign transactions, message with different coin instances.

## Packages

### Transport

To communicate with CoolWallet device, you need to specify a bluetooth transport.

| Package                                                                           | Version                                                                          | Description                      |
| --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- | -------------------------------- |
| [`@coolwallet/transport-web-ble`](/packages/transport-web-ble)                   | ![version](https://img.shields.io/npm/v/@coolwallet/transport-web-ble)          | Web Bluetooth transport          |
| [`@coolwallet/transport-react-native-ble`](/packages/transport-react-native-ble) | ![version](https://img.shields.io/npm/v/@coolwallet/transport-react-native-ble) | React-Native Bluetooth transport |

### Core

| Package | Version | Description |
| ----- | ------------- | ------ |
| [`@coolwallet/core`](/packages/core) | ![version](https://img.shields.io/npm/v/@coolwallet/core) | APDU commands, default encryptions and keypair generation for other SDKs. |

### Coin Apps

Used to sign transactions of different cryptocurrencies.

| Package                                 | Version                                                   | Description              |
| --------------------------------------- | --------------------------------------------------------- | ------------------------ |
| [`@coolwallet/atom`](/packages/cws-atom) | ![version](https://img.shields.io/npm/v/@coolwallet/atom) | Cosmos Application API |
| [`@coolwallet/bch`](/packages/cws-bch) | ![version](https://img.shields.io/npm/v/@coolwallet/bch) | Bitcoin Cash Application API   |
| [`@coolwallet/bnb`](/packages/cws-bnb) | ![version](https://img.shields.io/npm/v/@coolwallet/bnb) | Binance Application API  |
| [`@coolwallet/bsc`](/packages/cws-bsc) | ![version](https://img.shields.io/npm/v/@coolwallet/bsc) | Binance Smart Chain Application API  |
| [`@coolwallet/btc`](/packages/cws-btc) | ![version](https://img.shields.io/npm/v/@coolwallet/btc) | Bitcoin Application API      |
| [`@coolwallet/dot`](/packages/cws-dot) | ![version](https://img.shields.io/npm/v/@coolwallet/dot) | Polkadot Application API     |
| [`@coolwallet/eth`](/packages/cws-eth) | ![version](https://img.shields.io/npm/v/@coolwallet/eth) | Ethereum Application API     |
| [`@coolwallet/icx`](/packages/cws-icx) | ![version](https://img.shields.io/npm/v/@coolwallet/icx) | Icon Application API     |
| [`@coolwallet/ltc`](/packages/cws-ltc) | ![version](https://img.shields.io/npm/v/@coolwallet/ltc) | LetCoin Application API     |
| [`@coolwallet/trx`](/packages/cws-trx) | ![version](https://img.shields.io/npm/v/@coolwallet/trx) | Tron Application API     |
| [`@coolwallet/xlm`](/packages/cws-xlm) | ![version](https://img.shields.io/npm/v/@coolwallet/xlm) | Stellar Application API     |
| [`@coolwallet/xrp`](/packages/cws-xrp) | ![version](https://img.shields.io/npm/v/@coolwallet/xrp) | Ripple Application API     |
| [`@coolwallet/zen`](/packages/cws-zen) | ![version](https://img.shields.io/npm/v/@coolwallet/zen) | Zen Cash Application API     |

Other supported coins: BTC, BCH, ZEN. Open an issue if you want the sdk of any one of them to come out first.


## Examples: Build ETH in web app
### 建立連線

```
npm install @coolwallet/core
npm install @coolwallet/transport-web-ble
```

```javascript
import WebBleTransport from "@coolwallet/transport-web-ble";
import * as core from "@coolwallet/core";
```

建立連線，取得 Card Name 以及 SE Public Key

```javascript

connect = async () => {
  WebBleTransport.listen(async (error, device) => {
    console.log(device)
    if (device) {
      const cardName = device.name;
      const transport = await WebBleTransport.connect(device);
      const SEPublicKey = await core.config.getSEPublicKey(transport)
      this.setState({ transport, cardName, SEPublicKey });
      localStorage.setItem('cardName', cardName)
      localStorage.setItem('SEPublicKey', SEPublicKey)
      console.log(`SEPublicKey: ${SEPublicKey}`);
    } else {
      console.log(error);
    }
  });
};

disconnect = () => {
  WebBleTransport.disconnect(this.state.transport.device.id);
  this.setState({ transport: undefined, cardName: "" });
};

```

取得 app key pair

```javascript
const keyPair = crypto.key.generateKeyPair()
localStorage.setItem('appPublicKey', keyPair.publicKey)
localStorage.setItem('appPrivateKey', keyPair.privateKey)
```

### 配對卡片

配對卡片，取得 appId

```javascript
const name = 'your app name'
const SEPublicKey = localStorage.getItem('SEPublicKey')
const appId = await apdu.pair.register(transport, appPublicKey, password, name, SEPublicKey);
```

### 建立/還原錢包

建立錢包 by seed

```javascript
await apdu.wallet.setSeed(transport, appId, appPrivateKey, seedHex, SEPublicKey)

```


### 整合幣種

```
npm install @coolwallet/eth
```

```javascript
import cwsETH from '@coolwallet/eth'

const ETH = new cwsETH();
const address = await ETH.getAddress(
  transport,
  appPrivateKey,
  appId,
  addressIdx
); 

```

If you have `accountPublicKey` and `accountChainCode`, you can use the function `ETH.getAddressByAccountKey()` to get the address. 
```javascript
const address = await ETH.getAddressByAccountKey(
  accountPublicKey,
  accountChainCode,
  addressIndex
);
```

```javascript
const param = {
    nonce: "0x21d",
    gasPrice: "0x59682f00",
    gasLimit: "0x5208",
    to: "0x81bb32e4A7e4d0500d11A52F3a5F60c9A6Ef126C",
    value: "0x5af3107a4000",
    data: "0x00",
    chainId: 1
};
const signTxData = {
  transport,
  appPrivateKey,
  appId,
  transaction: param,
  addressIndex,
};

const signedTx = await ETH.signTransaction(signTxData);

```


If you want to build your own app with this sdk, you might find the following repos useful:

- [React Example](https://github.com/antoncoding/cws-web-ble-demo) (with `web-ble`)

- [React-Native Example](https://github.com/kunmingLiu/cws-rn-ble-demo) (with `rn-ble`)

If you build something new, welcome to contact us to put your work in the list.
