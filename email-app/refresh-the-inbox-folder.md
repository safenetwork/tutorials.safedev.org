# Refresh the inbox folder

When someone sends you a message, it gets stored in the appendable data corresponding to the hash of your email ID. When you refresh your inbox folder, the app fetches your appendable data and returns all the emails it contains.

#### Contents

<!-- toc -->

![Inbox page](img/inbox-page.png)

## Get the appendable data of the inbox folder

The app needs to obtain an appendable data handle.

### Get a data identifier handle

First, the app fetches a data identifier handle for the appendable data representing your inbox folder.

#### [Get DataIdentifier for AppendableData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/data_identifier.md#get-dataidentifier-for-appendabledata)

```
POST /data-id/appendable-data
```

##### [data_id_handle_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/data_id_handle_actions.js#L22-L37)

```js
export const getAppendableDataIdHandle = (token, name) => ({
  type: ACTION_TYPES.GET_STRUCTURED_DATA_ID_HANDLE,
  payload: {
    request: {
      method: 'post',
      url: '/data-id/appendable-data',
      headers: {
        'Authorization': token
      },
      data: {
        isPrivate: true,
        name
      }
    }
  }
});
```

The name of the appendable data is obtained by hashing your email ID:

##### [app_utils.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/utils/app_utils.js#L27-L29)

```js
export const hashEmailId = emailId => {
  return crypto.createHash('sha256').update(emailId).digest('base64');
};
```

### Get an appendable data handle

The app fetches an appendable data handle using the data identifier handle representing your inbox folder.

#### [Get AppendableData handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#get-appendabledata-handle-from-dataidentifier-handle)

```
GET /appendable-data/handle/:dataIdHandle
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L39-L52)

```js
export const fetchAppendableDataHandle = (token, dataIdHandle) => { // id => appendable data id
  return {
    type: ACTION_TYPES.FETCH_APPENDABLE_DATA_HANDLER,
    payload: {
      request: {
        url: `/appendable-data/handle/${dataIdHandle}`,
        headers: {
          'Authorization': token,
          'Is-Private': true
        }
      }
    }
  };
};
```

### Drop the data identifier handle

The app drops the data identifier handle for the appendable data representing your inbox folder.

#### [Drop data ID handle](https://api.safedev.org/low-level-api/data-id/drop-data-id-handle.html)

```
DELETE /data-id/:handleId
```

##### [data_id_handle_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/data_id_handle_actions.js#L67-L78)

```js
export const dropHandler = (token, handleId) => ({
  type: ACTION_TYPES.DROP_HANDLER,
  payload: {
    request: {
      method: 'delete',
      url: `/data-id/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

### Get the metadata of the appendable data

The app fetches the metadata of your appendable data.

#### [Get metadata](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#get-metadata)

```
GET /appendable-data/metadata/:handleId
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L25-L37)

```js
export const fetchAppendableDataMeta = (token, handleId) => {
  return {
    type: ACTION_TYPES.FETCH_APPENDABLE_DATA_META,
    payload: {
      request: {
        url: `/appendable-data/metadata/${handleId}`,
        headers: {
          'Authorization': token
        }
      }
    }
  };
};
```

If the data length is 0, it means your inbox is empty.

<!-- why not use fetchAppendableDataHandle instead of fetchAppendableDataMeta to get the dataLength? -->

## Iterate through the appendable data

If the data length is greater than 0, the app iterates through the appendable data. It starts at index 0 and fetches the data identifier corresponding to the first email.

#### [Get DataIdentifier at a specific index](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#get-data-id-of-a-data-at-appendable-data)

```
GET /appendable-data/:handleId/:index
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L54-L64)

```js
export const fetchDataIdAt = (token, handleId, index) => ({
  type: ACTION_TYPES.FETCH_DATA_ID_AT,
  payload: {
    request: {
      url: `/appendable-data/${handleId}/${index}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

## Fetch an email

For each email in the appendable data, the app needs to retrieve the corresponding immutable data.

### Get an immutable data reader handle

The app fetches the data map of the email using the data identifier handle previously obtained. The data map is decrypted using the asymmetric keys of the app.

#### [Get ImmutableDataReader handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#get-immutabledata-reader)

```
GET /immutable-data/reader/:handleId
```

##### [immutable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/immutable_data_actions.js#L15-L25)

```js
export const getImmutableDataReadHandle = (token, handleId) => ({
  type: ACTION_TYPES.GET_IMMUT_READ_HANDLE,
  payload: {
    request: {
      url: `/immutable-data/reader/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

The API returns an immutable data reader handle.

### Read the immutable data

The app reads the immutable data representing the email using the immutable data reader handle.

#### [Read ImmutableData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#read-immutable-data-using-reader)

```
GET /immutable-data/:handleId
```

##### [immutable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/immutable_data_actions.js#L27-L38)

```js
export const readImmutableData = (token, handleId) => ({
  type: ACTION_TYPES.READ_IMMUT_DATA,
  payload: {
    request: {
      url: `/immutable-data/${handleId}`,
      headers: {
        'Authorization': token
      },
      responseType: 'arraybuffer'
    }
  }
});
```

### Drop the immutable data reader

The app drops the immutable data reader handle.

#### [Drop ImmutableDataReader handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#drop-immutable-data-reader)

```
DELETE /immutable-data/reader/:handleId
```

##### [immutable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/immutable_data_actions.js#L84-L95)

```js
export const closeImmutableDataReader = (token, handleId) => ({
  type: ACTION_TYPES.CLOSE_IMMUT_DATA_READER,
  payload: {
    request: {
      method: 'delete',
      url: `/immutable-data/reader/${handleId}`,
      headers: {
        'Authorization': token,
      }
    }
  }
});
```

The app adds the content of the email to your inbox and then repeats this process for the next email (if there is one). It continues iterating through the appendable data until all your emails have been fetched.

## Get the size of the appendable data

Your appendable data has a maximum size of 100 KiB. To update the amount of space used by the emails in your appendable data, the app fetches the serialized content of your appendable data and measures its size.

#### [Serialize AppendableData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#serialise-appendabledata)

```
GET /appendable-data/serialise/:handleId
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L132-L143)

```js
export const getAppendableDataLength = (token, handleId) => ({
  type: ACTION_TYPES.GET_APPENDABLE_DATA_LENGTH,
  payload: {
    request: {
      url: `/appendable-data/serialise/${handleId}`,
      headers: {
        'Authorization': token
      },
      responseType: 'arraybuffer'
    }
  }
});
```

### Drop the appendable data handle

After refreshing your inbox folder, the app drops the appendable data handle.

#### [Drop AppendableData handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#drop-handle)

```
DELETE /appendable-data/handle/:handleId
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L145-L156)

```js
export const dropAppendableDataHandle = (token, handleId) => ({
  type: ACTION_TYPES.DROP_APPENDABLE_DATA_HANDLE,
  payload: {
    request: {
      method: 'delete',
      url: `/appendable-data/handle/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```
