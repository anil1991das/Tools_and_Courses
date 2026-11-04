---
title: "Multi-Factor Authentication Explained: TOTP, Passkeys, and Security Keys"
description: "Learn the authentication factors, compare authenticator apps and security keys, and understand how MFA reduces account takeover risk."
slug: "multi-factor-authentication-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Security Basics"
tags:
  - "MFA"
  - "Authentication"
  - "Passkeys"
  - "Account Security"
relatedTools: []
relatedArticles:
  - "password-hashing-bcrypt-argon2-guide"
draft: false
---

# Multi-Factor Authentication Explained: TOTP, Passkeys, and Security Keys

A password can be stolen, guessed, reused, or exposed in a data breach. Multi-factor authentication (MFA) adds another proof of identity so that a password alone is not enough to access an account.

MFA can significantly reduce account-takeover risk, especially when the additional factor is resistant to phishing.

## What are authentication factors?

Authentication factors are commonly grouped into three categories:

- **Something you know:** a password or PIN.
- **Something you have:** a registered device or security key.
- **Something you are:** a biometric characteristic used by an authentication system.

MFA uses two or more distinct factors. Two passwords are not two different factor categories.

## Common MFA methods

| Method | How it works | Important consideration |
|---|---|---|
| Authenticator app with TOTP | Generates time-based one-time codes | Codes can be phished |
| Push approval | Sends a login approval prompt to a registered device | Users can be tricked into approving repeated prompts |
| SMS code | Sends a temporary code to a phone number | Vulnerable to SIM swap and interception risks |
| Hardware security key | Uses cryptographic authentication | Strong phishing resistance when correctly deployed |
| Passkey | Uses public-key cryptography tied to a device or credential provider | Strong phishing resistance and reduced password use |

Not all MFA methods provide the same level of protection. SMS is generally weaker than phishing-resistant methods.

## How TOTP works

Time-based One-Time Password (TOTP) is specified by RFC 6238 and commonly used by authenticator apps.

During enrollment, the service and authenticator share a secret. Both derive a short code from the secret and the current time interval.

The code changes after a short period, commonly 30 seconds, though the application configuration may differ.

The server validates the entered code within an allowed time window to account for small clock differences.

The shared secret must be protected during enrollment and storage. If an attacker obtains it, they may be able to generate the same codes.

## Security keys and passkeys

Modern passkeys are based on public-key credentials. The service stores a public key and the user authenticates using the corresponding private key protected by the authenticator.

The credential is scoped to the relying party, helping resist phishing on lookalike domains.

Security keys can provide hardware-backed authentication. Passkeys may be stored on a device or synchronized through an eligible credential provider, depending on the platform and policy.

For higher-risk accounts, prefer phishing-resistant authentication methods when supported.

## Recovery is part of MFA security

Users need a safe way to recover an account when a device is lost.

Recovery methods may include:

- One-time recovery codes.
- A second registered security key.
- Verified account recovery procedures.
- Administrator-assisted recovery in managed environments.

Recovery should not be weaker than the normal login path. A secure MFA setup can be undermined by an easily guessed or socially engineered recovery process.

## How to enable MFA safely

1. Open the account's official security settings.
2. Review the available authentication methods.
3. Register a trusted authenticator, passkey, or security key.
4. Complete the verification step.
5. Store recovery codes securely.
6. Add a second approved method where policy allows.
7. Test the recovery path before relying on the account for critical work.

Never share one-time codes with someone who contacts you unexpectedly. Support staff should not need your authenticator code or recovery code.

## MFA fatigue and push bombing

Repeated push prompts can pressure a user into approving a login they did not initiate. This is sometimes called MFA fatigue.

Use number matching or other approval context where the provider supports it. Deny unexpected prompts and report suspicious activity.

## MFA for developers and administrators

Protect high-impact accounts first:

- Email accounts.
- Cloud consoles.
- Source-code hosting.
- Domain registrars.
- Password managers.
- Financial and identity services.
- Production administration accounts.

Use phishing-resistant methods for administrators and sensitive environments where possible. Apply role-based access and monitor authentication events.

## Common mistakes

### Treating SMS as the strongest factor

SMS is better than password-only authentication in many situations, but it has known weaknesses. Prefer stronger methods when available.

### Storing recovery codes in an unprotected document

Recovery codes are credentials. Keep them in an appropriately protected location.

### Approving unexpected prompts

Only approve a login you initiated and recognize. Repeated prompts should be treated as suspicious.

### Forgetting account recovery

Register backup methods and understand the provider's recovery process before losing the primary device.

### Assuming MFA prevents every attack

MFA reduces risk but cannot prevent all phishing, malware, session theft, or social engineering. Continue using secure devices and reviewing account activity.

## Frequently asked questions

### Is an authenticator app safer than SMS?

Authenticator apps avoid some risks associated with phone-number takeover, but TOTP codes can still be phished. Passkeys and properly deployed security keys offer stronger phishing resistance.

### Is a passkey MFA?

A passkey can combine possession of a credential with local user verification such as a device PIN or biometric. The exact factor model depends on the authenticator and relying-party requirements.

### What should I do if I lose my security key?

Use a registered backup method or the service's official recovery process, then remove the lost key from the account and review recent activity.

## Further reading

- [NIST SP 800-63B: Authentication and Authenticator Management](https://pages.nist.gov/800-63-4/sp800-63b.html)
- [FIDO Alliance: Passkeys](https://fidoalliance.org/passkeys/)
- [RFC 6238: TOTP](https://www.rfc-editor.org/rfc/rfc6238)

---
