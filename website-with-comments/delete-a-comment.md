# Delete a comment

If you are the website owner, you can delete comments by removing them from the appendable data associated with the current page.

#### Contents

<!-- toc -->

![Delete a comment](img/block-a-user.png)

## Delete a comment from the appendable data

The plugin moves the comment you want to delete to the `deleted_data` field of the appendable data associated with the current page.

#### [Remove a data item](https://api.safedev.org/low-level-api/appendable-data/remove-data-at-index.html#remove-a-data-item)

```
DELETE /appendable-data/:handleId/:index
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L241)

```js
window.safeAppendableData.removeAt(this._authToken, this._currentPostHandleId, index)
```

## Update the appendable data

The plugin updates the appendable data associated with the current page by sending a POST request to the SAFE Network.

#### [Save appendable data](https://api.safedev.org/low-level-api/appendable-data/save-appendable-data.html#post-endpoint)

```
POST /appendable-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L242)

```js
window.safeAppendableData.post(this._authToken, this._currentPostHandleId)
```

## Clear all deleted data from the appendable data

The plugin clears the `deleted_data` field of the appendable data associated with the current page.

#### [Clear all deleted data items](https://api.safedev.org/low-level-api/appendable-data/clear-all-data.html#clear-all-deleted-data-items)

```
DELETE /appendable-data/clear-deleted-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L245)

```js
window.safeAppendableData.clearAll(this._authToken, this._currentPostHandleId, true)
```

## Update the appendable data (again)

The plugin updates the appendable data associated with the current page by sending a POST request to the SAFE Network.

#### [Save appendable data](https://api.safedev.org/low-level-api/appendable-data/save-appendable-data.html#post-endpoint)

```
POST /appendable-data/:handleId
```

##### [controller.js](https://github.com/maidsafe/safe_examples/blob/19cb638c3f02a4b9b9492e44f1527f6010c8e9ba/permanent_comments_plugin/comments/src/controller.js#L246)

```js
window.safeAppendableData.post(this._authToken, this._currentPostHandleId)
```

The plugin then [reloads the comments](fetch-comments.md).
