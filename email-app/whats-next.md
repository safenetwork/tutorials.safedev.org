# What's next?

Now that you understand how this example app works, feel free to [fork it](https://github.com/maidsafe/safe_examples) and build your own email app!

## New features

Here are some potential features that you might want to build.

### Outbox

In addition to storing your saved emails in your root structured data, you could also store your outbox (the emails you send).

### Sending replies

Currently you have to compose a new email if you want to reply to someone. You could add the ability to send replies.

### Forwarding emails

You could also add the ability to forward an email to other users.

### File attachments

You could add ability to upload file attachments by using the [Immutable Data API](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/immutable_data.md).

### Multiple recipients

You could add the ability to send an email to multiple recipients. The email would need to be added to the appendable data of each recipient.

### Spam filtering

Using the `filter` field of the appendable data, you could let the user specifcy a blacklist of a whitelist.

> Owner(s) can set `filter` to either blacklist or whitelist. In case it is set to `Filter::BlackList`, the vaults shall enforce the rule of allowing everyone but the blacklisted keys to add to `data` on subsequent updates following the change to `filter`, i.e. existing data in `data` field will not be dealt by the vaults. Similarly, if set to `Filter::WhiteList`, no one but the the whitelisted keys will be allowed to add data.

Source: [RFC 38 – Appendable Data](https://github.com/maidsafe/rfcs/blob/master/text/0038-appendable-data/0038-appendable-data.md)

### Custom templates

Try to build an email app with pre-built template such as [this one](http://purecss.io/layouts/email/).
