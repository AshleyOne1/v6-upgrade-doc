# v6-upgrade-doc

## Installation
In the previous version before v6:
```bash
npm install tronweb   # 或者：yarn add tronweb
```

In v6.x:
```bash
npm install tronweb@beta  # 或者 yarn add tronweb@beta
```

## import
In the previous version before v6:

```js
const TronWeb = require('tronweb');
```

In v6.x:

```js
//js project using esm package
import { TronWeb, utils as TronWebUtils, Trx, TransactionBuilder, Contract, Event, Plugin, providers} from 'tronweb';

//js project using commonjs/dist package
const { TronWeb, utils, Trx, TransactionBuilder, Contract, Event, Plugin, providers} = require('tronweb');

//typescript project using esm/commonjs package, when using dist package, please use detailed tronwb's path.
import {TronWeb, Plugin, providers, utils, Trx, Event, TransactionBuilder, Contract} from 'tronweb';
```

NOTE: In a word, You can use major function parts like TransactionBuilder/Trx/Contract... independently in v6.x. But in previous version, we can only take TransactionBuilder/Trx/Contract... as attribute of tronweb instance,such as tronweb.TransactionBuilder.

## Creating an Instance
In the previous version before v6, Tronweb constructor is as below.
```js
constructor(options = false,
    // for retro-compatibility:
    solidityNode = false, eventServer = false, sideOptions = false, privateKey = false) {
    super();

    let fullNode;
    let headers = false;
    let eventHeaders = false;

    if (typeof options === 'object' && (options.fullNode || options.fullHost)) {
        fullNode = options.fullNode || options.fullHost;
        sideOptions = solidityNode;
        solidityNode = options.solidityNode || options.fullHost;
        eventServer = options.eventServer || options.fullHost;
        headers = options.headers || false;
        eventHeaders = options.eventHeaders || headers;
        privateKey = options.privateKey;
    } else {
        fullNode = options;
    }
    if (utils.isString(fullNode))
        fullNode = new providers.HttpProvider(fullNode);

    if (utils.isString(solidityNode))
        solidityNode = new providers.HttpProvider(solidityNode);

    if (utils.isString(eventServer))
        eventServer = new providers.HttpProvider(eventServer);
    ...
}
```

Supposing you are using a server that provides everything, like TronGrid, you can instantiate TronWeb as:

```js
const tronWeb = new TronWeb({
  fullHost: 'https://api.trongrid.io',
  headers: { 'TRON-PRO-API-KEY': 'your api key' },
  privateKey: 'your private key'
});
```
or you have sidechain options:
```js
let options = Object.assign({
        // fullHost: SIDE_CHAIN.fullNode,
        fullNode: SIDE_CHAIN.fullNode,
        solidityNode: SIDE_CHAIN.solidityNode,
        eventServer: SIDE_CHAIN.eventServer,
        privateKey: PRIVATE_KEY,
    }, extraOptions)
let sideOptions = Object.assign({
        // fullHost: SIDE_CHAIN.sideOptions.fullNode,
        fullNode: SIDE_CHAIN.sideOptions.fullNode,
        solidityNode: SIDE_CHAIN.sideOptions.solidityNode,
        eventServer: SIDE_CHAIN.sideOptions.eventServer,
        mainGatewayAddress: SIDE_CHAIN.sideOptions.mainGatewayAddress,
        sideGatewayAddress: SIDE_CHAIN.sideOptions.sideGatewayAddress,
        sideChainId: SIDE_CHAIN.sideOptions.sideChainId
}, sideExtraOptions);
const tronweb = new TronWeb(options, sideOptions);
```
And any other conditions suits to tronweb constructor function.

In v6.x verion, Tronweb constructor is as following.
```typescript
constructor(options: TronWebOptions);
constructor(fullNode: NodeProvider, solidityNode: NodeProvider, eventServer?: NodeProvider, privateKey?: string);
/* prettier-ignore */
constructor(fullNode: NodeProvider, solidityNode: NodeProvider, eventServer: NodeProvider, privateKey?: string);
constructor(
    options: TronWebOptions | NodeProvider,
    solidityNode: NodeProvider = '',
    eventServer?: NodeProvider,
    privateKey = ''
) {
    super();

    let fullNode;
    let headers: HeadersType | false = false;
    let eventHeaders: HeadersType | false = false;

    if (isValidOptions(options)) {
        fullNode = options.fullNode || options.fullHost;
        solidityNode = (options.solidityNode || options.fullHost)!;
        eventServer = (options.eventServer || options.fullHost)!;
        headers = options.headers || false;
        eventHeaders = options.eventHeaders || headers;
        privateKey = options.privateKey!;
    } else {
        fullNode = options;
    }
    if (utils.isString(fullNode)) fullNode = new providers.HttpProvider(fullNode);

    if (utils.isString(solidityNode)) solidityNode = new providers.HttpProvider(solidityNode);

    if (utils.isString(eventServer)) eventServer = new providers.HttpProvider(eventServer);
    ...
}
```

When you instantiate TronWeb you can define

* fullNode
* solidityNode
* eventServer
* privateKey

you can also set a

* fullHost

which works as a jolly. If you do so, though, the more precise specification has priority.
Supposing you are using a server which provides everything, like TronGrid, you can instantiate TronWeb as:

```js
const tronWeb = new TronWeb({
    fullHost: 'https://api.trongrid.io',
    headers: { "TRON-PRO-API-KEY": 'your api key' },
    privateKey: 'your private key'
})
```

For retro-compatibility, though, you can continue to use the old approach, where any parameter is passed separately:
```js
const tronWeb = new TronWeb(fullNode, solidityNode, eventServer, privateKey)
tronWeb.setHeader({ "TRON-PRO-API-KEY": 'your api key' });
```

