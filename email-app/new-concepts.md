# New concepts

Concepts that are not currently documented on [maidsafe.readme.io](https://maidsafe.readme.io/).

## Data identifier

An identifier to address a data chunk in the SAFE Network.

The different variants currently are `Structured`, `Immutable`, `PubAppendable` and `PrivAppendable`.

### Handle

Handles are used to interact with the low-level APIs.

Refer to [RFC 41 – Low Level API](https://github.com/maidsafe/rfcs/blob/master/text/0041-low-level-api/0041-low-level-api.md#choice-of-api) for more details (e.g. why it's a good practice to free handles when they are not used anymore).

## Structured data

> Structured data has a size restriction of 100KiB including its internals. If the size is more than permitted size after serialisation, error is returned.

> Versioned, Unversioned or Custom Structured data types can be created. If the size of the data being passed is greater than 100KiB, the versioned and unversioned structured data will ensure that the data is managed and stored successfully. But for custom type_tags it becomes the responsibility of the application to handle the size restriction.

Refer to [RFC 42 – Launcher API v0.6](https://github.com/maidsafe/rfcs/blob/master/text/0042-launcher-api-v0.6/api/structured_data.md) for more details.

## Appendable data

Appendable data allows non-owners to update the data.

There are two types of appendable data:

- **public**: anyone can read the data

- **private**: only the owner can read the data

Appendable data also provides the option to filter users by public key. Two filter types are supported, `Whitelist` (allow) or `Blacklist` (restrict).

Refer to the [RFC 38 – Appendable Data](https://github.com/maidsafe/rfcs/blob/master/text/0038-appendable-data/0038-appendable-data.md) for more details.

## Immutable data

Immutable data is written to and read from the network via [self-encryption](https://safenetwork.wiki/en/Security_-_Self_encryption). Once created, it can’t be edited by anyone.

See the [self_encryption crate](https://github.com/maidsafe/self_encryption) for more details.

## Encryption

### Symmetric

This type of encryption is used when encrypting data for yourself. No one else will be able to read it.

We use a particular combination of Salsa20 and Poly1305 as specified [here](http://docs.maidsafe.net/rust_sodium/master/rust_sodium/crypto/secretbox/xsalsa20poly1305/index.html) and [here](http://nacl.cr.yp.to/valid.html).

### Asymmetric

This type of encryption is used when encrypting data for someone else to read. After encrypting, even the sender wouldn’t be able to read the data anymore.

We use a particular combination of Curve25519, Blake2B, Salsa20 and Poly1305 as specified [here](http://docs.maidsafe.net/rust_sodium/master/rust_sodium/crypto/sealedbox/curve25519blake2bxsalsa20poly1305/index.html).
