Rebridge
========

[![npm](https://img.shields.io/npm/v/rebridge.svg?maxAge=2592000)](https://www.npmjs.com/package/rebridge)

Rebridge is a transparent Javascript-Redis bridge. You can use it to create JavaScript objects that are *automatically* synchronized to a Redis database.

## Install

```
npm install rebridge
```

##Usage

```js
var Rebridge = require("rebridge");

// Rebridge takes care of the Redis connection automatically,
// but you can pass an existing Redis client object if you wish.
var db = Rebridge();

db.hello = {world: ["foo", "bar"]};
// The change will be written to the database in real time.

console.log(db.hello.world);
// Prints ["foo", "bar"]
```

## Requirements

Rebridge uses ES6 Proxy objects, so it requires at least Node 6.

## Limitations

Rebridge objects can't contain functions, circular references, and in general everything for which `x === JSON.parse(JSON.stringify(x))` doesn't hold true.

* Obviously, you cannot write directly to `db` (i.e. you can't do `var db = Rebridge(); db = "e"`).

## Notable users

Rebridge is used successfully in [crisbal/Telegram-Bot-Node@es6](https://github.com/crisbal/Telegram-Bot-Node/tree/es6) to create a transparent data store for each plugin.

## How it works

`Rebridge()` returns an ES6 Proxy object around `{}`. When you try to read one of its properties, the getter intercepts the call, retrieves and deserializes the result from the database, and returns that instead; the same happens when you write to it.

The Proxy will forward the native methods and properties transparently, so that the objects it returns should behave the same as native objects; if this is not the case, file an issue on GitHub.

First-level objects (eg. `db.foo`) correspond to keys in the Redis database; they are serialized using JSON. When requesting deeper objects (eg. `db.foo.bar.baz`), the first-level object (`db.foo`) is deserialized to a native object, which is then accessed in the standard way.
