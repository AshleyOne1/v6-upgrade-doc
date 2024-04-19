# v6-upgrade-doc

## Installation
before(v5):
```bash
npm install tronweb   # 或者：yarn add tronweb
```

after(v6)
```bash
npm install tronweb@beta  # 或者 yarn add tronweb@beta
```

## import
before(v5):

```js
const TronWeb = require('tronweb');
```

after(v6): 

js project using esm package
```js
import { TronWeb, utils as TronWebUtils, Trx, TransactionBuilder, Contract, Event, Plugin, providers} from 'tronweb';
```

js project using commonjs/dist package
```js
const { TronWeb, utils, Trx, TransactionBuilder, Contract, Event, Plugin, providers} = require('tronweb');
```

typescript project using esm/commonjs package, when using dist package, please use detailed tronwb's path.
```typescript
import {TronWeb, Plugin, providers, utils, Trx, Event, TransactionBuilder, Contract} from 'tronweb';
```

NOTE: In a word, You can use major function parts like TransactionBuilder/Trx/Contract... independently in V6. But in V5, we can only take TransactionBuilder/Trx/Contract... as tronweb instance attribute,such as tronweb.TransactionBuilder.

## Creating an Instance
before(V5):
Tronweb constructor:
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

after(V6):
Tronweb constructor:
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

