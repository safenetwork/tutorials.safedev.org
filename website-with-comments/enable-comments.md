# Enable comments

You can enable comments on your webpage by clicking on the `Enable Comments` button. This ensures that the website owner becomes the owner of the `AppendableData` used to hold the comments.

#### Contents

<!-- toc -->

![Enable comments](img/enable-comments.png)

## Check if the user is an admin

The plugin verifies that the current user is an admin by comparing the public name of the current webpage to the public names owned by the user. If the public name of the current webpage is not contained in the list of public names owned by the user, it means that the user is not an admin of that webpage.

```js
isAdmin() {
  let currentDns = this.hostName.replace(/(^\w+\.|.safenet$)/g, '');
  if (!this.user.dns) {
    return;
  }
  return this.user.dns.indexOf(currentDns) !== -1;
}
```

## Create an appendable data

The plugin creates an appendable data for the current page. By default, the filter of the appendable data will be a blacklist (as opposed to a whitelist). Everyone except the keys listed in the filter will be allowed to append data.

#### [POST /appendable-data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#create)

```js
const createAppendableData = () => {
  this.log('Create appendable data');
  window.safeAppendableData.create(this.authToken, this.getLocation(), false)
    .then((res) => {
      put(res.__parsedResponseBody__.handleId);
    }, (err) => {
      this.errorHandler(err);
      console.error(err);
    });
};
```

The name of the appendable data is based on the URL of the current page.

```js
getLocation() {
  return `${this.hostName}/${window.location.pathname}`;
}
```

#### Example

```
blog.testing//post.html
```

The actual name of the appendable data will be the hash of `getLocation()`.

## Save the appendable data

The plugin saves the appendable data to the SAFE Network and enables the comment UI.

```js
const put = (handleId) => {
  this.log('Put appendable data');
  this.putAppendableData(handleId)
    .then((res) => {
      this.toggleEnableCommentBtn(false);
      this.toggleComments(true);
      this.currentPostHandleId = handleId;
      this.toggleSpinner(false);
    }, (err) => {
      console.error(err);
      this.errorHandler(err);
    });
};
```

#### [PUT /appendable-data/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#save-appendabledata)

```js
putAppendableData(handleId) {
  return window.safeAppendableData.put(this.authToken, handleId);
}
```
