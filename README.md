This is an attempt to use [snowpack](https://www.snowpack.dev/) with a yet-to-be-released version of the PureScript compiler that produces ES Module output. You can create a custom build of this compiler version from [this PR](https://github.com/purescript/purescript/pull/3791).

This project is based on the official [halogen template](https://github.com/purescript-halogen/purescript-halogen-template), but uses snowpack instead of parcel.

### Quick Start
```sh
git clone --branch snowpack-issues https://github.com/milesfrain/purescript-halogen-template-1.git snowpack-demo
cd snowpack-demo
npm install
npm run start
```

Unfortunately, this does not work yet because libraries still use CommonJS exports, but snowpack needs ES Modules.

You'll likely see this error in the browser terminal:
```
Uncaught SyntaxError: indirect export not found: unsafeHas
```

This particular error is complaining about [this file](https://github.com/purescript/purescript-prelude/blob/master/src/Record/Unsafe.js):

```js
//output/Record.Unsafe/foreign.js
exports.unsafeHas = function (label) {
  return function (rec) {
    return {}.hasOwnProperty.call(rec, label);
  };
};
```

It should work if it was written like this, but that requires a change to how FFI code works.

```js
//output/Record.Unsafe/foreign.js
const unsafeHas = function (label) {
  return function (rec) {
    return {}.hasOwnProperty.call(rec, label);
  };
};
export {
  unsafeHas
};
```
