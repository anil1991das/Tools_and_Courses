---
title: "Password Hashing Explained: Argon2id, bcrypt, and Secure Storage"
description: "Understand why passwords must be hashed rather than encrypted, how salts and work factors help, and how to choose a password-hashing function."
slug:"password-hashing-bcrypt-argon2-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Security Basics"
tags:
  - "Password Security"
  - "Argon2id"
  - "bcrypt"
  - "Authentication"
relatedTools: []
relatedArticles:
  - "password-generator-guide"
  - "secure-token-generator-guide"
draft: false
---

# Password Hashing Explained: Argon2id, bcrypt, and Secure Storage

Applications should never store users' passwords as readable plain text. If a database is exposed, plain-text passwords give an attacker immediate access to the original credentials, which may also be reused on other services.

Password hashing stores a one-way verifier derived from the password. A secure password-hashing function is deliberately expensive to calculate so that large-scale guessing becomes more costly.

## Hashing versus encryption

Encryption is designed to be reversible when the appropriate key is available. Password verification does not normally need the original password back.

A password-hashing function produces a verifier that can be checked when the user logs in. The server hashes the submitted password using the stored parameters and compares the result using the algorithm's verification routine.

Do not use ordinary fast hashes such as MD5 or SHA-256 directly for password storage. They are designed to be fast, which makes large-scale password guessing cheaper.

## What is a salt?

A salt is a unique random value used with a password before hashing.

A salt helps ensure that identical passwords do not produce identical stored verifiers when different salts are used. It also prevents attackers from relying on precomputed tables for every account.

Modern password-hashing libraries generate and store salts as part of their encoded output. Applications should not invent a shared salt or reuse one for all users.

## What is a work factor?

A password-hashing algorithm has configurable resource costs. The application should select parameters that make verification expensive enough for the deployment while remaining usable under legitimate login load.

The parameters should be reviewed as hardware changes and as security guidance evolves. Benchmark on the production class of infrastructure rather than copying a value from an unrelated example.

## Argon2id

Argon2 is a password-hashing function and Argon2id combines data-independent and data-dependent memory access in a way intended to balance relevant trade-offs.

Argon2id is commonly recommended for new password-storage systems when a supported implementation is available.

Its parameters include memory cost, time cost, and parallelism. Select values in line with current security guidance and the application's resource limits.

## bcrypt

bcrypt is an established adaptive password-hashing algorithm. It has a configurable cost factor that increases work as the cost rises.

bcrypt remains common in existing systems. Be aware of implementation-specific input length limits and encoding behavior when selecting a library.

For new systems, compare current recommendations for Argon2id and available platform support rather than assuming an older deployment configuration is still suitable.

## PBKDF2 and other options

PBKDF2 is another password-based key derivation function used in password storage systems, particularly where required by platform or compliance constraints.

Use a library and parameter set recommended for the target environment. Avoid designing a custom password-hashing algorithm.

## Store the encoded verifier

A password-hashing library commonly stores an encoded value containing the algorithm identifier, salt, parameters, and hash output.

Store that value in a database field sized for the library's current and future formats.

During login, use the library's verification function rather than manually extracting and comparing raw values.

## Rehash when parameters change

As a password-hashing configuration is strengthened, an application can check whether a stored verifier needs an upgrade.

After a user successfully authenticates, the application may compute a new verifier using current parameters and replace the old value.

Do not require users to remember their original password in order to upgrade a hash later. The new verifier can be computed at a successful login.

## Password reset and account recovery

Password hashing is only one part of authentication security.

A password reset flow should use high-entropy tokens, limited validity, single-use behavior, rate limiting, and careful account notification. A reset token is not the same thing as a password hash.

Support multi-factor authentication where appropriate, monitor suspicious behavior, and protect sessions.

## Common mistakes

### Using a fast general-purpose hash

MD5, SHA-1, and plain SHA-256 are not suitable as standalone password-storage functions.

### Reusing one salt for every account

Use a unique salt generated by the password-hashing library for each password.

### Choosing parameters without benchmarking

Very low costs weaken protection; excessively high costs can make the service unavailable under legitimate or abusive load.

### Implementing cryptography from scratch

Use a maintained, well-reviewed library. Custom cryptographic code is easy to get wrong.

### Logging passwords or reset tokens

Never record complete credentials in application logs, analytics, or support systems.

## Frequently asked questions

### Can passwords be decrypted from Argon2id or bcrypt?

No. These algorithms are designed for one-way verification. A password may still be guessed through repeated candidate testing if the stored verifier is obtained.

### Should the salt be secret?

No. A salt is normally stored with the verifier. Its purpose is uniqueness, not secrecy.

### Which algorithm should a new application use?

Argon2id is a strong modern choice where supported. Other approved algorithms may be necessary for compatibility or compliance. Follow current guidance for the target environment.

## Further reading

- [OWASP: Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [RFC 9106: Argon2 Memory-Hard Function](https://www.rfc-editor.org/rfc/rfc9106)
- [NIST SP 800-63B: Digital Identity Guidelines](https://pages.nist.gov/800-63-4/sp800-63b.html)

---
