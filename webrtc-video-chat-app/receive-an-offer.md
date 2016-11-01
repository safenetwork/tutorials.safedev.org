# Receive an offer

If you are joining a room where another user is waiting for you, the app needs to receive an offer (a session description in [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) format) from the caller (the person initiating the call). The app expects this offer to be stored inside a structured data with an ID based on the name of the room. The call recipient then [responds with an answer message](create-an-answer.md), which also contains an SDP description.

#### Contents

<!-- toc -->

## Get data identifier handle

The app fetches a data identifier handle for the structured data that contains the offer.

#### [Get DataIdentifier for StructuredData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/data_identifier.md#get-dataidentifier-for-structureddata)

```
POST /data-id/structured-data
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L45)

```js
safeDataId.getStructuredDataHandle(ACCESS_TOKEN, address, 500)
```

The address of the structured data is based on the app ID (`example.signaling.v1`) and the name of the room. The structured data is unversioned (type 500).

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L42)

```js
const address = btoa(`${APP_ID}-${item}`)
```

## Get structured data handle

The app fetches a structured data handle using the data identifier handle of the offer.

#### [Get StructuredData handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#get-structured-data-handle)

```
GET /structured-data/handle/:dataIdHandle
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L50)

```js
safeStructuredData.getHandle(ACCESS_TOKEN, dataHandleId)
```

## Drop data identifier handle

The app drops the data identifier handle of the offer.

#### [Drop handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/data_identifier.md#drop-handle)

```
DELETE /data-id/:handleId
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L56)

```js
safeDataId.dropHandle(ACCESS_TOKEN, dataHandleId)
```

## Fetch the structured data

The app fetches the content of the structured data using the structured data handle.

#### [Read data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#read-data)

```
GET /structured-data/:handleId
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L57)

```js
safeStructuredData.readData(ACCESS_TOKEN, handleId)
```

## Drop the structured data handle

The app drops the structured data handle of the offer.

#### [Drop handle](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#drop-handle)

```
DELETE /structured-data/handle/:handleId
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L61)

```js
safeStructuredData.dropHandle(ACCESS_TOKEN, handleId)
```
