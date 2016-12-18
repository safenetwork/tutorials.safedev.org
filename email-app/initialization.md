# Initialization

First, the app needs to receive an authorization token from SAFE Launcher. Then, it needs to retrieve your email data (your email ID and your saved emails).

#### Contents

<!-- toc -->

![Initialization page](img/initialization-page.png)

## Authorize the app

The app sends an [authorization request](https://api.safedev.org/auth/) to SAFE Launcher.

#### [Authorize app](https://api.safedev.org/auth/authorize-app.html)

```
POST /auth
```

##### [initializer_actions.js](https://github.com/maidsafe/safe_examples/blob/3012144cd8f4ab0f5a4891cbbedbdf3de1641755/email_app/app/actions/initializer_actions.js#L8-L19)

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

##### [constants.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/constants.js#L44-L52)

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

Therefore, the request body looks like this:

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

The `LOW_LEVEL_API` permission is requested because the app needs to use the low-level APIs:

- [Structured Data](https://api.safedev.org/low-level-api/structured-data/)
- [Appendable Data](https://api.safedev.org/low-level-api/appendable-data/)
- [Data Identifier](https://api.safedev.org/low-level-api/data-id/)
- [Immutable Data](https://api.safedev.org/low-level-api/immutable-data/)
- [Cipher Options](https://api.safedev.org/low-level-api/cipher-options/)

> #### info::Why is it necessary to ask for this permission?
>
> By providing the `LOW_LEVEL_API` permission, the user should be aware that the app can now store data outside of SAFE Launcher's sandboxing. If the app doesn't properly keep track of the data it creates, the user might be unable to retrieve and delete data stored by the app. For example, it might be possible that some of the data created by the app cannot be deleted by the user. It's the responsibility of the app to keep track of the data it creates using the low-level API.

SAFE Launcher displays a prompt with basic information about the app along with the requested permission (`LOW_LEVEL_API`). You can authorize this request by clicking on "ALLOW".

![Authorization request](img/authorization-request.png)

After you authorize the request, the app receives an authorization token.

> #### info::What is an authorization token?
>
> Authorization tokens are used to invoke APIs that require [authorized access](https://api.safedev.org/auth/#authorized-access). These tokens are session based and thus will be valid only while SAFE Launcher is running.

## Fetch the email data

The app needs a way to store your email data on the SAFE Network. One solution is to create a private [structured data](https://api.safedev.org/low-level-api/structured-data/) that will be used by the app to store your email ID and your saved emails. Let's call it the "root structured data".

Your root structured data is created using a random ID. In order to be able to retrieve your root structured data later, you need to store its ID in a config file. This config file will be stored in the app's root directory.

During the initialization process, [the app checks if a config file is present](fetch-email-data.md#check-if-a-config-file-is-present) in its root directory. [If a config file is not found](fetch-email-data.md#if-a-config-file-is-not-found), the app creates a root structured data and a config file. [If a config file is found](fetch-email-data.md#if-a-config-file-is-found), the app tries to fetch your root structured data.
