# Create an offer

If you are the first user to join a room (the person initiating the call), the app creates an offer and tries to store it inside a structured data with an ID based on the name of the room. This offer includes a session description in [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) format, and it needs to be [delivered to the call recipient](receive-an-offer.md) (the person receiving the call).

#### Contents

<!-- toc -->

## Create a new WebRTC peer connection

The app creates a new [RTCPeerConnection](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/RTCPeerConnection) using the [simple-peer](https://github.com/feross/simple-peer) module. If you are the first user to join this room, `props.peerPayload` is `false` and therefore the value of `initiator` will be `true`. This means that the app will create an offer.

##### [PeerView.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/components/PeerView.js#L59-L65)

```js
const initiator = !props.peerPayload
const peer = new Peer({ initiator: initiator,
                      stream: props.stream,
                      config : {
                        iceServers: CONFIG.iceServers
                      },
                      trickle: false })
```

For now, only the connection establishment is done using the SAFE Network. The actual connection is either peer-to-peer ([STUN](https://en.wikipedia.org/wiki/STUN)) or via a [TURN](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT) server. At some point later, we will integrate this functionality directly in [CRUST](https://github.com/maidsafe/crust).

## Store the offer inside a structured data

After the offer is created, the `signal` event is fired.

##### [PeerView.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/components/PeerView.js#L77-L90)

```js
peer.on('signal', (d) => {
  // try to automatically establish connection
  publishData(targetId, {payload: d, targetId: myNewId})
    .catch(console.log.bind(console))

  if (initiator) {
    let poller = window.setInterval(() => {
      readData(myNewId).then((data) => {
        window.clearInterval(poller)
        peer.signal(data.payload)
      })
    }, 2000) // we poll once every 2 seconds
  }
})
```

The app tries to create a structured data with an ID based on the name of the room. This structured data will contain the offer as well as a random ID, which will be used to store the answer of the call recipient.

##### [PeerView.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/components/PeerView.js#L66-L67)

```js
const targetId = initiator ? props.room : props.peerPayload.targetId
const myNewId = this.props.room + '-' + (Math.random())
```

### Create a structured data

The app creates a structured data handle for the offer.

#### [Create StructuredData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#create)

```
POST /structured-data
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L78)

```js
safeStructuredData.create(ACCESS_TOKEN, address, 500, data)
```

The address of the structured data is based on the app ID (`example.signaling.v1`) and the name of the room. The structured data is unversioned (type 500). The data is stored as a base64 string.

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L75-L76)

```js
const address = btoa(`${APP_ID}-${name}`)
const data = new Buffer(JSON.stringify(payload)).toString('base64')
```

### Save the structured data

The app saves the structured data by sending a PUT request to the SAFE Network.

#### [Save StructuredData](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md#save-structured-data)

```
PUT /structured-data/:handleId
```

##### [store.js](https://github.com/maidsafe/safe_examples/blob/9f51976fbc5a3c0fa1e14b61df9701d1680dc1aa/webrtc_app/src/store.js#L83)

```js
safeStructuredData.put(ACCESS_TOKEN, handleId)
```
