# What's next?

Now that you understand how this email app works, feel free to [fork it](https://github.com/maidsafe/safe_examples) and build your own email app!

## New features

Here are some potential features that you might want to build.

### Outbox

In addition to storing your saved emails in your root structured data, you could also store your outbox (the emails you send).

### Sending replies

Currently you have to compose a new email if you want to reply to someone. You could add the ability to send replies.

### Forwarding emails

You could also add the ability to forward an email to other users.

### File attachments

You could add the ability to upload file attachments using the [Immutable Data API](https://api.safedev.org/low-level-api/immutable-data/).

### Multiple recipients

You could add the ability to send an email to multiple recipients. The email would need to be added to the appendable data of each recipient.

### Spam filtering

Using the `filter` field of the appendable data, you could let the user specify a blacklist of a whitelist.

See the [Filter API](https://api.safedev.org/low-level-api/appendable-data/filter/) for more info.

### Custom templates

Try to build an email app with a pre-built template such as [this one](http://purecss.io/layouts/email/).
