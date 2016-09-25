# Saving an email

Your appendable data has a maximum size of 100 KiB. You can save the emails you want to keep by adding them your root structured data (which has no size limit). After you save an email, the app deletes that email from your appendable data. Therefore, it won't show up in your inbox anymore.

#### Contents

<!-- toc -->

![Saved page](/assets/saved-page.png)

## Updating the root structured data

The app updates the JSON data contained in your root structured data. It will now include the email you want to save.

#### PUT [/structuredData/:handleId](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#update-structured-data)

**core_structure_actions.js**

```js
export const updateCoreStructure = (token, id, data) => ({
  type: ACTION_TYPES.UPDATE_CORE_STRUCTURE,
  payload: {
    request: {
      method: 'put',
      url: `/structuredData/${id}`,
      headers: {
        'Content-Type': 'text/plain',
        'Tag-Type': CONSTANTS.TAG_TYPE.DEFAULT,
        Encryption: CONSTANTS.ENCRYPTION.SYMMETRIC,
        'Authorization': token
      },
      data: new Uint8Array(new Buffer(JSON.stringify(data)))
    }
  }
});
```

## Fetching the appendable data handle

The app fetches the data identifier handle corresponding to your appendable data.

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

## Deleting the email from the appendable data

The app deletes the email you just saved from your appendable data. This moves the email to the `deleted_data` field of the appendable data.

#### DELETE [/appendableData/:handleId/:index](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md#delete-data-by-index)

**appendable_data_actions.js**

```js
export const deleteAppendableData = (token, handleId, index) => {
  return {
    type: ACTION_TYPES.DELETE_APPENDABLE_DATA,
    payload: {
      request: {
        method: 'delete',
        url: `/appendableData/${handleId}/${index}`,
        headers: {
          'Authorization': token
        }
      }
    }
  };
};
```

## Clear the deleted data of the appendable data

The app clears the `deleted_data` field of your appendable data.

#### DELETE /appendableData/clearDeletedData/:handleId

**appendable_data_actions.js**

```js
export const clearDeleteData = (token, handleId) => ({
  type: ACTION_TYPES.CLEAR_DELETE_DATA,
  payload: {
    request: {
      method: 'delete',
      url: `/appendableData/clearDeletedData/${handleId}`,
      headers: {
        'Authorization': token
      }
    }
  }
});
```

## Drop the appendable data handle

The app drops the data identifier handle corresponding to your appendable data.

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
