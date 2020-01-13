---
date: 2018-07-11
updated:  2018-07-23
tag: 
  - Promise
  - ES6
author: NeoYo
location: Shenzhen
---

# Promise 小知识

本文记录了 Promise 使用中容易被忽视但又重要的细节。

## constructor

```js
function testConstructor() {
  console.log('1. Init');
  const promise = new Promise((resolve) => {
    resolve('4. Promise Then');
    // Promise 构造函数传进的函数会立即执行
    console.log('2. Call in Promise constructor paremeter');
  });
  promise.then((msg) => {
    // Promise then 总会被异步调用
    console.log(msg);
  });
  console.log('3. End');  
}
testConstructor();
```

## resolve then

```js
function testThenRevoke() {
  console.log('1. Init');
  const promise = new Promise((resolve) => {
    resolve('Resolve First');
    // 只接受第一个 Resolve
    resolve('Resove Second');
    console.log('2. Call in Promise constructor');
  });
  promise.then((value) => {
    console.log('Then First: ', value);
  });
  promise.then((value) => {
    console.log('Then Second: ', value);
  });
  console.log('3. End');  
};
testThenRevoke();
```

## reject

```js
function testReject() {
  console.log('1. Init');
  const promise = new Promise((resolve) => {
    undefined.key;
    console.log('Call in Promise constructor');
  });
  promise
      .then(
        function onFulfilled(value) {
          console.log('Then onFulfilled: ', msg);
        },
        function onRejected(reason) {
          console.log('3. Then onRejected: ' + reason);
          // throw new Error(reason);
        },
      )
      .catch((error) => {
        // 传进 Promise catch 的回调将不会执行
        // 因为 promise onRejected 默认会 throw new Error(reason);
        console.log('Catch: ' + error);
      });
  console.log('2. End');  
};
testReject();
```

## Promise.resolve

```js
function testPromiseResolve() {
  const p1 = new Promise(function(resolve) {
    console.log('resolve begin');
    setTimeout(() => {
      resolve(42);
    }, 3000);
    console.log('resolve end');
  });
  const p2 = Promise.resolve(42);
  const p3 = Promise.resolve(p1);
  console.log('p1 === p2: ', p1 === p2);
  console.log('p1 === p3: ', p1 === p3);
}

testPromiseResolve();
```

## cacel a promise

extract resolve or reject

```js
function cacelPromise1() {
  let mResolve;
  const p1 = new Promise(function(resolve, reject) {
    setTimeout(() => {
      resolve('Fullfill after 3s');
    }, 3000);
    mResolve = resolve;
  });
  // mResolve('Fullfill at once');
  p1.then((msg) => {
    console.log(msg);
  })
}
cacelPromise1();
```

## Promise.race

```js
// how to cacel a promise
function cacelPromise2() {
  const p1 = new Promise(function(resolve, reject) {
    setTimeout(() => {
      resolve('Fullfill after 3s');
    }, 300000);
  });
  const p2 = new Promise((resolve) => {
    setTimeout(() => {
      resolve('Ahead');
    }, 100);
  })
  Promise.race([p1, p2]).then((value) => {
    console.log(value);
  })
}

cacelPromise2();
```

# Reference

JS Promise 迷你书
