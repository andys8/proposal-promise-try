# Promise.try
ECMAScript Proposal, specs, and reference implementation for `Promise.try`

Spec drafted by [@ljharb](https://github.com/ljharb).

This proposal is currently [stage 0](https://github.com/tc39/proposals/blob/master/stage-0-proposals.md) of the [process](https://tc39.github.io/process-document/).

## Rationale

A common use case that I, and many others, have, is that I have a function, `f`. This function may be async, and return a Promise, or it may not - I don’t wish to have to know. However, I'd like to wrap it in a Promise so that if it _is_ async, or if it throws, I can lean on Promise semantics and `.catch` to handle it.

The typical “easy to remember” way this is achieved in JS Promises is with `Promise.resolve().then(f)`. This works great! It catches any exceptions thrown, and it Promise-wraps any thenable or value returned from the function. However, `f` is needlessly run asynchronously, on a future tick.

If I want `f` to be run on the same tick - since, after all, it might be synchronously returning a value - or if I want parallel semantics with an `async function` up to the first `await` - then I need to use `new Promise(resolve => resolve(f()))`. This achieves my goal, but is not ergonomic to write nor easy to remember.

Using `Promise.try(f)`, I can get the same semantics as the `new Promise` mess above, nicely mirroring `async function`, and allowing optimistically synchronous, but safe, execution of a function, and being able to work with a Promise afterwards. Yay!

Userland implementations
 - Bluebird: [`Promise.try`/`Promise.attempt`](http://bluebirdjs.com/docs/api/promise.try.html) - takes one function, calls it with no args.
 - Q: [`Q.try`/`Promise.prototype.fcall`](https://github.com/kriskowal/q/wiki/API-Reference#promisefcallargs) - `Q.try` takes one function, calls it with no args. `Promise#fcall` is deprecated, but takes a list of arguments, and invokes the given function with that list of arguments.
 - when: [`when.try`/`when.attempt`](https://github.com/cujojs/when/blob/master/docs/api.md#whentry) - takes one function, and an optional list of arguments, and invokes the given function with that list of arguments.

Further reading
 - http://cryto.net/~joepie91/blog/2016/05/11/what-is-promise-try-and-why-does-it-matter/
 - https://twitter.com/RReverser/status/695678489937186816

## Naming

The most common name is `try`, which has a clean parallel with a syntactic `try` block. A common alternative name is `attempt`, but this has been primarily for ES3 compatibility, and is not necessary here.

## Spec
You can view the spec in [markdown format](spec.md) or rendered as [HTML](https://ljharb.github.io/proposal-promise-try/).