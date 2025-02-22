# Prerequisites

```
sudo apt install python build-essential curl
```

# node-lib

Node.js library for encrypted file transfer on the StorXNetwork network via bindings to [CLI](https://github.com/StorXNetwork/libstorx).

## Example Usage

Install via npm:
```
npm install github:StorXNetwork/node-lib --save
```

Please see [`./examples`](/examples) directory for example code usage.

First setup the storj environment with authentication and encryption options:

```js
const { Environment } = require('storj');

const storj = new Environment({
  bridgeUrl: 'https://api.storx.io',
  bridgeUser: 'user@domain.com',
  bridgePass: 'password',
  encryptionKey: 'abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about',
  logLevel: 4
});
```

The `encryptionKey` is a randomly generated key used for file encryption, new keys can be randomly generated with the `mnemonicGenerate` method. The `bridgeUrl`, `bridgeUser` and `bridgePass` are the user credentials for authenticating to a Storj bridge.

Upload a file to a bucket:
```js
const bucketId = '368be0816766b28fd5f43af5';
const filePath = './storj-test-upload.data';

const state = storj.storeFile(bucketId, filePath, {
  filename: 'storj-test-upload.data',
  progressCallback: function(progress, downloadedBytes, totalBytes) {
    console.log('progress:', progress);
  },
  finishedCallback: function(err, fileId) {
    if (err) {
      return console.error(err);
    }
    console.log('File complete:', fileId);
  }
});

```

Download a file from a bucket:

```js
const bucketId = '368be0816766b28fd5f43af5';
const fileId = '998960317b6725a3f8080c2b';
const downloadFilePath = './storj-test-download.data';

const state = storj.resolveFile(bucketId, fileId, downloadFilePath, {
  progressCallback: function(progress, downloadedBytes, totalBytes) {
    console.log('progress:', progress)
  },
  finishedCallback: function(err) {
    if (err) {
      return console.error(err);
    }
    console.log('File download complete');
  }
});
```

Once finished, you should call to zero and free memory holding encryption keys:

```js
storj.destroy();
```

Please see [`./examples`](/examples) directory for further usage.

## API

- `.Environment(options)` - A constructor for keeping encryption options and other environment settings, see available methods below
- `.mnemonicGenerate(bits)` - Will create a new *Encryption Key* string for file encryption/decryption
- `.mnemonicCheck(encryptionKey)` - Will return boolean to verify that an *Encryption Key* hasn't been typed incorrectly by verifying the checksum and format
- `.utilTimestamp()` - Returns current unix timestamp in milliseconds

Methods available on an instance of `Environment`:

- `.getInfo(function(err, result) {})` - Gets general API info`
- `.getBuckets(function(err, result) {})` - Gets list of available buckets
- `.createBucket(bucketName, function(err, result) {})` - Creates a bucket
- `.deleteBucket(bucketId, function(err, result) {})` - Deletes a bucket
- `.getBucketId(bucketName, function(err, result) {})` - Gets a bucket id by name
- `.listFiles(bucketId, function(err, result) {})` - List files in a bucket
- `.storeFile(bucketId, filePath, options)` - Uploads a file, returns state object
- `.storeFileCancel(state)` - This will cancel an upload
- `.resolveFile(bucketId, fileId, filePath, options)` - Downloads a file, return state object
- `.resolveFileCancel(state)` - This will cancel a download
- `.deleteFile(bucketId, fileId, function(err, result) {})` - Deletes a file from a bucket
- `.destroy()` - This will zero and free memory of encryption keys and the environment

## Development & Testing

If you do not already have `libstorx` installed on your system, you can install from the directions at https://github.com/StorXNetwork/libstorx This isn't required, however it's best for development.

If you do not have `node-gyp` installed:

```
$ npm install -g node-gyp
```

To build:

```
$ npm install
$ node-gyp build
```

To test:

```
$ npm run test
```

To test with gdb for debugging:
```
$ gdb --args node node_modules/.bin/_mocha -R spec test/index.test.js
```

## License

Copyright (C) 2017 Storj Labs, Inc

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Lesser General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Lesser General Public License for more details.

You should have received a copy of the GNU Lesser General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
