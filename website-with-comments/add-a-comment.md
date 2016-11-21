# Add a comment

You can add comments by appending them to the appendable data associated with the current page.

#### Contents

<!-- toc -->

![Add a comment](img/add-a-comment.png)

## Permanent comments

If the current page is using the [Permanent Comments Plugin](https://github.com/maidsafe/safe_examples/tree/master/permanent_comments_plugin), the plugin will store your new comment using the [Immutable Data API](https://api.safedev.org/low-level-api/immutable-data/). This means that once a comment is posted, it can't be edited (immutable data doesn't have any owner).

### Get an immutable data writer handle

The plugin fetches an immutable data writer handle.

#### [Get immutable data writer handle](https://api.safedev.org/low-level-api/immutable-data/get-immutable-data-handle.html#get-immutable-data-writer-handle)

```
GET /immutable-data/writer
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L214)

```js
window.safeImmutableData.getWriterHandle(this._authToken)
```

### Write immutable data

The plugin stores the comment as immutable data using the immutable data writer handle.

#### [Write immutable data](https://api.safedev.org/low-level-api/immutable-data/write-immutable-data.html)

```
POST /immutable-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L216)

```js
window.safeImmutableData.write(this._authToken, writerHandle, payload)
```

The payload parameter contains the public name you selected, your comment and the current timestamp in JSON format.

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L206-L210)

```js
const payload = new Buffer(JSON.stringify({
  name: publicName,
  comment: comment,
  time: timeStamp
}));
```

#### Example

```json
{
  "name": "example",
  "comment": "Hello, world!",
  "time": 1476741155526
}
```

### Close the immutable data writer

The plugin saves the data map of the comment as immutable data on the SAFE Network.

#### [Close immutable data writer](https://api.safedev.org/low-level-api/immutable-data/close-immutable-data-writer.html)

```
PUT /immutable-data/:handleId/:cipherOptsHandle
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L220)

```js
window.safeImmutableData.closeWriter(this._authToken, writerHandle)
```

## Editable comments

If the current page is using the [Editable Comments Plugin](https://github.com/maidsafe/safe_examples/tree/master/editable_comments_plugin), the plugin will store your new comment using the [Structured Data API](https://api.safedev.org/low-level-api/structured-data/). This means that users will be able to edit their own comments.

### Create a structured data

The plugin fetches a structured data handle for the comment you want to add.

#### [Create structured data](https://api.safedev.org/low-level-api/structured-data/create-structured-data.html)

```
POST /structured-data
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L254)

```js
window.safeStructuredData.create(this._authToken, name, 501, payload)
```

#### Parameters

1. The name parameter will be the public name you selected combined with the current timestamp and a random string. The actual ID of the structured data will be the hash of the name parameter.

2. The type tag will be 501 (versioned) because the editable comments plugin lets you see the previous versions of each comment.

3. The payload parameter will contain the public name you selected, your comment and the current timestamp in JSON format (stored as a base64 string).

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L243-L249)

```js
const timeStamp = (new Date()).getTime()
const name = publicName + timeStamp + generateRandomString()
const payload = new Buffer(JSON.stringify({
  name: publicName,
  comment: comment,
  time: timeStamp
})).toString('base64')
```

#### Example

```json
{
  "name": "example",
  "comment": "test 123",
  "time": 1475234797311
}
```

### Save the structured data

The plugin saves the structured data representing your comment to the SAFE Network.

#### [Save structured data](https://api.safedev.org/low-level-api/structured-data/save-structured-data.html#put-endpoint)

```
PUT /structured-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L256)

```js
window.safeStructuredData.put(this._authToken, currentSDHandleId)
```

### Get a data identifier handle

The plugin fetches a data identifier handle using the structured data handle representing your comment.

#### [Get data ID handle](https://api.safedev.org/low-level-api/structured-data/get-data-id-handle.html)

```
GET /structured-data/data-id/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L260)

```js
window.safeStructuredData.getDataIdHandle(this._authToken, currentSDHandleId)
```

## Append the comment to the appendable data

The plugin appends the data identifier handle representing your comment to the appendable data of the current page.

#### [Append data](https://api.safedev.org/low-level-api/appendable-data/append-data.html)

```
PUT /appendable-data/:handleId/:dataIdHandle
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L222)

```js
window.safeAppendableData.append(this._authToken, this._currentPostHandleId, dataIdHandle)
```

### Drop the data identifier handle

The plugin drops the data identifier handle representing your comment.

#### [Drop data ID handle](https://api.safedev.org/low-level-api/data-id/drop-data-id-handle.html)

```
DELETE /data-id/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L224)

```js
window.safeDataId.dropHandle(this._authToken, dataIdHandle)
```

### Drop the immutable data writer handle

If the current page is using the [Permanent Comments Plugin](https://github.com/maidsafe/safe_examples/tree/master/permanent_comments_plugin), the plugin drops the immutable data writer handle.

#### [Drop immutable data writer handle](https://api.safedev.org/low-level-api/immutable-data/drop-immutable-data-handle.html#drop-immutable-data-writer-handle)

```
DELETE /immutable-data/writer/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L230)

```js
window.safeImmutableData.dropWriter(this._authToken, writerHandle)
```

### Drop the structured data handle

If the current page is using the [Editable Comments Plugin](https://github.com/maidsafe/safe_examples/tree/master/editable_comments_plugin), the plugin drops the structured data handle.

#### [Drop structured data handle](https://api.safedev.org/low-level-api/structured-data/drop-structured-data-handle.html)

```
DELETE /structured-data/handle/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L267)

```js
window.safeStructuredData.dropHandle(this._authToken, currentSDHandleId)
```

After successfully adding your comment to the appendable data of the current page, the plugin [reloads the comments](fetch-comments.md).
