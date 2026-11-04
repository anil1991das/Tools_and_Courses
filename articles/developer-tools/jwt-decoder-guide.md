---
title: "JWT Explained: How to Decode a JSON Web Token"
description: "Understand JWT structure, inspect header and payload claims, and learn why decoding a token does not verify its signature or make it trustworthy."
slug: "jwt-decoder-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Developer Tools"
tags:
  - "JWT"
  - "Authentication"
  - "JSON"
  - "Web Security"
relatedTools:
  - title: "JWT Decoder"
    url: "/jwt-decoder"
relatedArticles: []
draft: false
---

# JWT Explained: How to Decode a JSON Web Token

JSON Web Tokens (JWTs) are compact tokens that can carry claims between systems. They are commonly used in authentication and authorization workflows, including applications that need to pass information between a client and an API.

A JWT can look like a long string of letters, numbers, periods, underscores, and hyphens. A decoder helps make its structure readable, but decoding is not the same as verifying that the token is authentic.

The [iamnoobs JWT Decoder](https://iamnoobs.com/jwt-decoder) can help inspect the readable parts of a token.

## What is a JWT?

A JSON Web Token is a compact representation of claims. In the common signed JWT format, the token contains three Base64URL-encoded parts separated by periods:

~~~text
header.payload.signature
~~~

The three parts are:

1. **Header:** identifies information such as the token type and signing algorithm.
2. **Payload:** contains claims, such as an issuer, subject, audience, or expiration time.
3. **Signature:** provides cryptographic integrity when the token is correctly signed and verified.

The header and payload are encoded, not encrypted. Anyone who obtains a typical signed JWT can decode those sections and read their contents.

## A simplified JWT example

A header might contain:

~~~json
{
  "alg": "HS256",
  "typ": "JWT"
}
~~~

A payload might contain:

~~~json
{
  "sub": "user-123",
  "role": "reader",
  "iat": 1760000000,
  "exp": 1760003600
}
~~~

These values are illustrative. A real token may contain different claims and must be interpreted according to the application's rules.

## Common JWT claims

| Claim | Meaning |
|---|---|
| iss | Issuer of the token |
| sub | Subject represented by the token |
| aud | Intended audience |
| exp | Expiration time |
| nbf | Time before which the token must not be accepted |
| iat | Time the token was issued |
| jti | Token identifier |

A claim may be registered, public, or private. The application determines which claims are required and how they should be interpreted.

## Decode a JWT with the online tool

1. Open the [iamnoobs JWT Decoder](https://iamnoobs.com/jwt-decoder).
2. Paste the token into the input area.
3. Run the decode operation.
4. Inspect the header and payload fields.
5. Check claims such as issuer, audience, and expiration in the context of your application.

Use only tokens that you are authorized to inspect. Avoid pasting production access tokens or other live credentials into an online service unless you have reviewed and approved its data-handling behavior.

## Decoding does not verify a signature

This is the most important distinction.

Decoding Base64URL data only reveals the encoded content. It does not prove that the token was created by a trusted issuer or that its contents have not been changed.

A secure token-validation process must check, as appropriate:

- The signature using the expected verification key.
- The allowed signing algorithm.
- The expected issuer.
- The expected audience.
- The expiration and not-before times.
- Application-specific authorization rules.

Never authorize a user or API operation solely because a JWT can be decoded successfully.

## Base64URL is not ordinary encryption

JWT compact serialization uses Base64URL encoding for its header and payload segments in the common JWS form. Base64URL uses a URL-safe alphabet and has encoding rules that differ from standard Base64 presentation.

Encoding is not a confidentiality mechanism. Do not place passwords, private keys, or sensitive personal data in a token payload unless the token format and encryption design are specifically intended to protect that information.

JWE is a separate JSON Web Encryption format that can provide encrypted content. A three-part signed JWS and a five-part compact JWE should not be confused.

## Understand expiration times

JWT timestamps are commonly represented as NumericDate values: seconds relative to the Unix epoch.

For example, an expiration claim can be represented as:

~~~json
{
  "exp": 1760003600
}
~~~

The application should reject a token after its accepted expiration time, subject to any explicitly configured clock-skew policy. A decoder can display the value, but the server must enforce the rule.

## Common JWT mistakes

### 1. Treating the payload as secret

A signed JWT does not hide its payload. Use appropriate encryption when confidentiality is required, and avoid putting unnecessary sensitive information in claims.

### 2. Trusting the algorithm field blindly

A verifier must enforce an allowed algorithm policy. Do not let untrusted token input decide how the application verifies it.

### 3. Skipping issuer and audience checks

A valid signature alone may not mean a token is intended for your application. Verify the expected issuer and audience.

### 4. Ignoring expiration

A token should not be accepted indefinitely when it has an expiration claim and the application's policy requires enforcement.

### 5. Confusing decoding with authentication

A decoded name or role is just data until the token has been verified and the claims have been validated.

## Frequently asked questions

### Can anyone decode a JWT?

Anyone who has a typical signed JWT can decode its Base64URL-encoded header and payload. That does not give them the signing key or prove that the token is valid.

### Does a JWT decoder check whether a token is valid?

A decoder displays encoded content. Signature verification and claim validation require a separate verification process with the correct keys and policy.

### Should I share a JWT for debugging?

Treat access tokens as credentials. Use redacted or test tokens for demonstrations and debugging, and revoke exposed tokens according to your application's procedures.

## Further reading

- [RFC 7519: JSON Web Token (JWT)](https://www.rfc-editor.org/rfc/rfc7519)
- [RFC 7515: JSON Web Signature (JWS)](https://www.rfc-editor.org/rfc/rfc7515)
- [RFC 7516: JSON Web Encryption (JWE)](https://www.rfc-editor.org/rfc/rfc7516)
- [OWASP: JSON Web Token Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html)

---

**Try it:** [iamnoobs JWT Decoder](https://iamnoobs.com/jwt-decoder)
