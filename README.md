# dfdep
## Deferred Dependencies For The Browser

Modules that asynchronously require dependencies are often awkward to write. Dependencies
that are only potentially loaded (perhaps based on user action, or non-deterministic input) usually
end up inside of a nested, or deeply buried callback somewhere. This works, but kind of makes the
dependency tree ugly, and isn't always obvious at a glance. It'd be great to be able to declare
a top-level dependency with all of your other dependencies, but allow or force it's resolution to
be deferred. Enter dfdep.

Written for AMD or Browserify modules, dfdep allows you to declare a dependency that immediately
returns a function, but allows you to invoke that function to retrieve the module when you actually
want it. This is still an asynchronous action (via callbacks or promises), but helps unify the way
that you declare and think about dependencies. It implements the browser side code to allow you to
load in separate module bundles for different sections, without requiring nested `require` wrappers
or one-off hacks.

It's much more of a pattern than a library, so if it seems like you're missing something, you
probably aren't. We all know you're smart, you're just over-thinking it. They're just deferred
dependencies, not just asynchronous dependencies.

### Pre-Caching

One advanced feature of deferred dependencies in the browser is time or activity based pre-caching.

You can allow dfdep to load your deferred dependency at any time. That could mean right away
(weird), or perhaps just after the `onload` event, perhaps based on reduced user activity (implying
you have a moment to increase network activity). Whatever the case, dfdep will give you a way to
retrieve that module, and if it had already been preloaded and cached it will be much faster.

You can configure dfdep to fire at a certain time after onload, or you can specifically trigger a
pre-cache of a dependency at any point prior to actually invoking the deferred function via a call
to the `precache` function.

## Examples

```javascript
define(['a', 'dfd!b'], function (A, b) {
  var myA = A();

  // With a promise
  setTimeout(function() {
    b().then(function(b) {
      // Use b as you normally would
      myA.list.push(b.prop);
    });
  }, 10000);
});
```
