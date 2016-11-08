# tiny-redux
a tiny redux-like clone without any fancy ESNext syntax

I wanted to make sure I had a firm understanding of the principles associated with an `immutable store` and `unidirectional data` flow as sought after in the popular redux design pattern. Rather than writing an entire library like redux I figured I could skip all the sanity checks and solely focus on writing the core functionality of this library. 

I also decided to forgo all the fancy ESNext syntax in case someone wanted to look at the guts of the redux without having to know and understand all of the new syntactic sugar.

Also here are a few important rules that I learned along the way 
- mutations: 
  - These are considered [pure functions](https://www.sitepoint.com/functional-programming-pure-functions/) `(NO ASYNC HERE)` and are the only way you should be affecting `state`
  - Any time a mutation occurs, you can emit the `CHANGE` event so all subscribers can be notified of the change. 
  - mutations need to return state to set the state
- actions: 
  - These are functions that can make `async` calls
  - then should use a mutation to write to the state when they are ready
  - Actions never touch state themselves
- state:
  - The true state object is never returned only a copy

The philosophy behind redux is highly opinionated and works great for many applications, however it should not be used as the golden hammer. If you have an application that works well without it, then don't feel like you need to adapt it to use something like redux.

A small example of how to use the tiny-redux file 

```javascript

var store = new Store({
  state: {},
  mutations: {
    test: function (state, payload) {
      if (payload && payload.type && payload.data) {
        //just a simple function to write some data to the state
        state[payload.type] = payload.data
      }
      return state
    }
  },
  actions: {
    test: function (payload) {
      //simulated ajax call 
      //which can then use the above 
      //mutation to write to the state
      return new Promise(function (resolve, reject) {
        setTimeout(function () {
          return resolve({type: 'test', data: payload})
        }, 3000)
      }).then(function (res) {
        return store.commit(res.type, res.data)
      })
    }
  }
})

store.dispatch('test', {type: 'what', data: {hello: 'hello'}})

store.on('test', function (payload) {
  console.log(payload)
})

store.commit('non-registered-mutation', {wont: 'work'})

store.commit('test', {type: 'what', data: {hello: 'world!'}})
```



