# node-argon2 [![NPM package][npm-image]][npm-url] [![Build status][travis-image]][travis-url] [![Coverage status][coverage-image]][coverage-url] [![Code Quality][codequality-image]][codequality-url] [![Dependencies][david-dm-image]][david-dm-url]
Bindings to the reference [Argon2](https://github.com/P-H-C/phc-winner-argon2)
implementation.

**Warning: synchronous API has been removed as of 0.12, either use async/await
(recommended) or keep with 0.11.x if you need it.**

**Want to use it on command line? Instead check
[node-argon2-cli](https://github.com/ranisalt/node-argon2-cli).**

### Usage
It's possible to hash a password using both Argon2i (default) and Argon2d, sync
and async, and to verify if a password matches a hash, and also generate random
cryptographically-safe salts. Salts **must** be at least 8-byte long buffers.

To hash a password:
```js
const argon2 = require('argon2');
const salt = new Buffer('somesalt');

argon2.hash('password', salt).then(hash => {
  // ...
}).catch(err => {
  // ...
});

// OR

try {
  const hash = argon2.hashSync('password', salt);
} catch (err) {
  //...
}

// ES7

try {
  const hash = await argon2.hash('password', salt);
} catch (err) {
  //...
}
```
You can choose between Argon2i and Argon2d by passing an object as the third
argument with the `argon2d` key set to whether or not you want Argon2d:
```js
argon2.hash('password', salt, {
  argon2d: true
}.then(hash => {
  // ...
}).catch(err => {
  // internal failure
});

// OR

try {
  const hash = argon2.hashSync('password', salt, {
    argon2d: true
  });
} catch (err) {
  // internal failure
}

// ES7

try {
  const hash = await argon2.hash('password', salt, {
    argon2d: true
  });
} catch (err) {
  // internal failure
}
```
The `argon2d` option is flexible and accepts any truthy or falsy values.

You can provide your own salt as the second parameter. It is **highly**
recommended to use the salt generating methods instead of a hardcoded, constant
salt:
```js
argon2.generateSalt().then(salt => {
  // ...
});

// OR

var salt = argon2.generateSaltSync();

// ES7

const salt = await argon2.generateSalt();
```
You can also pass a desired salt length as parameter. Although the default of 16
is enough and very safe, Argon2 will use all salt bytes.
```js
argon2.generateSalt(32).then(salt => {
  // ...
});

// OR

var salt = argon2.generateSaltSync(32);

// ES7

const salt = await argon2.generateSalt(32);
```

Please keep in mind synchronous salt generation is blocking, since it waits for
entropy when enough is not available, so please refrain from using sync version.

You can also modify time, memory and parallelism constraints passing the object
as the third parameter, with keys `timeCost`, `memoryCost` and `parallelism`,
respectively defaulted to 3, 12 (meaning 2^12 KB) and 1 (threads):
```js
const options = {
  timeCost: 4, memoryCost: 13, parallelism: 2, argon2d: true
};

argon2.generateSalt().then(salt => {
  argon2.hash('password', salt, options).then(hash => {
    // ...
  });
});

// OR

const hash = argon2.hashSync('password', argon2.generateSaltSync(), options);

// ES7

const hash = await argon2.hash('password', await argon2.generateSalt(), options);
```

The default parameters for Argon2 can be accessed with `defaults`:
```js
console.log(argon2.defaults);
// => { timeCost: 3, memoryCost: 12, parallelism: 1, argon2d: false }
```

To verify a password:
```js
argon2.verify('<big long hash>', 'password').then(match => {
  if (match) {
    // password match
  } else {
    // password did not match
  }
}).catch(err => {
  // internal failure
});

// OR

try {
  if (argon2.verifySync('<big long hash>', 'password')) {
    // password match
  } else {
    // password did not match
  }
} catch (err) {
  // internal failure
}

// ES7

try {
  if (await argon2.verify('<big long hash>', 'password')) {
    // password match
  } else {
    // password did not match
  }
} catch (err) {
  // internal failure
}
```
First parameter must have been generated by an Argon2 encoded hashing method,
not raw.

When you hit an internal failure, the message is properly set. If it is not or
you do not understand it, feel free to open an issue.

### Before installing
You **MUST** have a **node-gyp** global install before proceeding with install,
along with GCC >= 4.8 / Clang >= 3.3. On Windows, you must compile under Visual
Studio 2015 or newer.

**node-argon2** works only and is tested against Node >=4.0.0.

#### OSX

To install GCC >= 4.8 on OSX, use [homebrew](http://brew.sh/):
```console
$ brew install gcc
```

Once you've got GCC installed and ready to run, you then need to install
node-gyp, you must do this globally:
```console
$ npm install -g node-gyp
```

Finally, once node-gyp is installed and ready to go, you can install this
library, specifying the GCC or Clang binary to use:

```console
$ CXX=g++-5 npm install argon2
```

**NOTE**: If your GCC or Clang binary is named something different than `g++-5`,
you'll need to specify that in the command.

# License
Work licensed under the [MIT License](LICENSE). Please check
[P-H-C/phc-winner-argon2] (https://github.com/P-H-C/phc-winner-argon2) for
license over Argon2 and the reference implementation.

[npm-image]: https://img.shields.io/npm/v/argon2.svg?style=flat-square
[npm-url]: https://www.npmjs.com/package/argon2
[travis-image]: https://img.shields.io/travis/ranisalt/node-argon2/master.svg?style=flat-square
[travis-url]: https://travis-ci.org/ranisalt/node-argon2
[coverage-image]: https://img.shields.io/coveralls/ranisalt/node-argon2/master.svg?style=flat-square
[coverage-url]: https://coveralls.io/github/ranisalt/node-argon2
[codequality-image]: https://img.shields.io/codacy/15927f4eb15747fd8a537e48a04bd4f6/master.svg?style=flat-square
[codequality-url]: https://www.codacy.com/app/ranisalt/node-argon2
[david-dm-image]: https://img.shields.io/david/ranisalt/node-argon2.svg?style=flat-square
[david-dm-url]: https://david-dm.org/ranisalt/node-argon2
