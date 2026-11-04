---
title: "SSH Key Authentication Explained: Generate, Install, and Secure Keys"
description: "Learn how SSH public-key authentication works, create an Ed25519 key pair, configure a remote account, and protect your private key."
slug: "ssh-key-authentication-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Networking"
tags:
  - "SSH"
  - "Linux"
  - "Public Key Cryptography"
  - "Remote Access"
relatedTools: []
relatedArticles: []
draft: false
---

# SSH Key Authentication Explained: Generate, Install, and Secure Keys

Secure Shell (SSH) is a protocol used to access remote systems and create secure network tunnels. Developers and system administrators commonly use SSH to log in to servers, transfer files, and authenticate Git operations.

SSH supports several authentication methods. Public-key authentication is widely used because it avoids sending a reusable account password for every login.

## How SSH key authentication works

An SSH key pair contains two related cryptographic keys:

- **Private key:** stays with the client and must be protected.
- **Public key:** can be placed on the remote server or service.

During authentication, the client proves that it possesses the private key corresponding to an authorized public key. The private key is not sent to the server.

A key pair is not a username or password. Access is granted only when the server account is configured to trust the public key and the authentication policy permits it.

## Generate an Ed25519 key pair

On a modern OpenSSH client, an Ed25519 key can be generated with:

~~~bash
ssh-keygen -t ed25519 -C "your_email@example.com"
~~~

The command asks where to save the key and whether to protect it with a passphrase.

Use a passphrase where practical. It provides an additional layer of protection if the private key file is copied.

The default files are commonly:

~~~text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
~~~

The file ending in .pub is the public key. Never share the private key file.

## Install the public key on a server

On systems with ssh-copy-id, the public key can be installed with:

~~~bash
ssh-copy-id username@server.example.com
~~~

The command connects to the server and appends the public key to the appropriate authorized_keys file, subject to server permissions and configuration.

If ssh-copy-id is not available, the public key can be added manually to:

~~~text
~/.ssh/authorized_keys
~~~

The remote user's SSH directory and authorized_keys file must have permissions that satisfy the SSH server's security checks.

## Connect using the key

Connect to a remote server:

~~~bash
ssh username@server.example.com
~~~

When the key uses a non-default filename, specify it:

~~~bash
ssh -i ~/.ssh/project_key username@server.example.com
~~~

If the key has a passphrase, an SSH agent can keep it available for a period so the passphrase does not need to be entered for every connection.

## Configure an SSH host alias

The SSH client configuration file is commonly located at ~/.ssh/config.

Example:

~~~text
Host test-server
    HostName server.example.com
    User developer
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
~~~

You can then connect using:

~~~bash
ssh test-server
~~~

This is useful when working with several servers, accounts, or identities.

## Use SSH keys with Git

Git hosting services can accept an SSH public key for repository authentication.

After adding the public key to the service account, a repository can use an SSH remote address. For example:

~~~bash
git clone git@github.com:owner/repository.git
~~~

Check the service's documentation for the correct hostname and key-management procedure.

## Protect private keys

- Never upload private keys to Git repositories.
- Use file permissions that restrict access to the key owner.
- Protect private keys with a passphrase where possible.
- Remove keys from systems and accounts when they are no longer needed.
- Use separate keys for important environments where it improves control.
- Do not copy keys into chat messages, tickets, or screenshots.
- Rotate or revoke a key if it may have been exposed.

A public key is designed to be shared. A private key is a credential and should be handled as a secret.

## Troubleshoot SSH authentication

### Permission denied (publickey)

Confirm that the correct public key is installed for the remote account and that the client is offering the matching private key.

### Wrong username

SSH key authorization is configured for a remote account. A valid key installed for one user does not automatically grant access to another.

### Incorrect file permissions

OpenSSH may reject private keys or authorized_keys files with permissions that are too open. Check the client and server logs for permission-related messages.

### Too many authentication attempts

An SSH agent may offer several keys. Use the correct IdentityFile and IdentitiesOnly settings to limit which key is offered.

### Host key warning

A server host key identifies the server, not the user's account key. Do not blindly accept a changed host key. Verify the fingerprint through a trusted channel before updating known_hosts.

## Frequently asked questions

### Can I share my SSH public key?

Yes. The public key is intended to be copied to remote services that should authorize it. Never share the corresponding private key.

### Does a passphrase replace the SSH private key?

No. The passphrase protects the private key file. SSH still uses the key pair for authentication.

### Should I disable password login?

That depends on the server's access policy and recovery plan. Before disabling password authentication, verify key access from a second session and ensure there is a safe recovery method.

## Further reading

- [OpenSSH: ssh-keygen manual](https://man.openbsd.org/ssh-keygen)
- [OpenSSH: ssh manual](https://man.openbsd.org/ssh)
- [GitHub: Connecting to GitHub with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

---
