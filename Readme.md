# npm-path

### Set the `$PATH` as if you were within an `npm run`.

`npm-path` replicates the behaviour of `npm` when it changes the `$PATH` to execute a lifecycle script
e.g. `npm install` `npm test`, `npm run <scriptname>`.


#### `npm-path` will set your `$PATH` to include:

* All of the `node_modules/.bin` directories from the current directory, up through all of its parents. This allows you to invoke the executables for any installed modules. e.g. if `mocha` is installed a dependency of the current module, then `mocha` will be available on a `npm-path` generated `$PATH`.
* The directory containing the current `node` executable, so any scripts that invoke `node` will execute the same `node`.
* Current npm's `node-gyp` directory, so the `node-gyp` bundled with `npm` can be used.

## Usage

### Command-line

Calling `npm-path` from the commandline is the equivalent of executing an npm script with the body `echo $PATH`.

```bash
> npm-path
# /usr/local/lib/node_modules/npm/bin/node-gyp-bin:/Users/timoxley/Projects/npm-path/node_modules/.bin etc

# Example
# Setting the npm enhanced PATH before running a script

# PATH Before:
> node -e "console.log(process.env.PATH)"
/Users/timoxley/bin:/usr/local/bin:/usr/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin

# PATH After:
> PATH=`npm-path` node -e "console.log(process.env.PATH)"
/usr/local/lib/node_modules/npm/bin/node-gyp-bin:/Users/timoxley/Projects/npm-path/node_modules/.bin:/usr/local/bin:/Users/timoxley/bin:/usr/local/bin:/usr/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin
```

### Set PATH

This will set the 'npm enhanced' PATH.

```js

var npmPath = require('npm-path')

// Asynchronously

npmPath(function(err, $PATH) {
  console.log($PATH)
})

// Optional options
var options = {
  env: process.env, // Environment to use. `process.env` is the default.
  cwd: process.cwd() // Working directory. `process.cwd()` is the default.
}

npmPath(options, function(err, $PATH) {
  console.log($PATH)
})

// `npmPath.set` is a more explicit alternative to `npmPath`
console.log(npmPath.set == npmPath) // true
npmPath.set(options, function(err, $PATH) {
  // ...
})

// Synchronously

var $PATH = npmPath() // passing no function will execute synchronously
var $PATH = npmPath.setSync() // more explicit alternative

console.log($PATH)
// /usr/local/lib/node_modules/npm/bin/node-gyp-bin:/Users/timoxley/Projects/npm-path/node_modules/.bin:/usr/local/bin: ...etc

```

### Get $PATH

This will get the 'npm enhanced' path, but *does not modify the $PATH*.
Takes the exact same options as when setting the path.

```js

// Asynchronously

npmPath.get(function(err, $PATH) {
  console.log($PATH)
  // did not modify process.env[PATH]
})

// options is optional, takes same options as `npmPath.set`
npmPath.get(options, function(err, $PATH) {
  console.log($PATH)
})

// Synchronously

var $PATH = npmPath.getSync()

```

### Options

Both `set` and `get` takes an optional options object, with optional `env` and `cwd` keys.

* Set `options.env` if you wish to use something other than `process.env` (the default)
* Set `options.cwd` if you wish to use something other than `process.cwd()` (the default)

There's also a `options.npm` property which you can set if you want `node-gyp` to be sourced from
an alternative `npm` installation.

### Get the PATH environment variable key

```js
// windows calls it's path "Path" usually, but this is not guaranteed.
npmPath.PATH // 'Path', probably

// rest of the world
npmPath.PATH // 'PATH'

// Usage:
process.env[npmPath.PATH] // get path environment variable

// set path environment variable manually
process.env[npmPath.PATH] = npmPath.get()

// set path environment variable automatically
npmPath()
```

### Get the PATH separator

```js
// windows
npmPath.SEPARATOR // ';'

// rest of the world
npmPath.SEPARATOR // ':'
```

# License

MIT
