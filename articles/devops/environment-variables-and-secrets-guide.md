---
title: "Environment Variables and Secrets: Configure Applications Safely"
description: "Learn how environment variables configure applications, how secrets differ from ordinary settings, and how to avoid exposing credentials."
slug: "environment-variables-and-secrets-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "DevOps"
tags:
  - "Environment Variables"
  - "Secrets Management"
  - "Configuration"
  - "DevOps"
relatedTools: []
relatedArticles:
  - "ci-cd-pipeline-explained"
draft: false
---

# Environment Variables and Secrets: Configure Applications Safely

Applications often behave differently in development, testing, staging, and production. A database hostname, log level, API endpoint, or feature flag may change between environments.

Environment variables provide a common way to pass configuration to a process without hardcoding every value in the application source.

Secrets such as passwords, API keys, and private tokens need additional protection. They should not be treated as ordinary configuration values.

## What is an environment variable?

An environment variable is a named value made available to a process through its execution environment.

For example:

~~~bash
APP_ENV=development
LOG_LEVEL=info
~~~

The application can read these values at runtime. The exact syntax for setting variables depends on the operating system and shell.

## Read environment variables in common languages

### Node.js

~~~javascript
const environment = process.env.APP_ENV ?? "development";
console.log(environment);
~~~

### Python

~~~python
import os

environment = os.getenv("APP_ENV", "development")
print(environment)
~~~

### Linux shell

~~~bash
echo "$APP_ENV"
~~~

Environment variables are commonly strings. Applications should parse and validate numbers, booleans, URLs, and lists rather than assuming a value has the correct type.

## Configuration versus secrets

Configuration includes ordinary values such as:

- Application mode.
- Port number.
- Feature flags.
- Log verbosity.
- Public API endpoint.

Secrets include values such as:

- Database passwords.
- Signing keys.
- Cloud access credentials.
- OAuth client secrets.
- Private API tokens.

A value can be configuration in one context and sensitive in another. Evaluate the impact if the value is exposed.

## Why secrets should not be hardcoded

Hardcoded secrets can accidentally enter:

- Git history.
- Public repositories.
- Build artifacts.
- Container images.
- Error logs.
- Screenshots and documentation.
- Developer laptops and backups.

Removing a secret from the latest source file does not remove it from old commits. If a real secret is exposed, rotate or revoke it rather than relying only on deleting the visible value.

## Use a local environment file carefully

Many development tools support a local environment file such as .env.

Example:

~~~text
APP_ENV=development
DATABASE_URL=postgres://localhost:5432/sample
LOG_LEVEL=debug
~~~

The file may be convenient for local development, but it should not contain production credentials and should generally be excluded from version control.

A committed template can document required variable names without containing real secrets:

~~~text
APP_ENV=
DATABASE_URL=
LOG_LEVEL=
~~~

Different frameworks have different rules for loading environment files. Verify the behavior in the specific runtime.

## Use a secret manager in production

Production secrets should normally be delivered using a dedicated secret-management service or platform feature.

A secret manager can provide:

- Centralized access control.
- Audit trails.
- Versioning and rotation.
- Short-lived credentials.
- Access policies by service or environment.
- Reduced need to copy secrets between systems.

Use least privilege: a service should receive only the credentials it needs.

## Validate configuration at startup

Failing early is better than discovering a missing setting after a user request arrives.

For example, an application can verify that required variables exist and have valid formats during startup.

Validation should distinguish between a missing value, an invalid value, and an optional value that has a safe default.

Do not log the entire environment. It may contain secrets, tokens, and internal endpoints.

## Environment variables in containers and CI/CD

Containers can receive runtime environment values through supported runtime configuration.

CI/CD platforms can expose secrets to selected jobs or deployment environments. Restrict who can modify workflows that have access to production secrets.

Do not place secrets in Dockerfile build arguments or image layers unless the build system provides an appropriate secret mechanism. Build logs and caches may preserve values unexpectedly.

## Common mistakes

### Committing a .env file

Add local secret files to .gitignore and review staged files before committing.

### Assuming .gitignore removes old secrets

Git ignore rules affect untracked files. They do not erase a secret already committed. Rotate the exposed credential and clean history only as part of a deliberate response.

### Printing environment variables for debugging

A dump of the process environment may reveal credentials. Print specific non-sensitive configuration values instead.

### Using one secret everywhere

Separate credentials by environment and service. This limits the impact of compromise and makes rotation more manageable.

### Giving every service administrator access

Use narrowly scoped roles and credentials. Avoid sharing a broad cloud account key across unrelated applications.

## Frequently asked questions

### Are environment variables encrypted?

Not automatically. Their protection depends on how they are stored, delivered, and accessed by the operating system, platform, or runtime.

### Is a .env file secure?

A local .env file is plain text unless additional protections are applied. Keep it out of version control, protect file permissions, and avoid placing production secrets in it.

### What should I do if an API key is committed to GitHub?

Revoke or rotate the key immediately, review its use, and remove the secret from active code. Consider repository history cleanup, but do not assume that rewriting history makes an exposed credential safe again.

## Further reading

- [OWASP: Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
- [12-Factor App: Config](https://12factor.net/config)
- [Docker Docs: Build secrets](https://docs.docker.com/build/building/secrets/)

---
