---
title: Blockchain Claims for use in JWT
author: Jared Hanson (@jaredhanson)
discussions-to: https://github.com/jaredhanson/id-blockchain-claims-in-jwt
status: Draft
type: Standard
created: 2022-07-11
updated: 2022-07-11
requires: [2, 10, 19]
---

## Simple Summary

This specification defines claims that can be used to identify blockchain-based
entities in JSON Web Tokens (JWTs).

## Abstract

[JSON Web Tokens (JWTs)](https://datatracker.ietf.org/doc/html/rfc7519) are a
widely used means of securely transferring claims for use in authentication and
authorization.  This proposal defines claims to convey blockchain accounts and
assets.

## Motivation

Blockchain accounts authenticate with off-chain services using [Sign in With X
(SIWx)](https://github.com/ChainAgnostic/CAIPs/pull/122).  Once authenticated,
off-chain APIs need to be able to identity accounts and assets in order to
authorize access (often referred to as token gating).

There are established standards such as [OAuth 2.0](https://datatracker.ietf.org/doc/html/rfc6749),
[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) and JWTs
that off-chain services are already using for API access.  It is the intent of
this specification to leverage those standards and facilitate blockchain
integration in off-chain applications.

## Specification

### Claims

`blockchain`: Blockchain on which the entity identified by the JWT has an
account.  The value is a string where [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-2.md)
is the recommended format.

`blockchain_account`: Blockchain account of the entity identified by the JWT.
The value is a string where [CAIP-10](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md)
is the recommended format.  If the JWT has a `blockchain` claim, the value may
contain only the `account_address` portion and omit the `chain_id` prefix, in
which case the account identified by this claim is in the blockchain identified
by the `blockchain` claim.

`blockchain_assets`: Blockchain assets owned by the entity identified by the
JWT.  The value is an array of strings where [CAIP-19](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-19.md)
is the recommended format for each string.  If the JWT has a `blockchain` claim,
the value may contain only the `asset_namespace:asset_reference` portion and
omit the `chain_id` prefix, in which case the assets identified by this claim
are in the blockchain identified by the `blockchain` claim.

## Rationale

- As a chain-agnostic standard, blockchain claims used in JWTs should allow
authorization across off-chain applications regardless of which chain or wallet
the user is using.
- The application server should be able to implement support with minimal
changes to existing authorization infrastructure already being used for
non-blockchain functionality.
- The application server should be able to assign its own identier to its
internal account model.  This motivates the addition of `block_account` as a
claim rather than overloading the existing `sub`.
- The `blockchain_assets` claim serves a similar need as the `entitlements`
claim defined by [Section 2.2.3.1 of RFC9068](https://datatracker.ietf.org/doc/html/rfc9068#section-2.2.3.1).

## Backwards Compatibility

Not applicable.

## Test Cases

```
{
  "iss": "https://authorization-server.example.com/"
  "sub": "5ba552d67",
  "aud": "https://rs.example.com/",
  "blockchain_account": "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
  "exp": 1639528912,
  "iat": 1618354090
}

{
  "iss": "https://authorization-server.example.com/"
  "sub": "5ba552d67",
  "aud": "https://rs.example.com/",
  "blockchain_account": "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
  "blockchain_assets": [
    "eip155:1/erc721:0x06012c8cf97BEaD5deAe237070F9587f8E7A266d/771769",
  ],
  "exp": 1639528912,
  "iat": 1618354090
}

{
  "iss": "https://authorization-server.example.com/"
  "sub": "5ba552d67",
  "aud": "https://rs.example.com/",
  "blockchain": "eip155:1",
  "blockchain_account": "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
  "exp": 1639528912,
  "iat": 1618354090
}

{
  "iss": "https://authorization-server.example.com/"
  "sub": "5ba552d67",
  "aud": "https://rs.example.com/",
  "blockchain": "eip155:1",
  "blockchain_account": "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
  "blockchain_assets": [
    "erc721:0x06012c8cf97BEaD5deAe237070F9587f8E7A266d/771769",
  ],
  "exp": 1639528912,
  "iat": 1618354090
}
```

## Links

- [RFC 7519: JSON Web Token (JWT)](https://datatracker.ietf.org/doc/html/rfc7519)
- [RFC 9068: JSON Web Token (JWT) Profile for OAuth 2.0 Access Tokens](https://datatracker.ietf.org/doc/html/rfc9068)

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
