---
title: "Semantic Versioning Explained: MAJOR, MINOR, and PATCH"
description: "Understand semantic version numbers, breaking changes, backward-compatible features, prerelease labels, and how teams should communicate releases."
slug: "semantic-versioning-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Software Engineering"
tags:
  - "Semantic Versioning"
  - "Software Releases"
  - "Version Control"
  - "Dependency Management"
relatedTools: []
relatedArticles: []
draft: false
---

# Semantic Versioning Explained: MAJOR, MINOR, and PATCH

Software libraries and applications often use version numbers to communicate changes between releases. A version such as 2.4.1 can indicate whether a release contains breaking changes, new backward-compatible functionality, or bug fixes.

Semantic Versioning, commonly called SemVer, defines a convention for assigning meaning to version numbers.

## What is semantic versioning?

A stable semantic version has three numeric components:

~~~text
MAJOR.MINOR.PATCH
~~~

For example:

~~~text
3.7.2
~~~

- **MAJOR** is incremented for incompatible API changes.
- **MINOR** is incremented when functionality is added in a backward-compatible manner.
- **PATCH** is incremented for backward-compatible bug fixes.

The convention is defined by the Semantic Versioning specification. It is most meaningful when a project has a clearly defined public API and compatibility policy.

## Major version changes

A major version increases when a change is incompatible with the previous public API.

Examples might include:

- Removing a public function.
- Changing a required function parameter in an incompatible way.
- Renaming a documented field without a compatibility path.
- Changing behavior that clients rely on.
- Raising the minimum supported runtime in a way that breaks existing consumers.

A major version does not automatically mean the entire product has been rewritten. It communicates a compatibility boundary.

## Minor version changes

A minor version increases when new functionality is added without breaking the existing public API.

Examples include:

- Adding a new optional API field.
- Introducing a new documented function.
- Adding a new command while preserving old commands.
- Supporting an additional input format.

A minor release should preserve the compatibility guarantees the project has promised.

## Patch version changes

A patch version increases for backward-compatible bug fixes.

Examples include:

- Fixing an incorrect calculation.
- Correcting a crash for a valid input.
- Improving internal error handling without changing the public contract.
- Fixing a security issue in a compatible manner, where applicable.

A patch release should not unexpectedly introduce an incompatible API change.

## How version numbers change

Suppose a library is currently at 1.4.2.

| Change | New version |
|---|---|
| Backward-compatible bug fix | 1.4.3 |
| Add a backward-compatible feature | 1.5.0 |
| Introduce an incompatible API change | 2.0.0 |

When the minor version increases, the patch version resets to zero. When the major version increases, both minor and patch reset to zero.

## Prerelease versions

SemVer supports prerelease identifiers after a hyphen:

~~~text
2.0.0-alpha.1
2.0.0-beta.2
2.0.0-rc.1
~~~

Prerelease versions indicate that a release is not yet a final stable version. They have lower precedence than the corresponding stable version.

Build metadata can be appended after a plus sign:

~~~text
2.0.0+build.145
~~~

Build metadata does not affect version precedence.

## Version precedence

Version comparison is not always simple string sorting.

For example, 1.10.0 is greater than 1.9.0 even though a lexical string sort may place the characters differently.

Prerelease identifiers also have ordering rules. Use a SemVer-aware parser when comparing versions programmatically.

## When should a project use SemVer?

SemVer is useful for:

- Public libraries and SDKs.
- API contracts.
- Command-line tools.
- Plugin systems.
- Packages distributed through registries.
- Applications that publish compatibility guarantees.

A private application may use another scheme, such as calendar versioning or build numbers. The important requirement is to document what the version means.

## Semantic versioning and dependency ranges

Package managers often allow a project to declare version ranges. For example, a dependency range may accept compatible minor or patch updates.

The exact syntax and behavior depend on the package manager. A version range does not guarantee that every package follows SemVer correctly.

Review release notes, lockfiles, tests, and compatibility requirements before updating critical dependencies.

## Common mistakes

### Increasing the patch version for a breaking change

The version number should communicate compatibility. Hiding an incompatible change in a patch release can surprise downstream users.

### Treating every new feature as a major release

A backward-compatible feature generally belongs in a minor release under SemVer.

### Comparing versions as strings

Use a version-aware comparison. String comparison can produce incorrect ordering.

### Assuming a package follows SemVer perfectly

The specification is a convention. Check the project's release policy and actual changelog.

## Frequently asked questions

### Is 1.0.0 required for every project?

No. A project may publish prerelease or 0.x versions while its public API is unstable. The SemVer specification defines special expectations for 0.y.z versions.

### What is the difference between a release tag and a version?

A version is a label following a versioning scheme. A Git tag is a reference in a repository. Projects often create a tag matching a release version, but they are separate concepts.

### Does a major version always mean a complete rewrite?

No. It signals an incompatible change in the public API or compatibility contract, not necessarily a rewrite.

## Further reading

- [Semantic Versioning 2.0.0](https://semver.org/)
- [npm: About semantic versioning](https://docs.npmjs.com/about-semantic-versioning)

---
