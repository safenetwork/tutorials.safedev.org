# Send an email

To send emails to other SAFE Network users, you need to know their email ID.

First, the app retrieves the encryption key associated with the appendable data that belongs to the recipient. Then, it encrypts the email using that encryption key and saves it as immutable data. Finally, it appends the immutable data that represents the email to the appendable data of the recipient.

#### Contents

<!-- toc -->

![Compose Mail page](img/compose-mail-page.png)

The [JSON](https://en.wikipedia.org/wiki/JSON) data for the above email would look like this:

```json
{
  "subject": "Test",
  "from": "example",
  "time": "Fri, 16 Sep 2016 10:49:44 GMT",
  "body": "123"
}
```

## Get the appendable data of the recipient

Before fetching the encryption key of the recipient, the app needs to obtain an appendable data handle.

### Get a data identifier handle

First, the app fetches a data identifier handle for the appendable data of the recipient.

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

The name of the appendable data is obtained by hashing the email ID of the recipient:

##### [app_utils.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/utils/app_utils.js#L27-L29)

```js
export const hashEmailId = emailId => {
  return crypto.createHash('sha256').update(emailId).digest('base64');
};
```

### Get an appendable data handle

The app fetches an appendable data handle using the data identifier handle previously obtained.

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

The app drops the data identifier handle for the appendable data of the recipient.

#### [Drop data ID handle](https://api.safedev.org/low-level-api/data-id/drop-data-id-handle.html)

```
/data-id/:handleId
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

## Get the encryption key of the recipient

After the appendable data handle is successfully obtained, the app fetches an handle for the public encryption key of the recipient. By encrypting your email using that encryption key, only the recipient will be able to read it. This is known as asymmetric encryption.

#### [Get encryption key handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#get-encryption-key)

```
GET /appendable-data/encrypt-key/:handleId
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L66-L76)

```js
export const getEncryptedKey = (token, handleId) => ({
  type: ACTION_TYPES.GET_ENCRYPTED_KEY,
  payload: {
    request: {
      url: `/appendable-data/encrypt-key/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

### Get a cipher options handle

The app fetches a cipher options handle for asymmetric encryption using the encryption key handle of the recipient.

#### [Get cipher options handle](https://api.safedev.org/low-level-api/cipher-options/get-cipher-options-handle.html)

```
GET /cipher-opts/:encType/:keyHandle?
```

##### [cipher-opts_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/cipher-opts_actions.js#L3-L13)

```js
export const getCipherOptsHandle = (token, encType, keyHandle='') => ({
  type: ACTION_TYPES.GET_CIPHER_OPTS_HANDLE,
  payload: {
    request: {
      url: `/cipher-opts/${encType}/${keyHandle}`,
      headers: {
        'Authorization': token,
      }
    }
  }
});
```

### Drop the encryption key handle

The app drops the encryption key handle of the recipient.

#### [Drop handle](#)

```
DELETE /appendable-data/encrypt-key/:handleId
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L78-L89)

```js
export const deleteEncryptedKey = (token, handleId) => ({
  type: ACTION_TYPES.DELETE_ENCRYPTED_KEY,
  payload: {
    request: {
      method: 'delete',
      url: `/appendable-data/encrypt-key/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

## Create the email

The app creates an email using the cipher handle that contains the encryption key of the recipient.

### Get an immutable data writer handle

First, the app fetches an immutable data writer handle.

#### [Get ImmutableDataWriter handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#get-immutabledata-writer)

```
GET /immutable-data/writer
```

##### [immutable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/immutable_data_actions.js#L3-L13)

```js
export const createImmutableDataWriterHandle = (token) => ({
  type: ACTION_TYPES.CREATE_IMMUT_WRITER_HANDLE,
  payload: {
    request: {
      url: `/immutable-data/writer`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

### Write immutable data

The app stores the email as immutable data using the immutable data writer handle.

#### [Write ImmutableData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#write-immutable-data)

```
POST /immutable-data/:handleId
```

##### [immutable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/immutable_data_actions.js#L40-L56)

```js
export const writeImmutableData = (token, handleId, data) => {
  const dataByteArray = new Uint8Array(new Buffer(JSON.stringify(data)));
  return {
    type: ACTION_TYPES.WRITE_IMMUT_DATA,
    payload: {
      request: {
        method: 'post',
        url: `/immutable-data/${handleId}`,
        headers: {
          'content-type': 'text/plain',
          'Authorization': token
        },
        data: dataByteArray
      }
    }
  };
};
```

### Close the immutable data writer

The app encrypts the data map of the email using the cipher handle that contains the encryption key handle of the recipient. The data map is stored as immutable data on the SAFE Network.

#### [Close ImmutableDataWriter](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#close-immutable-data-writer)

```
PUT /immutable-data/:handleId/:cipherOptsHandle
```

##### [immutable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/immutable_data_actions.js#L58-L69)

```js
export const putImmutableData = (token, handleId, cipherOptsHandle) => ({
  type: ACTION_TYPES.PUT_IMMUT_DATA,
  payload: {
    request: {
      method: 'put',
      url: `/immutable-data/${handleId}/${cipherOptsHandle}`,
      headers: {
        'Authorization': token,
      }
    }
  }
});
```

Once the write operation is successful, the API returns a data identifier handle for the data map of the email.

<!-- *(explain what is a DataMap)* -->

### Drop the immutable data writer handle

The app drops the immutable data writer handle.

#### [Drop handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#drop-immutable-data-writer)

```
DELETE /immutable-data/writer/:handleId
```

##### [immutable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/immutable_data_actions.js#L71-L82)

```js
export const closeImmutableDataWriter = (token, handleId) => ({
  type: ACTION_TYPES.CLOSE_IMMUT_DATA_WRITER,
  payload: {
    request: {
      method: 'delete',
      url: `/immutable-data/writer/${handleId}`,
      headers: {
        'Authorization': token,
      }
    }
  }
});
```

## Append the email to the appendable data

The app adds the data identifier handle representing your email to the appendable data of the recipient.

#### [Append DataIdentifier](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#append-data)

```
PUT /appendable-data/:handleId/:dataIdHandle
```

##### [appendable_data_actions.js](https://github.com/maidsafe/safe_examples/blob/f1d7510b9a17c05a31da761927e05f17ca9b1c26/email_app/app/actions/appendable_data_actions.js#L106-L117)

```js
export const appendAppendableData = (token, handleId, dataIdHandle) => ({
  type: ACTION_TYPES.APPEND_APPENDABLE_DATA,
  payload: {
    request: {
      method: 'put',
      url: `/appendable-data/${handleId}/${dataIdHandle}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

### Drop the appendable data handle

After your email is successfully appended to the appendable data of the recipient, the app drops the appendable data handle.

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
