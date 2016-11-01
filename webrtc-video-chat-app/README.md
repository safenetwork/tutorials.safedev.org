# WebRTC video chat app

In this tutorial, you will learn how to **establish a WebRTC connection using the SAFE Network**.

Instead of using a centralized video chat service, such as Skype or Google Hangouts – who can track your location, what you are saying, and to whom, you can use the SAFE Network as a secure channel to exchange connectivity information to establish direct peer-to-peer WebRTC connections.

The **SAFE Signaling Demo** lets you set up a room and share the room name with someone you want to talk with. You should be able to connect to one another with video, audio and a small instant messaging feature on the left. You can see yourself on the bottom right, your peer is visible in the center. Enjoy!

For now, only the connection establishment is done using the SAFE Network. The actual connection is either peer-to-peer ([STUN](https://en.wikipedia.org/wiki/STUN)) or via a [TURN](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT) server. At some point later, we will integrate this functionality directly in [CRUST](https://github.com/maidsafe/crust).

#### Contents

<!-- toc -->

![Video chat app](img/video-chat-app.png)

## Overview

This tutorial will showcase how to:

- [Create an offer](create-an-offer.md)
- [Receive an offer](receive-an-offer.md)
- [Create an answer](create-an-answer.md)
- [Receive an answer](receive-an-answer.md)

### APIs

You will learn about the following APIs:

- [Authorization API](https://api.safedev.org/auth/)
- [Data Identifier API](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/data_identifier.md)
- [Structured Data API](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md)

### External libraries and APIs

- [React](https://facebook.github.io/react/) (for the user inteface)
- [simple-peer](https://github.com/feross/simple-peer) (simple WebRTC video/voice and data channels)
- [MediaDevices.getUserMedia()](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)

## Source code

[Browse **the source code of the SAFE Signaling Demo** on GitHub](https://github.com/maidsafe/safe_examples/tree/master/webrtc_app)

[create-react-app](https://github.com/facebookincubator/create-react-app) was used as a starting point.

### Live version

You can access the **SAFE Signaling Demo** at **[safe://mediawebrtc.ben](safe://mediawebrtc.ben)** using [SAFE Browser v0.3.6-2](https://github.com/joshuef/beaker/releases/tag/0.3.6-2).

### Development mode

#### Requirements

##### 1. SAFE Launcher

Start [SAFE Launcher v0.9.3](https://github.com/maidsafe/safe_launcher/releases/tag/0.9.3) and log in.

##### 2. SAFE Browser

Start [SAFE Browser v0.3.6-2](https://github.com/joshuef/beaker/releases/tag/0.3.6-2).

##### 3. Node.js

Make sure you have Node.js v6 (LTS).

```
node --version
```

There are many ways to install Node.js. See [nodejs.org](https://nodejs.org/en/download/) for more info.

#### Setup

##### 1. Clone [this GitHub repository](https://github.com/maidsafe/safe_examples)

```
git clone https://github.com/maidsafe/safe_examples.git
```

If you don't have Git installed, you can download it from [git-scm.com](https://git-scm.com/downloads).

##### 2. Install the dependencies

```
cd safe_examples/webrtc_app && npm install
```

##### 3. Start the app

```
npm run start
```

### Limitations

- Cannot reuse the same room (currently you need to use a different room name for every new chat)
- Supports only one-on-one chat (group chat not currently supported)
- Needs camera access or else it doesn't work
