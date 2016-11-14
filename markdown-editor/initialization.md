# Initialization

First, the app needs to receive an authorization token from SAFE Launcher. Then, it needs to obtain a cipher options handle and load a config file. Finally, it needs to load the list of Markdown files using the information obtained in the config file.

#### Contents

<!-- toc -->

![Authorize app](img/authorize-app.png)

## Authorize the app

The app sends an [authorization request](https://api.safedev.org/auth/) to SAFE Launcher.

#### [Authorize app](https://api.safedev.org/auth/authorize-app.html)

```
POST /auth
```

##### [store.js](https://github.com/shankar2105/safe_examples_private/blob/ben_versioning_editor/versioning_editor/src/store.js#L77-L84)

```js
safeAuth.authorise({
  'name': APP_NAME,
  'id': APP_ID,
  'version': APP_VERSION,
  'vendor': 'MaidSafe Ltd.',
  'permissions': ['LOW_LEVEL_API']
},
APP_ID)
```

For this example app, `APP_ID` has been set to `net.maidsafe.examples.versioning-editor`.

##### [store.js](https://github.com/shankar2105/safe_examples_private/blob/ben_versioning_editor/versioning_editor/src/config.js#L12)

```js
export const APP_ID = 'net.maidsafe.examples.versioning-editor';
```

SAFE Launcher displays a prompt with basic information about the app along with the requested permission (`LOW_LEVEL_API`). You can authorize this request by clicking on "ALLOW".

![Authorization](img/authorization.png)

After you authorize the request, the app receives an authorization token.

## Obtain a cipher options handle

The app fetches a cipher options handle for symmetric encryption. It will be used to encrypt your Markdown files. That way, only you will be able to read them.

#### [Get Cipher-Opts Handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/cipher_opts.md#get-cipher-opts-handle)

```
GET /cipher-opts/:encType/:keyHandle?
```

##### [store.js](https://github.com/shankar2105/safe_examples_private/blob/ben_versioning_editor/versioning_editor/src/store.js#L31-L32)

```js
safeCipherOpts.getHandle(ACCESS_TOKEN,
  window.safeCipherOpts.getEncryptionTypes().SYMMETRIC)
```

## Load config file

##### [store.js](https://github.com/shankar2105/safe_examples_private/blob/ben_versioning_editor/versioning_editor/src/store.js#L43-L46)

```js
safeNFS.createFile(ACCESS_TOKEN,
  FILE_NAME,
  JSON.stringify({ 'user_prefix': _createRandomUserPrefix() }), 'application/json')
```

##### [store.js](https://github.com/shankar2105/safe_examples_private/blob/ben_versioning_editor/versioning_editor/src/store.js#L21-L28)

```js
const _createRandomUserPrefix = () => {
  let randomString = '';
  for (var i = 0; i < 10; i++) {
    // and ten random ascii chars
    randomString += String.fromCharCode(Math.floor(Math.random(100) * 100));
  }
  return btoa(`${APP_ID}@${APP_VERSION}#${(new Date()).getTime()}-${randomString}`);
};
```

##### [store.js](https://github.com/shankar2105/safe_examples_private/blob/ben_versioning_editor/versioning_editor/src/store.js#L48)

```js
safeNFS.getFile(ACCESS_TOKEN, FILE_NAME)
```

## Load file index

Load file index.
