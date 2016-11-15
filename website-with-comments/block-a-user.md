# Block a user

If you are the website owner, you can block users by adding their signing key to the appendable data associated with the current page.

#### Contents

<!-- toc -->

![Block a user](img/block-a-user.png)

## Get the signing key of a user

The plugin fetches the signing key of the user you want to block.

#### [Get signing key by index](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#get-signing-key-of-a-data-by-index)

```
GET /appendable-data/sign-key/:handleId/:index
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L258)

```js
window.safeAppendableData.getSignKeyAt(this._authToken, this._currentPostHandleId, index)
```

## Add the signing key to the filter

The plugin adds the signing key of the user you want to block to the filter of the appendable data associated with the current page. Since the filter of the appendable data is a blacklist, everyone except the keys listed in the filter will be allowed to append data.

#### [Add signing key to filter](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#add-sign-keys-to-filter)

```
PUT /appendable-data/filter/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L260)

```js
window.safeAppendableData.addToFilter(this._authToken, this._currentPostHandleId, [signKeyHandleId])
```

## Save the appendable data

The plugin updates the appendable data associated with the current page by sending a POST request to the SAFE Network.

#### [Save AppendableData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#save-appendabledata)

```
POST /appendable-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L261)

```js
window.safeAppendableData.post(this._authToken, this._currentPostHandleId)
```

Whenever you block a user, the plugin adds their public name and their signing key to a structured data associated with the current page. Using this list of blocked users, you can [unblock a user](unblock-a-user.md) based on their public name.

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L262)

```js
this._saveBlockedUser(userName, signKeyHandleId)
```

## Serialize the signing key

The plugins serializes the signing key of the user you want to block.

#### Serialize signing key

```
GET /sign-key/serialise/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L369)

```js
window.safeSignKey.serialise(this._authToken, signKeyHandle)
```

The API returns a handle ID for the serialized signing key.

## If the structured data for blocked users is found

The plugin adds the serialized signing key of the user you want to block to the list of block users for the current page.

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L426)

```js
this._data.blockedUsers[userName] = serialisedSignKey
```

### Update the structured data

The plugin updates the structured data with the new list of block users.

#### [Update structured data](https://api.safedev.org/low-level-api/structured-data/update-structured-data.html)

```
PATCH /structured-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L427-L430)

```js
window.safeStructuredData.updateData(
    this._authToken,
    this._blockedUserStructureDataHandle,
    new Buffer(JSON.stringify(this._data.blockedUsers)).toString('base64'), this._symmetricCipherOptsHandle)
```

### Save existing structured data

The plugin saves the structured data by sending a POST request to the SAFE Network.

#### [Save structured data](https://api.safedev.org/low-level-api/structured-data/save-structured-data.html#post-endpoint)

```
POST /structured-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L431-L433)

```js
window.safeStructuredData.post(
    this._authToken,
    this._blockedUserStructureDataHandle)
```

After the user has been blocked, the plugin [reloads the comments](fetch-comments.md).

## If the structured data for blocked users is not found

The plugin creates a new structured data based on the URL of the current page. This structured data will be used to store the list of blocked users for the current page. Whenever you want to block a user, the plugin will add their public name and their signing key to that structured data.

### Create a structured data

The plugin creates a new structured data that contains the public name and the serialized signing key of the user you want to block.

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L440-L441)

```js
this._data.blockedUsers = {}
this._data.blockedUsers[userName] = serialisedSignKey
```

#### [Create structured data](https://api.safedev.org/low-level-api/structured-data/create-structured-data.html)

```
POST /structured-data
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L442-L446)

```js
window.safeStructuredData.create(
    this._authToken,
    this._getLocation() + '_blocked_users', 500,
    new Buffer(JSON.stringify(this._data.blockedUsers)).toString('base64'),
    this._symmetricCipherOptsHandle)
```

### Save new structured data

The plugin saves this new structured data by sending a PUT request to the SAFE Network.

#### [Save structured data](https://api.safedev.org/low-level-api/structured-data/save-structured-data.html#put-endpoint)

```
PUT /structured-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L448-L450)

```js
window.safeStructuredData.put(
    this._authToken,
    this._blockedUserStructureDataHandle)
```

After the user has been blocked, the plugin [reloads the comments](fetch-comments.md).

## Drop the handle for the signing key

The plugin drops the handle that represents the signing key of the user you just blocked.

#### Drop handle

```
DELETE /sign-key/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L264)

```js
window.safeSignKey.dropHandle(this._authToken, signKeyHandleId)
```
