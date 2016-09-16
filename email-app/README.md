# Email app

In this tutorial, you will learn how to build **an email app that works entirely on the SAFE Network**!

Instead of relying on an email service provider such as Gmail, or going through the trouble of running your own mail server, you can use the SAFE Network to build dynamic applications such as email and messaging apps.

![Compose Mail page](/assets/compose-mail-page.png)

![Inbox page](/assets/inbox-page.png)

## Source code

**[Click here to view the source code of this example app on GitHub](https://github.com/maidsafe/safe_examples/tree/master/email_app)**

[electron-react-boilerplate](https://github.com/chentsulin/electron-react-boilerplate) was used as a starting point.

## Overview

This tutorial will showcase how to:

- Create an email ID
- Send emails to other users
- Check for new emails
- Save emails
- Delete emails

### SAFE Launcher API

You will learn about the following APIs:

- [Authorization](https://maidsafe.readme.io/docs/auth)
- [Network File System (NFS)](https://maidsafe.readme.io/docs/network-file-system-nfs)
- [Data Identifier](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/0042-launcher-api-v0.6.md#handle-id)
- [Structured Data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md)
- [Immutable Data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md)
- [Appendable Data](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/appendable_data.md)

### User interface

The user interface is built using the following front-end libraries:

- [React](https://facebook.github.io/react/)
- [Redux](http://redux.js.org/)
- [React Router](https://github.com/reactjs/react-router)

Since it's built using [Electron](http://electron.atom.io/), it can be distributed as a desktop app (Windows, OS X and Linux).

## Setup

Here are the instructions for running this example app locally.

**Make sure SAFE Launcher is running**

You need to have [SAFE Launcher v0.9.0](https://github.com/maidsafe/safe_launcher/releases/tag/0.9.0) installed and running to use this example app.

**Make sure you have [Node.js](https://nodejs.org/en/) installed**

```
node -v
```

**Clone the [GiHub repository](https://github.com/maidsafe/safe_examples)**

```
git clone https://github.com/maidsafe/safe_examples.git
```

**Install the dependencies**

```
cd email_app && npm install
```

**Start the app**

```
npm run dev
```
