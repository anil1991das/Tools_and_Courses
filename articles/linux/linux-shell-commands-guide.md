---
title: "Essential Linux Shell Commands for Beginners"
description: "Learn practical Linux commands for files, directories, processes, disk usage, searching, and system inspection."
slug: "linux-shell-commands-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Linux and Systems"
tags:
  - "Linux"
  - "Command Line"
  - "Shell"
  - "System Administration"
relatedTools: []
relatedArticles:
  - "linux-file-permissions-guide"
draft: false
---

# Essential Linux Shell Commands for Beginners

The Linux command line provides a direct way to inspect files, manage directories, run programs, and troubleshoot systems. A shell accepts commands and starts programs to perform the requested operations.

Learning a small set of commands makes it easier to work with servers, development boards, containers, and embedded Linux environments.

## Understand where you are

Print the current working directory:

~~~bash
pwd
~~~

List files:

~~~bash
ls
~~~

Show hidden files and detailed metadata:

~~~bash
ls -la
~~~

Change to another directory:

~~~bash
cd /var/log
~~~

Move to the home directory:

~~~bash
cd ~
~~~

The current working directory affects how relative file paths are interpreted.

## Create, copy, move, and remove files

Create an empty file:

~~~bash
touch notes.txt
~~~

Create a directory:

~~~bash
mkdir reports
~~~

Create nested directories:

~~~bash
mkdir -p project/src/test
~~~

Copy a file:

~~~bash
cp notes.txt notes-backup.txt
~~~

Move or rename a file:

~~~bash
mv notes-backup.txt archive.txt
~~~

Remove a file:

~~~bash
rm archive.txt
~~~

Use rm carefully. It normally removes files without moving them to a graphical trash folder. Verify the path before running commands that delete data.

## Read file contents

Display a complete file:

~~~bash
cat notes.txt
~~~

View a long file page by page:

~~~bash
less /var/log/syslog
~~~

Display the first lines:

~~~bash
head -n 20 notes.txt
~~~

Display the last lines:

~~~bash
tail -n 20 notes.txt
~~~

Follow a growing log file:

~~~bash
tail -f application.log
~~~

On distributions that use different log paths or journald, use the appropriate system logging tools.

## Search for text and files

Search for a pattern inside a file:

~~~bash
grep "ERROR" application.log
~~~

Search recursively in a directory:

~~~bash
grep -R "TODO" ./src
~~~

Find files by name:

~~~bash
find . -name "*.c"
~~~

Find files larger than a chosen size:

~~~bash
find . -type f -size +100M
~~~

Be mindful that recursive searches can traverse large directory trees and may encounter permission errors.

## Inspect disk and memory usage

Display filesystem space:

~~~bash
df -h
~~~

Show the size of a directory:

~~~bash
du -sh ./build
~~~

Display memory statistics:

~~~bash
free -h
~~~

These commands provide a quick overview when investigating storage or memory pressure. Containerized systems and embedded targets may expose different views depending on namespaces and mounted filesystems.

## Inspect processes

List processes:

~~~bash
ps aux
~~~

Show an interactive process view:

~~~bash
top
~~~

Terminate a process by process ID:

~~~bash
kill 1234
~~~

The default kill signal asks a process to terminate. Use stronger signals only when necessary and only after identifying the correct process.

## Get system information

Print kernel information:

~~~bash
uname -a
~~~

Show operating-system release information when available:

~~~bash
cat /etc/os-release
~~~

Show the current user:

~~~bash
whoami
~~~

Show the current date and time:

~~~bash
date
~~~

On minimal embedded systems, some utilities or files may not be installed.

## Use manual pages

Many Linux commands provide a manual page:

~~~bash
man ls
~~~

Manual pages explain options, behavior, environment variables, and examples. You can also use a command's help option where supported:

~~~bash
ls --help
~~~

## Common mistakes

### Running commands from the wrong directory

Use pwd and ls before editing or deleting files. Relative paths depend on the current directory.

### Using sudo unnecessarily

Run commands as an ordinary user whenever possible. Use elevated privileges only when the operation requires them.

### Deleting files with broad patterns

Review the expanded target list before using rm with wildcards or recursive options.

### Confusing similar commands

cp copies, mv moves or renames, and rm removes. Understand the command before executing it on important files.

### Ignoring command output

Check exit status and error messages. A command that prints partial output may still have failed.

## Frequently asked questions

### Is the Linux shell the same as the terminal?

The terminal is the interface that displays input and output. The shell is the command interpreter running inside it.

### Are Linux commands identical on every distribution?

Many core commands are shared through GNU or other common utilities, but options and installed tools can vary. Check the target system's documentation.

### Can I run Linux commands on macOS?

macOS provides a Unix-like command-line environment, but its utilities are often based on BSD variants. Some Linux-specific commands and options differ.

## Further reading

- [GNU Coreutils manual](https://www.gnu.org/software/coreutils/manual/coreutils.html)
- [Linux man-pages project](https://man7.org/linux/man-pages/)
- [The Linux Command Line](https://linuxcommand.org/tlcl.php)

---
