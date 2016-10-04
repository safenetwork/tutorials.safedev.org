# Delete a comment

If you are the website owner, you can delete comments by removing them from the appendable data associated with the current page.

#### Contents

<!-- toc -->

## Delete a comment from the appendable data

The plugin moves the comment you want to delete to the `deleted_data` field of your appendable data.

#### [DELETE /appendable-data/:handleId/:index](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#remove-from-data-by-index)

```js
this.log('Remove appendable data at :: ' + index);
window.safeAppendableData.removeAt(this.authToken, this.currentPostHandleId, index)
  .then((res) => {
    this.postAppendableData(this.currentPostHandleId)
      .then((res) => {
        console.log(res);
        clearAllDeletedData();
      }, (err) => {
        console.error(err);
      });
  }, (err) => {
    console.error(err);
    this.errorHandler(err);
  });
```

## Update the appendable data

The plugin updates your appendable data on the SAFE Network.

#### [POST /appendable-data/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#save-appendabledata)

```js
postAppendableData(handleId) {
  return window.safeAppendableData.post(this.authToken, handleId);
}
```

## Clear all deleted data from the appendable data

The plugin clears the `deleted_data` field of your appendable data.

#### [DELETE /appendable-data/clear-deleted-data/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#clear-deleted-data-section)

```js
const clearAllDeletedData = () => {
  this.log('Clear appendable deleted data');
  window.safeAppendableData.clearAll(this.authToken, this.currentPostHandleId, true)
    .then((res) => {
      post();
      console.log(res);
    }, (err) => {
      console.error(err);
      this.errorHandler(err);
    });
};
```

## Reupdate the appendable data

The plugin updates your appendable data on the SAFE Network and [reloads the comments](load-comments.md).

```js
const post = () => {
  this.log('Post appendable data');
  this.postAppendableData(this.currentPostHandleId)
    .then((res) => {
      console.log(res);
      this.clearComments();
      this.toggleSpinner(false);
      this.fetchComments();
    }, (err) => {
      console.error(err);
      this.errorHandler(err);
    });
};
```

#### [POST /appendable-data/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#save-appendabledata)

```js
postAppendableData(handleId) {
  return window.safeAppendableData.post(this.authToken, handleId);
}
```
