# Fetch comments

The plugin fetches the comments associated with the current page and adds them to the UI.

#### Contents

<!-- toc -->

![Fetch comments](img/comments-plugin.png)

## Get a data identifier handle

The plugin fetches a data identifier handle for the appendable data associated with the current page.

#### [Get data ID handle](https://api.safedev.org/low-level-api/data-id/get-data-id-handle.html#for-appendable-data)

```
POST /data-id/appendable-data
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L190)

```js
window.safeDataId.getAppendableDataHandle(this._authToken, this._getLocation())
```

The name of the appendable data is based on the URL of the current page.

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L312-L317)

```js
_getLocation () {
  if (this._isDevMode() && this._data.user.dns) {
    return `comments-dev-${this._data.user.dns}/${window.location.pathname}`
  }
  return `${this._hostName}/${window.location.pathname}`
}
```

#### Example

```
blog.example//post.html
```

The actual name of the appendable data is the hash of `_getLocation()`.

## Get an appendable data handle

The plugin fetches an appendable data handle using the data identifier handle previously obtained.

#### [Get appendable data handle](https://api.safedev.org/low-level-api/appendable-data/get-appendable-data-handle.html)

```
GET /appendable-data/handle/:dataIdHandle
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L163-L164)

```js
window.safeAppendableData.getHandle(
    this._authToken, dataHandleId)
```

## Get the metadata of the appendable data

The plugin fetches the metadata of the appendable data using the appendable data handle. The length of the appendable data represents the number of comments for the current page.

#### [Get appendable data metadata](https://api.safedev.org/low-level-api/appendable-data/get-appendable-data-metadata.html)

```
GET /appendable-data/metadata/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L155-L156)

```js
window.safeAppendableData.getMetadata(
    this._authToken, this._currentPostHandleId)
```

If the data length is 0, it means that the current page doesn't have any comments.

## Iterate through the appendable data

If the data length is greater than 0, the plugin fetches all the comments contained inside the appendable data.

## Get a data identifier handle from the appendable data

The plugin fetches a data identifier handle from the appendable data based on the current index.

#### [Get data ID handle at index](https://api.safedev.org/low-level-api/appendable-data/get-data-id-handle-at-index.html#for-a-data-item)

```
GET /appendable-data/:handleId/:index
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L391-L392)

```js
window.safeAppendableData.getDataIdAt(
    this._authToken, this._currentPostHandleId, index)
```

## Permanent comments

If the current page is using the [Permanent Comments Plugin](https://github.com/maidsafe/safe_examples/tree/master/permanent_comments_plugin), the plugin will expect the comments inside the appendable data to be stored as immutable data.

### Get an immutable data reader handle

The plugin fetches the data map of the comment using the data identifier handle of the current comment.

#### [Get immutable data reader handle](https://api.safedev.org/low-level-api/immutable-data/get-immutable-data-handle.html#get-immutable-data-reader-handle)

```
GET /immutable-data/reader/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L379)

```js
window.safeImmutableData.getReaderHandle(this._authToken, address)
```

The API returns an immutable data reader handle.

### Read the immutable data

The plugin reads the immutable data representing the comment using the immutable data reader handle.

#### [Read immutable data](https://api.safedev.org/low-level-api/immutable-data/read-immutable-data.html)

```
GET /immutable-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L381)

```js
window.safeImmutableData.read(this._authToken, handleId)
```

### Drop the immutable data reader handle

The plugin drops the immutable data reader handle.

#### [Drop immutable data reader handle](https://api.safedev.org/low-level-api/immutable-data/drop-immutable-data-handle.html#drop-immutable-data-reader-handle)

```
DELETE /immutable-data/reader/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L383)

```js
window.safeImmutableData.dropReader(this._authToken, hId)
```

## Editable comments

If the current page is using the [Editable Comments Plugin](https://github.com/maidsafe/safe_examples/tree/master/editable_comments_plugin), the plugin will expect the comments inside the appendable data to be stored as structured data.

### Get a structured data handle

The plugin fetches a structured data handle using the data identifier handle of the current comment.

#### [Get structured data handle](https://api.safedev.org/low-level-api/structured-data/get-structured-data-handle.html)

```
GET /structured-data/handle/:dataIdHandle
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L461)

```js
window.safeStructuredData.getHandle(this._authToken, address)
```

### Fetch the structured data

The plugin fetches the content of the structured data using the structured data handle.

#### [Read structured data](https://api.safedev.org/low-level-api/structured-data/read-structured-data.html)

```
GET /structured-data/:handleId/:version?
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L463)

```js
window.safeStructuredData.readData(this._authToken, payload.handleId)
```

### Drop the structured data handle

The plugin drops the structured data handle of the current comment.

#### [Drop structured data handle](https://api.safedev.org/low-level-api/structured-data/drop-structured-data-handle.html)

```
DELETE /structured-data/handle/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/editable_comments_plugin/comments/src/controller.js#L469)

```js
window.safeStructuredData.dropHandle(this._authToken, hId)
```

## Drop the data identifier handle

The plugin drops the data identifier handle of the current comment.

#### [Drop data ID handle](https://api.safedev.org/low-level-api/data-id/drop-data-id-handle.html)

```
DELETE /data-id/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L396)

```js
window.safeDataId.dropHandle(this._authToken, dataIdHandle)
```

The plugin continues iterating through the appendable data until all the comments it contains have been fetched. The plugin then sorts them by time and adds them to the UI.

## Drop the data identifier handle for appendable data

The plugin drops the data identifier handle of the appendable data associated with the current page.

#### [Drop data ID handle](https://api.safedev.org/low-level-api/data-id/drop-data-id-handle.html)

```
DELETE /data-id/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L194)

```js
window.safeDataId.dropHandle(this._authToken, dataIdHandle)
```
