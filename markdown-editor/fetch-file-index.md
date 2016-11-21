# Fetch file index

The app fetches the file index associated with your user prefix. Each Markdown file you create is stored inside a new [unversioned structured data](https://api.safedev.org/low-level-api/structured-data/). The ID of each file is based on your user prefix and the filename. Your file index contains the names of all your files. Individual files can be fetched using your user prefix and the filename.

#### Contents

<!-- toc -->

![Fetch file index](img/fetch-file-index.png)

## Get a data ID handle

The app obtains a data ID handle for the unversioned structured data (type tag 500) that contains your file index.

#### [Get data ID handle](https://api.safedev.org/low-level-api/data-id/get-data-id-handle.html#for-structured-data)

```
POST /data-id/structured-data
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L145)

```js
safeDataId.getStructuredDataHandle(ACCESS_TOKEN, INDEX_FILE_NAME, 500)
```

The name of your file index is based on your user prefix and the string "#index".

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L143)

```js
const INDEX_FILE_NAME = btoa(`${USER_PREFIX}#index`);
```

## Get a structured data handle

The app tries to obtain a structured data handle using the data ID handle of your file index.

#### [Get structured data handle](https://api.safedev.org/low-level-api/structured-data/get-structured-data-handle.html)

```
GET /structured-data/handle/:dataIdHandle
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L147)

```js
safeStructuredData.getHandle(ACCESS_TOKEN, handle)
```

The app stores the structured data handle in a global variable.

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L153)

```js
INDEX_HANDLE = sdHandle;
```

## Fetch the file index

The app tries to read the structured data that contains your file index using the structured data handle previously obtained.

#### [Read structured data](https://api.safedev.org/low-level-api/structured-data/read-structured-data.html)

```
GET /structured-data/:handleId/:version?
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L155-L155)

```js
safeStructuredData.readData(ACCESS_TOKEN, sdHandle, '')
```

If the app is able to read the structured data, it parses your file index and stores it in a global variable.

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L176)

```js
FILE_INDEX = payload;
```

## Create a file index

If the app is unable to read the structured data, it means that your file index hasn't been created yet. Therefore, the app will create an unversioned structured data (type tag 500) with an ID based on your user prefix and the string "#index". Your file index is encrypted using the cipher options handle previously obtained.

#### [Create structured data](https://api.safedev.org/low-level-api/structured-data/create-structured-data.html)

```
POST /structured-data
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L161-L162)

```js
safeStructuredData.create(ACCESS_TOKEN, INDEX_FILE_NAME, 500,
  new Buffer(JSON.stringify({})).toString('base64'), SYMETRIC_CYPHER_HANDLE)
```

The app saves your file index by sending a PUT request to the SAFE Network.

#### [Save structured data](https://api.safedev.org/low-level-api/structured-data/save-structured-data.html#put-endpoint)

```
PUT /structured-data/:handleId
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L165)

```js
safeStructuredData.put(ACCESS_TOKEN, INDEX_HANDLE)
```

Finally, the app stores your file index in a global variable.

##### [store.js](https://github.com/maidsafe/safe_examples/blob/6f740f79ce30349c2b94252d6856927375bf3dbe/markdown_editor/src/store.js#L176)

```js
FILE_INDEX = payload;
```
