# Deleting an email

Your appendable data has a maximum size of 100 KiB. You can delete emails from your inbox by removing them from your appendable data. Your root structured data has no size limit. You can delete emails from your saved folder by removing them from your root structured data.

<!-- toc -->

![Inbox page](/assets/inbox-page.png)

## If the email was in your inbox

The emails in your inbox are stored in your appendable data. If you want to delete an email from your inbox, you just need to remove it from your appendable data.

### Fetching the appendable data handle

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

### Deleting the email from the appendable data

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

### Clear the deleted data of the appendable data

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

### Drop the appendable data handle

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

## If the email was in your saved folder

The emails in your saved folder are stored in your root structured data. If you want to delete an email from your saved folder, you just need to remove it from your root structured data.

### Updating the root structured data

The app updates the JSON data contained in your root structured data. It will now exclude the email you want to delete.

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