If you are, for example, using a server as full and solidity node, and another server for the events, you can set it as:

```js
const tronWeb = new TronWeb({
    fullHost: 'https://api.trongrid.io',
    eventServer: 'https://api.someotherevent.io',
    privateKey: 'your private key'
  }
)
```

If you are using different servers for anything, you can do
```js
const tronWeb = new TronWeb({
    fullNode: 'https://some-node.tld',
    solidityNode: 'https://some-other-node.tld',
    eventServer: 'https://some-event-server.tld',
    privateKey: 'your private key'
  }
)
```
## TronWeb API Changes

### TronWeb.createRandom

In the previous version before v6, this API hasing optional parameter - options with three fields: 

```js
const password = "123456"
const path = "m/44'/195'/0'/0/13";
const wordlist = 'zh_cn';
const options = { password: password, path: path, wordlist: wordlist};
const newAccount = await tronWeb.createRandom(options);
```
In v6.x, this API need pass password, path, wordlist as respective parameters.
```js
const password = "123456"
const wordlist = wordlists.zh_tw;
const path = "m/44'/195'/3'/0/99";
const options = { path: path, wordlist};
const newAccount = await tronWeb.createRandom(password, path, wordlist);
```
### TronWeb.fromMnemonic 

Change TronWeb.fromMnemonic(mnemonic, path, wordlist) to TronWeb.fromMnemonic(mnemonic, path, password, wordlist).

In the previous version before v6:
```js
const wordlist = 'zh_tw';
const path = "m/44'/195'/3'/0/99";
const options = { path: path, locale: wordlist};
const newAccount = await tronWeb.createRandom(options);
const newAccount2 = await tronWeb.fromMnemonic(newAccount.mnemonic.phrase, path, wordlist);
```

In v6.x: 
```js
password = "123456"
path = "m/44'/195'/3'/0/99"
const newAccount = await tronWeb.createRandom(password, path);
const newAccount2 = await tronWeb.fromMnemonic(newAccount.mnemonic.phrase, path,password, wordlist);
```
### utils.abi.decodeParams


In the previous version before v6, if the type of third param is boolean, "names" param could be not passed.
```js
const types = ['string', 'string', 'uint8', 'bytes32', 'uint256'];
const output = '0x00000000000000000000000000000000000000000000000000000000000000a000000000000000000000000000000000000000000000000000000000000000e00000000000000000000000000000000000000000000000000000000000000012dc03b7993bad736ad595eb9e3ba51877ac17ecc31d2355f8f270125b9427ece700000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000011506920446179204e30306220546f6b656e00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000035049450000000000000000000000000000000000000000000000000000000000';
const result = tronWeb.utils.abi.decodeParams(types, output, false);
```

In v6.x, the parameters are as follows: utils.abi.decodeParams(names: string[], types: string[], output: string, ignoreMethodHash = false). You must pass names argument. If there is no name, pass an empty array.
```js
const types = ['string', 'string', 'uint8', 'bytes32', 'uint256'];
const output = '0x00000000000000000000000000000000000000000000000000000000000000a000000000000000000000000000000000000000000000000000000000000000e00000000000000000000000000000000000000000000000000000000000000012dc03b7993bad736ad595eb9e3ba51877ac17ecc31d2355f8f270125b9427ece700000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000011506920446179204e30306220546f6b656e00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000035049450000000000000000000000000000000000000000000000000000000000';
const result = tronWeb.utils.abi.decodeParams([], types, output);
```
### Strict check for position and type of parameters
In the previous version, most methods(expecially in transanctionBuilder API) support variable parameter length. For example, the following codes are both valid:

```js
// As to this api, the third parameter is 'fromAddress', and the forth is options. You can pass fromAddress.
fromAddress = 'THph9K2M2nLvkianrMGswRhz5hjSA9fuH7'
await tronWeb.transactionBuilder.sendTrx('toAddress', 10, fromAddress, { permissionId: 1 });

// If the third parameter is object, it will be treated as options and fromAddress will be its default value, that's tronWeb defaultAddress.
await tronWeb.transactionBuilder.sendTrx('toAddress', 10, { permissionId: 1 });
```
In v6.x, we use typescript, please note the default value parameter and optional parameter. if you want to use default value of fromAddress but pass value after it, you must use "undefined" as placeholder, or pass the default value. <font color=red> It's important and worth to check all your multi-sign transanctions created by transanctionBuilder.</font>

```js
await tronWeb.transactionBuilder.sendTrx('toAddress', 10, undefined, { permissionId: 1 });
```

Here's another example "delegateResource API" in transanctionBuilder if you want use defaut vaule params fromAddress.

```js
/*  the declaration of delegateResource
async delegateResource(
        amount = 0,
        receiverAddress: string,
        resource: Resource = 'BANDWIDTH',
        address: string = this.tronWeb.defaultAddress.hex as string,
        lock = false,
        lockPeriod?: number,
        options: TransactionCommonOptions = {}
    ) 
*/
receiverAddress = 'THph9K2M2nLvkianrMGswRhz5hjSA9fuH7'
//V5 use default value
const transaction = await tronWeb.transactionBuilder.delegateResource(10e6, receiverAddress, 'BANDWIDTH',)
//V6 use default valule 
const transaction = await tronWeb.transactionBuilder.delegateResource(10e6, receiverAddress, 'BANDWIDTH', undefined, undefined, 0, {permissionId : 2});
//V6 pass default value or new value.
const transaction = await tronWeb.transactionBuilder.delegateResource(10e6, receiverAddress, 'BANDWIDTH', tronWeb.defaultAddress.base58, false, 0, {permissionId : 2});
```



