# Hive Stream

A Node.js layer for Hive that allows you to watch for specific actions on the Hive blockchain.

## Install

```shell
npm install hive-stream
```

## Quick Usage

```javascript
const { Streamer } = require('hive-stream');

const ss = new Streamer();

// Watch for all custom JSON operations
ss.onCustomJson((op, { sender, isSignedWithActiveKey }, blockNumber, blockId, prevBlockId, trxId, blockTime) => {
  // React to custom JSON operations
});
```

## Configuration

The `Streamer` object can accept an object of configuration values which are all optional. However, some operations like transfering Hive Engine tokens or other operations on the blockchain that are not READ ONLY, will require the active key and/or posting keys supplied as well as a username.

The `BLOCK_CHECK_INTERVAL` value is how often to check for new blocks or in cases of error or falling behind, to poll for new blocks. It is advisable you keep this as the default 1000ms value which is one second. This allows you to account for situations where blocks fall behind the main block.

The `BLOCKS_BEHIND_WARNING` value is a numeric value of the number of blocks your API will fall behind from the master before warning to the console.

The `CHAIN_ID` value is only for Hive Engine related operations. The `API_URL` is the Hive API. If you want to enable debug mode, set to `DEBUG_MODE` to `true`. The configuration values and their defaults can be found [here](https://github.com/Vheissu/hive-stream/blob/master/config.js).

```
const options = {
  ACTIVE_KEY: '',
  POSTING_KEY: '',
  APP_NAME: 'hive-stream',
  USERNAME: '',
  LAST_BLOCK_NUMBER: 0,
  BLOCK_CHECK_INTERVAL: 1000,
  BLOCKS_BEHIND_WARNING: 25,
  CHAIN_ID: 'ssc-mainnet1',
  API_URL: 'https://api.hiveit.com',
  DEBUG_MODE: false
}

const ss = new Streamer(options);
```

The configuration itself can also be overloaded using the `setConfig` method which allows you to pass one or more of the above configuration options, useful in situations where multiple keys might be used for issuing.

```
ss.setConfig({
  ACTIVE_KEY: 'newactivekey',
  USERNAME: 'newusername'
});
```

## Streamer

The following subscription methods are read only methods, they allow you to react to certain Hive and Hive Engine events on the blockchain. You do not need to pass in any keys to use these methods as they're purely read only.

**The following actions DO require calling the `start` method first to watch the blockchain**

#### Watch for transfers

```javascript
ss.onTransfer((op,blockNumber, blockId, prevBlockId, trxId, blockTime) => {

})
```

#### Watch for custom JSON operations
```javascript
ss.onCustomJson((op, { sender, isSignedWithActiveKey }, blockNumber, blockId, prevBlockId, trxId, blockTime) => {
  
})
```

#### Watch for Hive Engine JSON operations
```javascript
ss.onSscJson((contractName, contractAction, contractPayload, sender, op, blockNumber, blockId, prevBlockId, trxId, blockTime) => {
  
})
```

#### Watch for post operations
```javascript
ss.onPost((op, blockNumber, blockId, prevBlockId, trxId, blockTime) => {

});
```

#### Watch for comment operations
```javascript
ss.onComment((op, blockNumber, blockId, prevBlockId, trxId, blockTime) => {

});
```

## Actions (active key)

All of the below methods require an active key has been supplied in the constructor above called `ACTIVE_KEY`. The methods below are all promised based, so you can `await` them or use `then` to confirm a successful result.

**The following actions do NOT require calling the `start` method first to watch the blockchain**

```javascript
const ss = new Streamer({
  ACTIVE_KEY: 'youractivekey'
});
```

### Transfer Hive (STEEM or SBD)
```javascript
transferHiveTokens(from, to, amount, symbol, memo = '') {

}
```

### Transfer Hive Engine tokens
```javascript
transferHiveEngineTokens(from, to, symbol, quantity, memo = '') {

}
```

### Transfer Multiple Hive Engine tokens

The accounts array should contain one or more objects with the following keys:

```
{
  account: 'hiveaccountname',
  amount: '2.00'
}
```

If the object does not contain an amount, you will need to supply a default amount to the function itself. By default the default amount if no value is supplied is '0'. If every object in the array has an amount specific, the default amount is ignored.

```javascript
transferHiveEngineTokensMultiple(from, accounts = [], symbol, memo = '', amount = '0') {

}
```

### Issue Hive Engine tokens
```javascript
issueHiveEngineTokens(from, to, symbol, quantity, memo = '') {

}
```

### Issue Multiple Hive Engine tokens

The accounts array should contain one or more objects with the following keys:

```
{
  account: 'hiveaccountname',
  amount: '2.00'
}
```

If the object does not contain an amount, you will need to supply a default amount to the function itself. By default the default amount if no value is supplied is '0'. If every object in the array has an amount specific, the default amount is ignored.

```javascript
issueHiveEngineTokensMultiple(from, to, symbol, quantity, memo = '', amount = '0') {

}
```

## Permanently running with PM2

Simply copy the `ecosystem.config.js` file from this repository into your application, globally install `pm2` via `npm install pm2 -g` and change the `script` value below to reflect the main file of your application.

**ecosystem.config.js**

```
module.exports = {
  apps: [
    {
      name: 'hive-stream',
      script: 'index.js',
      ignore_watch: ['node_modules'],
      env: {
        NODE_ENV: 'development'
      },
      env_production: {
        NODE_ENV: 'production'
      }
    }
  ]
```
