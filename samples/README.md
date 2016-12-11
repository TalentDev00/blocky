# Sample
Ethereum based smart contract samples

## Bridge
Import contract
```javascript
> personal.unlockAccount(eth.coinbase, YOUR_PASSWORD)
true
> loadScript('bridge.js');
I1212 00:09:54.484559 internal/ethapi/api.go:1045] Tx(0x94075470292dfe99dfe933ba3e330a049630cf5f767c9afb46632ed4090f4cad) created: 0x3b26e8bd43effdcc0ce824c6bc29d40bac3b5aad
null [object Object]
true
```

Deploy to blockchain
```javascript
> miner.start()
...
Contract mined! address: 0x3b26e8bd43effdcc0ce824c6bc29d40bac3b5aad transactionHash: 0x94075470292dfe99dfe933ba3e330a049630cf5f767c9afb46632ed4090f4cad
...
> miner.stop()
```

Check contract status
```javascript
> bridge
{
  abi: [{
      constant: true,
      inputs: [],
      name: "creator",
      outputs: [{...}],
      payable: false,
      type: "function"
  }, {
      constant: false,
      inputs: [{...}],
      name: "request",
      outputs: [],
      payable: false,
      type: "function"
  }, {
      constant: false,
      inputs: [],
      name: "kill",
      outputs: [],
      payable: false,
      type: "function"
  }, {
      constant: false,
      inputs: [{...}, {...}],
      name: "activate",
      outputs: [],
      payable: false,
      type: "function"
  }, {
      inputs: [],
      payable: false,
      type: "constructor"
  }, {
      anonymous: false,
      inputs: [{...}, {...}],
      name: "Notify",
      type: "event"
  }, {
      anonymous: false,
      inputs: [{...}, {...}, {...}],
      name: "Process",
      type: "event"
  }],
  address: "0x3b26e8bd43effdcc0ce824c6bc29d40bac3b5aad",
  transactionHash: "0x94075470292dfe99dfe933ba3e330a049630cf5f767c9afb46632ed4090f4cad",
  Notify: function(),
  Process: function(),
  activate: function(),
  allEvents: function(),
  creator: function(),
  kill: function(),
  request: function()
}
```

Export contract on another node
```javascript
> var bridge_on_blockchain = eth.contract([{ constant: true, inputs: [], name: "creator", outputs: [{ name: "", type: "address" }], payable: false, type: "function" }, { constant: false, inputs: [{ name: "data", type: "string" }], name: "request", outputs: [], payable: false, type: "function" }, { constant: false, inputs: [], name: "kill", outputs: [], payable: false, type: "function" }, { constant: false, inputs: [{ name: "gateway", type: "address" }, { name: "data", type: "string" }], name: "activate", outputs: [], payable: false, type: "function" }, { inputs: [], payable: false, type: "constructor" }, { anonymous: false, inputs: [{ indexed: true, name: "from", type: "address" }, { indexed: false, name: "message", type: "string" }], name: "Notify", type: "event" }, { anonymous: false, inputs: [{ indexed: true, name: "from", type: "address" }, { indexed: true, name: "to", type: "address" }, { indexed: false, name: "message", type: "string" }], name: "Process", type: "event" }] ).at("0x3b26e8bd43effdcc0ce824c6bc29d40bac3b5aad");
```

IoT vendor starts watching for Notify() events (i.e IoT gateway message)
```javascript
var event_notify = bridge.Notify();
event_notify.watch(function(error, result){
    if (!error) {
        console.log("[0xdeadbeefb00b1e56] MSG[" + result.args.message + "] FROM[" + result.args.from + "]");
    }
});
```

IoT Gateway starts watching for Process() events (i.e. IoT vendor messages)
```javascript
var event_process = bridge.Process();
event_process.watch(function(error, result){
    if (!error) {
        console.log("[0x0123456789abcdef] MSG[" + result.args.message + "] FROM[" + result.args.from + "] TO[" + result.args.to + "]");
    }
});
```

IoT Gateway sends a message to IoT vendor via blockchain (either activation request or data)
```javascript
> personal.unlockAccount(eth.coinbase, YOUR_PASSWORD)
true
> bridge_on_blockchain.request('myjsonstring',{from: eth.coinbase})
I1212 00:46:01.687701 internal/ethapi/api.go:1047] Tx(0x61e3f2b44d51de0168966e44e2d1bf22054fad50bf48ca8ecb2b16626751fc86) to: 0x3b26e8bd43effdcc0ce824c6bc29d40bac3b5aad
"0x61e3f2b44d51de0168966e44e2d1bf22054fad50bf48ca8ecb2b16626751fc86"
```

IoT vendor watch notified with gateway message
```javascript
> I1212 00:46:18.675648 core/blockchain.go:1047] imported 1 blocks,     1 txs (  0.025 Mg) in  10.235ms ( 2.454 Mg/s). #407 [08be0754…]
I1212 00:46:18.960413 core/blockchain.go:1047] imported 1 blocks,     0 txs (  0.000 Mg) in   9.505ms ( 0.000 Mg/s). #408 [16c4683e…]
I1212 00:46:18.969545 core/blockchain.go:1047] imported 1 blocks,     0 txs (  0.000 Mg) in   8.578ms ( 0.000 Mg/s). #408 [86f92da9…]
[0xdeadbeefb00b1e56] MSG[myjsonstring] FROM[0xcb2a95f964acf8adee7fae30cf5dc6a3f5e14a5c]
I1212 00:46:20.049817 core/blockchain.go:1047] imported 1 blocks,     0 txs (  0.000 Mg) in   6.588ms ( 0.000 Mg/s). #409 [fd02e772…]
I1212 00:46:20.973832 core/blockchain.go:1047] imported 1 blocks,     0 txs (  0.000 Mg) in  12.712ms ( 0.000 Mg/s). #410 [83942e5d…]
```

