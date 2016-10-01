# Sending an email

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

## Fetch the appendable data handle

The app fetches the appendable data handle with an ID corresponding to the hash of the email ID of the recipient:

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

## Get the encryption key

After the appendable data handle is successfully retrieved, the app fetches the public encrytion key of the recipient. By encrypting your email using that encryption key, only the recipient will be able to read it. This is known as asymmetric encryption.

#### GET /appendableData/encryptKey/:handleId

**appendable_data_actions.js**

```js
export const getEncryptedKey = (token, handleId) => ({
  type: ACTION_TYPES.GET_ENCRYPTED_KEY,
  payload: {
    request: {
      url: `/appendableData/encryptKey/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

## Save the email as immutable data

After the public encryption key of the recipient is successfully retrieved, the app saves your email on the SAFE Network using the [Immutable Data API](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md).

#### POST [/immutableData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md#write-immutable-data-using-self-encryptor)

**immutable_data_actions.js**

```js
export const createMail = (token, data, encryptKeyHandler) => ({
  type: ACTION_TYPES.CREATE_MAIL,
  payload: {
    request: {
      method: 'post',
      url: '/immutableData',
      headers: {
        'Content-Type': 'text/plain',
        encryption: CONSTANTS.IMMUT_ENCRYPTION_TYPE.ASYMMETRIC,
        'encrypt-key-handle': encryptKeyHandler,
        'Authorization': token
      },
      data: new Uint8Array(new Buffer(JSON.stringify(data)))
    }
  }
});
```

Once the write operation is successful, the API returns a data identifer handle corresponding to the DataMap.

<!-- *(explain what is a DataMap)* -->

## Append the email to the appendable data

The app adds your email (represented by a DataMap handle) to the appendable data of the recipient.

#### PUT [/appendableData/:handleId/:dataIdHandle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#append-data)

**appendable_data_actions.js**

```js
export const appendAppendableData = (token, id, dataId) => ({
  type: ACTION_TYPES.APPEND_APPENDABLE_DATA,
  payload: {
    request: {
      method: 'put',
      url: `/appendableData/${id}/${dataId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

## Drop the appendable data handle

After your email is successfully appended to the appendable data of the recipient, the app drops the data identifier handle corresponding to the appendable data of the recipient.

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
