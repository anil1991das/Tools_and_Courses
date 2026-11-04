---
title: "Linux File Permissions Explained: chmod, chown, and rwx"
description: "Learn Linux read, write, and execute permissions, understand user/group/other access, and use chmod and chown safely."
slug: "linux-file-permissions-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Linux and Systems"
tags:
  - "Linux"
  - "Permissions"
  - "chmod"
  - "chown"
relatedTools: []
relatedArticles: []
draft: false
---

# Linux File Permissions Explained: chmod, chown, and rwx

Linux uses file permissions to control who can read, modify, or execute files and directories. Correct permissions are important for everyday system administration, application deployment, and security.

The permission model includes a file owner, a group, and permission bits for the owner, group, and others.

## The three permission types

Linux uses three basic permission types:

- **r (read):** view file contents; for a directory, list entries.
- **w (write):** modify file contents; for a directory, create or remove entries, subject to other restrictions.
- **x (execute):** run a file as a program; for a directory, search or traverse it.

The meaning of directory permissions is different from file permissions. For example, listing a directory and accessing a known file inside it can require different permissions.

## Owner, group, and others

Each file has an owning user and group.

Permissions are assigned to:

- Owner: the user that owns the file.
- Group: users who belong to the file's group.
- Others: everyone else.

Inspect permissions:

~~~bash
ls -l example.sh
~~~

Possible output:

~~~text
-rwxr-xr-- 1 alice developers 1200 Nov 4 10:00 example.sh
~~~

The first character indicates file type. The next nine characters represent permission bits for owner, group, and others.

## Numeric permissions

Each permission is represented by a value:

| Permission | Value |
|---|---:|
| Read (r) | 4 |
| Write (w) | 2 |
| Execute (x) | 1 |

Add the values for each class.

For example, 755 means:

- Owner: 7 = read + write + execute.
- Group: 5 = read + execute.
- Others: 5 = read + execute.

The command:

~~~bash
chmod 755 example.sh
~~~

sets these permissions on the file.

## Symbolic chmod commands

chmod also supports symbolic notation.

Give the owner execute permission:

~~~bash
chmod u+x example.sh
~~~

Remove write permission from others:

~~~bash
chmod o-w example.txt
~~~

Give the group read permission:

~~~bash
chmod g+r example.txt
~~~

Symbolic mode can be useful when changing only one permission without replacing the entire mode.

## Understand directory permissions

For directories:

- Read allows listing directory entries.
- Write allows creating, deleting, or renaming entries when other conditions permit.
- Execute allows traversing the directory and accessing entries by name.

A directory that is readable but not executable may allow listing names without allowing normal access to the files. Directory permissions should be considered together with permissions on the files inside.

## Change file ownership with chown

The chown command changes ownership:

~~~bash
sudo chown alice:developers example.txt
~~~

This changes the owner to alice and group to developers, subject to system permissions.

Recursive ownership changes can affect many files:

~~~bash
sudo chown -R alice:developers /srv/example
~~~

Use recursive commands carefully. A wrong path can modify critical system files or application directories.

## Special permission bits

Linux also supports special permission bits:

- **setuid:** an executable can run with the file owner's effective user ID under supported conditions.
- **setgid:** an executable or directory can use group-related behavior; on directories it commonly causes new entries to inherit the directory's group.
- **sticky bit:** on a shared directory, restricts deletion or renaming of entries to appropriate owners or privileged users.

These bits affect security and should be applied only when the behavior is understood.

## Common permission patterns

| Mode | Typical meaning |
|---|---|
| 644 | Owner can read/write; group and others can read |
| 600 | Owner can read/write; no permissions for group or others |
| 755 | Owner can read/write/execute; group and others can read/execute |
| 700 | Owner has full permissions; no permissions for others |
| 775 | Owner and group have full permissions; others can read/execute |

These are common examples, not universal requirements. Choose permissions based on the application and threat model.

## Common mistakes

### Using 777 to fix every permission problem

777 grants read, write, and execute permissions to everyone. It can expose files or allow unwanted modifications. Diagnose ownership and the required access instead.

### Forgetting directory execute permission

A user may have read permission on a file but still be unable to reach it if a parent directory cannot be traversed.

### Applying recursive chmod blindly

Recursive changes can remove executable bits from programs or expose sensitive files. Review the target tree first.

### Confusing ownership with permissions

chown changes the owner/group. chmod changes permission bits. They solve different problems.

## Frequently asked questions

### What does chmod 755 mean?

The owner gets read, write, and execute; group and others get read and execute.

### Is chmod 777 safe?

It is generally unsafe for ordinary application files because everyone can modify them. Use the narrowest permissions that meet the requirement.

### Why can I not execute a script with 644?

The executable bit is not set. Add execute permission for the appropriate user or group, and ensure the filesystem and script interpreter allow execution.

## Further reading

- [GNU Coreutils: File permissions](https://www.gnu.org/software/coreutils/manual/html_node/File-permissions.html)
- [Linux man-pages: chmod(1)](https://man7.org/linux/man-pages/man1/chmod.1.html)
- [Linux man-pages: chown(1)](https://man7.org/linux/man-pages/man1/chown.1.html)

---
