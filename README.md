# Reticle
Put a scope on gunDB.

[![npm](https://img.shields.io/npm/dt/reticle.svg?style=flat-square)](https://www.npmjs.com/package/reticle)[![Travis branch](https://img.shields.io/travis/PsychoLlama/Reticle/master.svg?style=flat-square)](https://travis-ci.org/PsychoLlama/Reticle)[![Gitter](https://img.shields.io/gitter/room/amark/gun.js.svg?style=flat-square)](http://gitter.im/amark/gun)

## What it is
> Reticle is a tool built for [gunDB](https://github.com/amark/gun)

By default, gun's data is global. If you use the same key by accident on a different app, both datasets will merge into one frankensteinian nightmare. Reticle solves this by namespacing your keys under a hashed string, preventing otherwise gruesome conflicts from ever happening.

## How it works
You can scope your databases under a name, like `Todo List`, and Reticle will produce a hashed string (`'7b3ozc'`) and automatically upgrade your context to use that new namespace.

### Including
**Node.js**

To use Reticle in node, `require('reticle')` in your app. Reticle is version agnostic when it comes to gunDB, and since you may be using multiple versions of gun with node, Reticle needs an explicit reference to the constructor you want to extend.
```javascript
var Gun = require('gun')

// reticle automatically upgrades gun
require('reticle')
```

**Browser**

To use Reticle in the browser, you can include it as a script tag.
```html
<script src="reticle.min.js"></script>
```
Since `Gun` is exported to the global namespace, Reticle knows what version you're using and can automatically attach itself.


## API


Reticle exposes two methods through Gun...

 - one on the constructor: `Gun.scope`
 - one on each instance: `gun.scope`

To set a scope for every gun instance you create, use the constructor method.
```javascript
Gun.scope('Todo List')

// both are scoped under "Todo List"
var list = Gun().get('list')
var items = Gun().get('list/items')
```

To scope just one instance, use the instance method.
```javascript
var gun = Gun().scope('Todo List').get('list/items');
```

Once the scope has been set, it will remain the same until changed again.

> Scope names are case sensitive

## Examples
Using two instances without collision
```javascript
var fleetPlayers, tracePlayers;
fleetPlayers = Gun().scope('battlefleet').get('players')
tracePlayers = Gun().scope('trace').get('players')

fleetPlayers.put({
  'Player 0': 'Bob'
})
tracePlayers.put({
  'Player 0': 'Dave'
})
fleetPlayers.path('Player 0').val() // "Bob"
tracePlayers.path('Player 0').val() // "Dave"
```

## Edge cases
Reticle will try to convert input to a string so it can hash it. If the value it's given is a isn't a string, Reticle will try to convert it. If the value is falsy, gun will revert to it's global behavior. For example, if you call `.scope` with `null` or empty string `""`, **your namespace is global**. There is no prefix.

Previous versions of gun have mixed support for the `uuid` option in the gun constructor. To be sure the UUIDs are scoped properly, the `Gun.text.random` function was overloaded. If you're using that function, you may notice the output being prepended with a scope.

## Support
If you find any problems or know of a way to improve Reticle (or gun for that matter), send us a message on [Gitter](http://gitter.im/amark/gun). We're almost always on that channel.
