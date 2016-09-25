# Checking for new emails

When someone sends you a message, it gets stored in the appendable data corresponding to the hash of your email ID. When you refresh your inbox, the app fetches your appendable data and returns all the emails it contains.

#### Contents

<!-- toc -->

![Inbox page](/assets/inbox-page.png)

## Fetching the appendable data handle

The app fetches the data identifier handle corresponding to your appendable data:

**app_utils.js**

```js
export const hashEmailId = emailId => {
  return crypto.createHash('sha256').update(emailId).digest('base64');
};
```

#### GET [/appendableData/handle/:id](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#get-data-identifier-handle)

**appendable_data_actions.js**

```js
export const fetchAppendableDataHandler = (token, id) => { // id => appendable data id
  return {
    type: ACTION_TYPES.FETCH_APPENDABLE_DATA_HANDLER,
    payload: {
      request: {
        url: `/appendableData/handle/${id}`,
        headers: {
          'Authorization': token,
          'Is-Private': true
        }
      }
    }
  };
};
```

## Fetching the appendable data

The app fetches the metadata of your appendable data. If the data length is 0, it means your inbox is empty.

#### HEAD [/appendableData/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#read-appendable-data)

**appendable_data_actions.js**

```js
export const fetchAppendableData = (token, handlerId) => {
  return {
    type: ACTION_TYPES.FETCH_APPENDABLE_DATA,
    payload: {
      request: {
        method: 'head',
        url: `/appendableData/${handlerId}`,
        headers: {
          'Authorization': token
        }
      }
    }
  };
};
```

### Iterating through the appendable data

If the data length is greater than 0, the app iterates through the appendable data. It starts at index 0 and fetches the data identifier corresponding to the first email.

#### GET [/appendableData/:handleId/:index](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#read-appendable-data)

**appendable_data_actions.js**

```js
export const fetchDataIdAt = (token, handlerId, index) => ({
  type: ACTION_TYPES.FETCH_DATA_ID_AT,
  payload: {
    request: {
      url: `/appendableData/${handlerId}/${index}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

### Fetching the email

The app fetches the immutable data by using the data identifier of the first email.

#### GET [/immutableData/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#read-using-self-encryptor)

```js
export const fetchMail = (token, handleId) => ({
  type: ACTION_TYPES.FETCH_MAIL,
  payload: {
    request: {
      url: `/immutableData/${handleId}`,
      headers: {
        'Authorization': token
      },
      responseType: 'arraybuffer'
    }
  }
});
```

It adds the content of the first email to your inbox and then repeats this process for the second email (if there is one). It continues iterating through the appendable data until all your emails have been fetched.

**mail_inbox.js**

```js
fetchMail(handlerId) {
  const { token, fetchMail, pushToInbox } = this.props;
  fetchMail(token, handlerId)
    .then(res => {
      if (res.error) {
        return showError('Fetch Mail Error', res.error.message);
      }
      const data = new Buffer(res.payload.data).toString();
      pushToInbox(JSON.parse(data));
      this.currentIndex++;
      if (this.dataLength === this.currentIndex) {
        return this.dropAppendableData();
      }

      return this.iterateAppendableData();
    });
}
```

## Getting the length of the appendable data

To update the amount of space used by the emails in your appendable data, the app fetches the serialized content of your appendable data and measures its length.

#### GET /appendableData/serialise/:handleId

**appendable_data_actions.js**

```js
export const getAppendableDataLength = (token, handleId) => ({
  type: ACTION_TYPES.GET_APPENDABLE_DATA_LENGTH,
  payload: {
    request: {
      url: `/appendableData/serialise/${handleId}`,
      headers: {
        'Authorization': token
      },
      responseType: 'arraybuffer'
    }
  }
});
```

## Dropping the appendable data

After refreshing your inbox, the app drops the data identifier handle corresponding to your appendable data.

#### DELETE [/dataId/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#drop-handle)

**data_handle_actions.js**

```js
export const dropHandler = (token, handleId) => ({
  type: ACTION_TYPES.DROP_HANDLER,
  payload: {
    request: {
      method: 'delete',
      url: `/dataId/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```
