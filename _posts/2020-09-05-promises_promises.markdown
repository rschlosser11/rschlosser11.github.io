---
layout: post
title:      "Promises Promises"
date:       2020-09-05 19:56:08 +0000
permalink:  promises_promises
---


While working through the Javascript, React, and Redux lessons, we often used the asynchronous function, fetch. This function returned a promise and I wondered what exactly is a promise? We are all familiar with promises in our daily lives, 'I promise to do that' or 'I promise to avoid this'. Promises are a vow to do or to not do something. I assumed, since the object returned from fetch was called a promise, that it would behave similarly to what we understand about a promise. And it does!

A promise is an object that may result in a value in the future, a resolved one or a reason for not resolving. So, it makes since that it can be in one of three possible states, pending (I'm working on it), fulfilled (here's what you asked for), or rejected (a problem occurred and I wasn't able to get the information you asked for). As soon as you call a fetch (or some other promise constructor), it immediately goes to work on handling your request. A promise is settled once it either is fulfilled or rejected and cannot change.

I didn't really think too much about promises outside of when using fetch. However, I learned that you can actually create a Promise using a promise constructor. The constructor takes only one argument a function. That function can take two arguments, resolve() and reject(). The syntax would look like

```

let promise = new Promise(function(resolve, reject) {
     //do something
});

```

An easy, but ridiculous, example would be

```

let promise = new Promise((resolve, reject) => {
  const a = 'something';
  const b = 'something';
  if (a === b) {
    resolve();
  } else {
    reject()
  }
})

```

In this case if a is equal to b the promise is resolved or else it is rejected. But how does the promise know what resolve() and reject() are? Using .then()! Every promise must supply one. 

.then() accepts two functions as parameters. The first is onFulfilled(), which will run if the promise is fulfilled and the first argument is the promise's value. The optional second function is onRejected(), which will run if the promise is rejected and why it was rejection is the first argument. But the much more common approach is to chain .then(), to handle fulfilled promises, and .catch(), to handle rejected promises or errors.   

```

let promise = new Promise((resolve, reject) => {
  const a = 'something';
  const b = 'something';
  if (a === b) {
    resolve();
  } else {
    reject()
  }
})

promise.then(() => console.log('success')) // console logs 'success' if a === b
  .catch(() => console.log('failed')) // console logs 'failed' if a !== b
	
// Console looks like: 

// Promise { <pending > }
// success

```

Since the promise is fulfilled, success is console logged.

I also found another chainable function that promises can use, .finally(). Like .then() and .catch(), finally returns a promise and once that promise is settled (either successfully or not), the callback function that is provided is called. I can see this being really useful if I'd want to do some cleanup or processing regardless of if the promise is fulfilled or not, like perhaps a isLoading state.

Going back to our fulfilled example  

```

let promise = new Promise((resolve, reject) => {
  const a = 'something';
  const b = 'something';
  if (a === b) {
    resolve();
  } else {
    reject()
  }
})

promise.then(() => console.log('success'))
  .catch(() => console.log('failed'))
	.finally(() => console.log('done'))
	
// Console looks like: 

// Promise { <pending > }
// success
// done

```

or if we make a change to 'b' so the promise is rejected the results would be

```

let promise = new Promise((resolve, reject) => {
  const a = 'something';
  const b = 'something else';
  if (a === b) {
    resolve();
  } else {
    reject()
  }
})

promise.then(() => console.log('success'))
  .catch(() => console.log('failed'))
	.finally(() => console.log('done'))
	
// Console looks like: 

// Promise { <pending > }
// failed
// done

``` 

There is so much to learn about what is going on behind the scenes of a simple fetch request. I'm looking forward to continuing to learn how to more fully utilize promises.

