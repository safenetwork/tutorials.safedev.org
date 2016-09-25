# Initialization

There are multiple tasks that the app needs to do during its initialization. Some of these tasks are only necessary if it's the first time you are authorizing this app with your SAFE Network account.

#### Contents

<!-- toc -->

![Initialization page](/assets/initialization-page.png)

## Authorizing the app

The app sends an [authorization request](https://maidsafe.readme.io/docs/auth) to SAFE Launcher.

#### POST [/auth](https://maidsafe.readme.io/docs/auth)

**initializer_actions.js**

```js
export const authoriseApplication = (data) => {
  return {
    type: ACTION_TYPES.AUTHORISE_APP,
    payload: {
      request: {
        method: 'post',
        url: '/auth',
        data: data
      }
    }
  };
};
```

The `authoriseApplication` function is called using `AUTH_PAYLOAD` as an argument:

**initializer.js**

```js
const AUTH_PAYLOAD = {
  app: {
    name: pkg.productName,
    vendor: pkg.author.name,
    version: pkg.version,
    id: pkg.identifier
  },
  permissions: ['LOW_LEVEL_API']
};
```

The app uses the information contained in its `package.json` file for the following fields:

* app.name
* app.vendor
* app.version
* app.id

Therefore, the authorization payload for this app looks like this:

```json
{
  "app": {
    "name": "SafeMailApp",
    "id": "safe-mail-app",
    "version": "0.0.1",
    "vendor": "MaidSafe"
  },
  "permissions": ["LOW_LEVEL_API"]
}
```

The `LOW_LEVEL_API` permission is requested because the app needs to the use the low-level APIs:

- [Data Identifier](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/0042-launcher-api-v0.6.md#handle-id)
- [Structured Data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md)
- [Immutable Data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md)
- [Appendable Data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md)

> #### info::Why is it necessary to ask for this permission?
>
> Since low-level APIs can be used to create data in the network, it might be possible where the data created by the apps cannot be deleted by the user again to retrieve the lost space. Thus, it makes it important to request user for the permission to access low-level APIs.
>
> Source: [RFC 42 – SAFE Launcher API v0.6](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/0042-launcher-api-v0.6.md#permission)

SAFE Launcher displays a prompt with basic information about the app along with the requested permission (`LOW_LEVEL_API`). You can authorize this request by clicking on "ALLOW".

![Authorization request](/assets/authorization-request.png)

After you authorize the request, the app receives an authorization token.

> #### info::What is an authorization token?
>
> Authorization tokens are used to invoke APIs that require [authorized access](https://maidsafe.readme.io/docs/introduction#section-authorised-access). These tokens are session based and thus will be valid only while SAFE Launcher is running.

## Checking for a config file

The app needs a way to store your email data on the SAFE Network. Using the [Structured Data API](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md), we can create a private structured data that will be used by the app to store your email ID and your saved emails. Let's call it the "root structure data".

The root structured data will be created using a random ID. In order to be able to retrieve the root structured data later, we need to store its ID in a config file. This config file will be stored in the app's root directory.

During the initialization process, if the app detects that you already have a config file, it will try to fetch your root structured data. If you don't have a config file, the app will create one for you.

The app attempts to retrieve a config file in its root directory:

#### GET [/nfs/file/:rootPath/:filePath](https://maidsafe.readme.io/docs/nfs-get-file)

**nfs_actions.js**

```js
export const getConfigFile = token => {
  return {
    type: ACTION_TYPES.GET_CONFIG_FILE,
    payload: {
      request: {
        url: `/nfs/file/${CONSTANTS.ROOT_PATH}/config`,
        headers: {
          'Authorization': token,
          Range: 'bytes=0-'
        },
        responseType: 'arraybuffer'
      }
    }
  };
};
```

<!-- *(Should we explain why we just fetch the first byte of the config file?)* -->

## If a config file is not found

The app creates a root structured data with a random ID. All structured data items need to have an ID that is 32 bytes long. Therefore, the app generates a 32 bytes long random ID:

**app_utils.js**

```js
export const generateCoreStructreId = () => {
  return base64.encode(crypto.randomBytes(32).toString('base64'));
};
```

The root structured data will be used to store your email ID and your saved emails. Since we use an unversioned structured data, you can store as many emails as you want.

The email data can be represented using a simple [JSON](https://en.wikipedia.org/wiki/JSON) format:

```json
{
  "id": "",
  "saved": []
}
```

### Creating a root structured data

The root structured data is encrypted using symmetric encryption. This means that no one else can read the content of your root structured data. Only you can decrypt it. Also, since we don't need versioning (we only want to show the latest data), we create an [unversioned structured data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#create) (type 500).

#### POST [/structuredData/:id](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#create)

**core_structure_actions.js**

```js
export const createCoreStructure = (token, id, data) => ({
  type: ACTION_TYPES.CREATE_CORE_STRUCTURE,
  payload: {
    request: {
      method: 'post',
      url: `/structuredData/${id}`,
      headers: {
        'Tag-Type': CONSTANTS.TAG_TYPE.DEFAULT,
        Encryption: CONSTANTS.ENCRYPTION.SYMMETRIC,
        'Authorization': token,
        'Content-Type': 'text/plain'
      },
      data: new Uint8Array(new Buffer(JSON.stringify(data)))
    }
  }
});
```

### Creating a config file

After the structured data is successfully created, the app stores its ID in a config file. That way, the app will be able to retrieve your email data in the future.

The config file is stored in the app's root directory, which is private. Therefore, the config file will be automatically encrypted and no one else will be able to read it.

#### POST [/nfs/file/:rootPath/:filePath](https://maidsafe.readme.io/docs/nfsfile)

**nfs_actions.js**

```js
export const writeConfigFile = (token, coreId) => {
  return {
    type: ACTION_TYPES.WRITE_CONFIG_FILE,
    payload: {
      request: {
        method: 'post',
        url: `/nfs/file/${CONSTANTS.ROOT_PATH}/config`,
        headers: {
          'Authorization': token,
          'Content-Type': 'plain/text'
        },
        data: new Uint8Array(Buffer(coreId))
      }
    }
  }
};
```

After the config file is successfully created, the app transitions to the Create Account page.

![Create Account page](/assets/create-account-page.png)

## If a config file is found

The app fetches your email data (email ID and saved emails) using the ID stored in the config file.

Before fetching your root structured data, the app needs to obtain a structured data handle using the Data Identifier API.

<!-- *(explain why handles are needed?)* -->

#### GET [/structuredData/handle/:id](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#get-data-identifier-handle)

**core_structure_actions.js**

```js
export const fetchCoreStructureHandler = (token, id) => ({
  type: ACTION_TYPES.FETCH_CORE_STRUCTURE_HANDLER,
  payload: {
    request: {
      url: `/structuredData/handle/${id}`,
      headers: {
        'Tag-Type': CONSTANTS.TAG_TYPE.DEFAULT,
        'Authorization': token
      }
    }
  }
});
```

### Fetching the root structured data

After the structured data handle is successfully retrieved, the app fetches the root structured data that contains your email data.

#### GET [/structuredData/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#read-data)

**core_structure_actions.js**

```js
export const fetchCoreStructure = (token, id) => ({
  type: ACTION_TYPES.FETCH_CORE_STRUCTURE,
  payload: {
    request: {
      url: `/structuredData/${id}`,
      headers: {
        'Authorization': token,
        'Tag-Type': CONSTANTS.TAG_TYPE.DEFAULT,
        Encryption: CONSTANTS.ENCRYPTION.SYMMETRIC,
        'Content-Type': 'text/plain'
      }
    }
  }
});
```

#### If the structured data doesn't contain an email ID

If you hadn't created an email ID yet, the app transitions to the Create Account page.

![Create Account page](/assets/create-account-page.png)

#### If the structured data contains an email ID

If you had already created an email ID, the app transitions to the Inbox page.

![Inbox page](/assets/inbox-page.png)
