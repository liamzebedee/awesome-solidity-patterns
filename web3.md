# Web3
## Events
Events are a good way of getting data from your contract into the UI. Why? Because they're the simplest access pattern within the realm of many asynchronous Web3 requests. 

#### 1. Getting all past events from the contract
```js
let evs = await contract.getPastEvents('EventName', {
    fromBlock: 0,
    toBlock: 'latest'
});

let evs = await contract.getPastEvents('eventName or allEvents', {
    fromBlock: 0,
    toBlock: 'latest'
});
```
       
#### 2. Listening to new events
```js
let emitter = contract.events.EventName();
emitter.on('data', ev => {
    // handle
})

let emitter = contract.events.allEvents();
emitter.on('data', ev => {
    // handle
})
```


## Making large numbers of requests
Running and connecting to a WebSocket server is much more performant for many calls (especially in parallel). However, even more performant (and HTTP-compatible) is using batch requests in Web3 1.x. Unfortunately, Web3 doesn't yet support batch requests with Promises (only callbacks) - below will help:

```
function makeBatchRequest(calls) {
    let batch = new web3.BatchRequest();

    let promises = calls.map(call => {
        return new Promise((res, rej) => {
            let req = call.request(params, (err, data) => {
                if(err) rej(err);
                else res(data)
            });
            batch.add(req)
        })
    })
    batch.execute()

    return Promise.all(promises)
}

// Usage
let [
    returnValueFromCall,
    publicProp,
    txid
] = await makeBatchRequest([
    contract.methods.getSomething().call,
    contract.methods.publicProp().call,
    contract.methods.makeAChange().send
])
```
