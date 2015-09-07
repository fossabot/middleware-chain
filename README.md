# middleware-chain

A middleware chain component for Node.js.

Current version: **1.0.0**

Lead Maintainer: [Halim Qarroum](mailto:hqarroum@awox.com)

## Install

Only 0.5 kB Gzipped.

```sh
$ npm install --save middleware-chain
```

```sh
$ bower install --save middleware-chain
```

## Motivations

I find the [chain of responsibility](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern) pattern to be pretty useful in a lot of components I write. It allows to separate responsibilities in the form of pluggable handlers along a processing chain which decouples the treatments made on the chain. It also makes hotplugging of new treatments while the application is running possible, and exposes a clean and evolutive interface.

The programming interface provided by this component draws its inspiration from the [Express framework](http://expressjs.com/), in which [`routers` and `middlewares`](http://expressjs.com/guide/using-middleware.html) are in charge of treating received requests.

## Semantics

A chain holds a collection of middlewares, and iterates over them as a treatment is handled.

A *middleware* is a software component that handles an `input`, decides whether it can handle it, and if it does, produces an `output`. If a middleware cannot handle an input, the next middleware in the chain is called.

What an input and an output concretely are, is dependant on the implementation, and the client of the library will have to provide them. But globally, their semantics must *not* change in order to keep an appropriate logic.

## Usage

### Creating a chain

Creating a middleware chain is as simple as calling its constructor. A new instance does not contain any middleware.

```javascript
var chain = new Chain();
```

### Adding middlewares

You will then be able to push new middlewares in the chain using the `.use` method.

```javascript
chain.use(function (input, output, next) {
  // Perform some treatment.
  next();
});
```

It is also possible to push many middlewares at once using an array, which comes in handy when grouping a particular set of actions together.

```javascript
chain.use([
    function (input, output, next) {
        // Verify for instance that the
        // `input` is correctly formatted.
        next();
    },
    
    function (input, output, next) {
        // Perform some treatment on
        // the `input`.
        next();
    }
]);
```

Similarly, you can push many middlewares by simply passing them in the argument list of `.use`.

```javascript
chain.use(
  function (input, output, next) {
    next();
  },
    
  function (input, output, next) {
    next();
  }
);
```

### Middleware lifecycle

When a middleware is called in the chain, it can handle an input, and interact with the output. If the middleware cannot handle the given input, it should call the next middleware in the chain.

```javascript
chain.use(function (input, output, next) {
  if (isNotHandled(input)) {
    // Calling the next middleware.
    return next();
  }
  // Otherwise, we treat the input.
  // In this example we "pipe" the input
  // with the output.
  output.write(input.data());
});
```
