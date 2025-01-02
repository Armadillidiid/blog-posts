---
title: A Comprehensive Guide to JavaScript Generators
---

Generators are a powerful concept in programming that allows you to easily define iterators. They're an
abstraction that allows you to write code that can be paused and resumed, allowing you to control the
execution flow.

In this article, we’ll go over what are generators functions, how to create and iterate over them,
their usefulness in processing streams of data and long-running asynchronous operations.

## Understanding JavaScript Generators?

A function generator is a special type of function that returns a `Generator` object and conforms to the [iterable protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) and the [iterator protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterator_protocol). They were first introduced in [ES5](https://262.ecma-international.org/6.0) and have since become a fundamental part of the language.

They are defined using the function keyword suffixed with an asterisks like so `function*`. Here's an example:

```typescript
function* generatorFunction() {
  return "Hello World";
}
```

Sometimes, you might find the asterisks prefixed to the function name like so `*function`. This is syntax is less common but is still valid.

In a normal function, when you call it, it runs to completion and returns a value. However, with generator functions, when you call them, they return a `Generator` object. This object is an [iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterator_protocol) that we can then use to iterate over the values.

An iterator is an object that has a `next()` method available, which is used for looping through a sequence of values. The `next()` method returns an object with `value`` and`done``properties.`value`represent the returned value, and`done` indicates whether the iterator has run through all its values or not.

To begin, let's initialize our generator function and retrieve its value:

```typescript
const generator = generatorFunction();
```

If you try to log the value of the generator object, you'll notice that it's not a string `Hello World` as expected. Instead, it's a `Generator` object with a `suspended` state. The `suspended` state means that the generator function has not yet started executing.

To get the value of the generator function, we need to call the `next()` method on the generator object:

```typescript
const result = generator.next();
```

This will give us the following output:

```typescript
{ value: 'Hello World', done: true }
```

It returned our `Hello World` string as the `value` of the object key and the state `done` equal `true` because there is no more code to execute. As a result, the status of the generator function changes from `suspended` to `closed`.

So far we've only seen how to return a single value from a generator function. But what if we want to return multiple values? This is where the `yield` operator comes in.

## The yield Operator

Take for exmaple the following function:

```typescript
function* generatorFunction() {
  yield "first value";
  yield "second value";
  yield "third value";
  yield "last value";
}
```

When we invoke the `next()` method on the generator object, it will pause each time it meets `yield`. The `done` property will be `false` after each yield as it's still in progress. Once there are no further yields in the function, or it reaches a `return` statement, `done` property will change to `true`, indicating that the generator has completed its execution.

If we call the `next()` method four times, we'll get the following output:

```typescript
const generator = generatorFunction();
generator.next(); // { value: 'first value', done: false }
generator.next(); // { value: 'second value', done: false }
generator.next(); // { value: 'third value', done: false }
generator.next(); // { value: 'last value', done: true }
```

But not only can we use the `yield` keyword to return values, but we can also use it to receive values from the caller.

## Passing Values to Generators

Yield in generator functions are two-way streets. They can both return values and receive values from the caller. To pass a value to a generator function, we can call the `next()` method with an argument.

```typescript
function* generatorFunction() {
  console.log(yield);
  console.log(yield);
}

const generator = generatorFunction();

generator.next();
generator.next("first input");
generator.next("second input");
```

This would log the following sequentially:

```typescript
first input
second input
```

> It's also important to note that the first call to the `next()` method will always be `undefined`. This is because the first `next()` call does not have a corresponding `suspended` yield operation, so there's no way to get the argument passed to it.

## Processing Long Async Operations and Streams

With the arrival of [ECMAScript 2017](https://262.ecma-international.org/8.0/index.html), we got the `async` versions of generator functions. They're referred to as [async generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncGenerator) as they always yield a promise object.

This gives us a plethora of possibilities to work with as our code is no longer bound by being synchronous. We can now work with asynchronous operations like fetching data from an API, reading files, or any other operation that requires resolving a promise.

Here's a simple example of an async generator function:

```typescript
async function* asyncGenerator() {
  yield await Promise.resolve("1");
  yield await Promise.resolve("2");
  yield await Promise.resolve("3");
}

const generator = asyncGenerator();
await generator.next(); // { value: '1', done: false }
await generator.next(); // { value: '2', done: false }
await generator.next(); // { value: '3', done: true }
```

The only difference here is that we're awaiting the `next()` method call before we can gain the value of the iterator result.

We can go a step further to show how we can use async generators to view paginated datasets from a remote API. This is a perfect use case for async generators as we can encapsulate our sequential iteration logic in a single function.

For this example, we'll use the free [DummyJSON API](https://dummyjson.com) to fetch a list of paginated products.

To get data from this API, we can make a GET request to the following endpoint. We'll pass `limit` and `skip` params to limit and skip the results for pagination:

```
https://dummyjson.com/products?limit=10&skip=0
```

The request will respond with a JSON object that looks similar to this:

```json
{
  "products": [
    {
      "id": 1,
      "title": "Annibale Colombo Bed",
      "price": 1899.99
    },
    {...},
    {...},
    {...}
    // 10 items
  ],
  "total": 194,
  "skip": 0,
  "limit": 10
}
```

To get the next page of products, we can increment the `skip` parameter by the `limit` value. We can continue this process until we reach the total number of products.

With that in mind, this is how we can implement a custom generator function to fetch all the products from the API:

```typescript
async function* fetchProducts(skip = 0, limit = 10) {
  let total = 0;

  do {
    const response = await fetch(
      `https://dummyjson.com/products?limit=${limit}&skip=${skip}`,
    );
    const { products, total: totalProducts } = await response.json();

    total = totalProducts;
    skip += limit;
    yield products;
  } while (skip < total);
}
```

Now we can iterate over it to get all the products using `for await...of` loop:

```typescript
for await (const products of fetchProducts()) {
  for (const product of products) {
    console.log(product.title);
  }
}
```

It will log the products until there is no more data to fetch.

```txt
Essence Mascara Lash Princess
Eyeshadow Palette with Mirror
Powder Canister
Red Lipstick
Red Nail Polish
... // 15 more items
```

## Generators as State Machines

## RxJS vs Generators for Processing Streams

## Conclusion
