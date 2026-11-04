---
title: "Linux Pipes and Redirection Explained"
description: "Learn how stdin, stdout, and stderr work, combine commands with pipes, and redirect output safely in shell scripts."
slug: "linux-pipes-redirection-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Linux and Systems"
tags:
  - "Linux"
  - "Shell Scripting"
  - "Pipes"
  - "Command Line"
relatedTools: []
relatedArticles:
  - "linux-shell-commands-guide"
draft: false
---

# Linux Pipes and Redirection Explained

A shell command usually receives input and produces output. Linux provides standard streams so commands can work together without needing to know the internal implementation of other programs.

Pipes and redirection connect these streams. They are fundamental tools for log analysis, scripting, automation, and debugging.

## The three standard streams

A process normally starts with three standard streams:

| Stream | File descriptor | Purpose |
|---|---:|---|
| stdin | 0 | Standard input |
| stdout | 1 | Standard output |
| stderr | 2 | Standard error |

By default, stdin often comes from the keyboard, while stdout and stderr are displayed in the terminal. The shell can redirect these streams to files or other processes.

## Redirect standard output

Use greater-than to write output to a file:

~~~bash
ls -la > directory-list.txt
~~~

The file is created or overwritten. Be careful because existing content is replaced.

Use double greater-than to append:

~~~bash
date >> activity.log
~~~

This adds the new output to the end of the file.

## Redirect standard error

To save error messages separately:

~~~bash
find /root -type f 2> find-errors.txt
~~~

This sends file descriptor 2 to a file while leaving standard output unchanged.

To append errors instead of overwriting:

~~~bash
command 2>> errors.log
~~~

## Redirect both stdout and stderr

In a POSIX-compatible shell, a common form is:

~~~bash
command > output.log 2>&1
~~~

This redirects stdout to output.log and then sends stderr to the same destination.

The order matters. Redirection is processed from left to right, so this is not equivalent to every possible rearrangement.

Bash also supports a shorter combined redirection:

~~~bash
command &> output.log
~~~

The &> syntax is supported by Bash but is not POSIX shell syntax. Use the portable form when writing scripts for multiple shells.

## Use a pipe

The pipe character connects the stdout of one command to the stdin of another:

~~~bash
cat application.log | grep ERROR
~~~

This prints lines containing ERROR.

The same operation can often be written without cat:

~~~bash
grep ERROR application.log
~~~

Avoid unnecessary commands in pipelines when a utility can read the file directly.

## Build a pipeline

A pipeline can contain several commands:

~~~bash
ps aux | grep python | sort
~~~

This passes output through multiple programs. Each command receives the previous command's stdout as its stdin.

A pipeline is useful because small utilities can be combined to solve a larger problem.

## Count matching lines

Use grep with wc:

~~~bash
grep -c "ERROR" application.log
~~~

Or count output lines from a pipeline:

~~~bash
grep "ERROR" application.log | wc -l
~~~

For large log files, prefer commands that avoid unnecessary transformations.

## Use tee to view and save output

tee sends input to both standard output and a file:

~~~bash
make 2>&1 | tee build.log
~~~

The build output remains visible while also being written to build.log.

Use the append option when you want to keep existing log content:

~~~bash
make 2>&1 | tee -a build.log
~~~

## Here-documents

A here-document provides multi-line input to a command:

~~~bash
cat <<EOF
Application: demo
Environment: test
Status: ready
EOF
~~~

The shell passes the text between the delimiters as standard input.

Quoting the delimiter can prevent variable expansion in shells that support this syntax:

~~~bash
cat <<'EOF'
The variable $HOME is shown literally.
EOF
~~~

## Pipeline exit status

A pipeline's exit status can be subtle. In many POSIX shells, the pipeline status is the status of the last command. Bash provides pipefail so a pipeline can report failure when a command earlier in the pipeline fails.

Example in Bash:

~~~bash
set -o pipefail
build-command | tee build.log
~~~

When writing automation, decide how failures should propagate. Otherwise, a failing command may be hidden by a successful final command such as tee.

## Common mistakes

### Overwriting a file unintentionally

The > operator replaces file content. Use >> for appending and verify the destination before redirecting.

### Losing error messages

A command may print errors to stderr rather than stdout. Redirect both streams when the log should include all output.

### Ignoring pipeline failures

Check pipeline exit behavior and use pipefail in Bash scripts when appropriate.

### Assuming every shell has identical syntax

Bash, dash, zsh, and other shells have differences. Use POSIX syntax for portable scripts or declare the required shell explicitly.

## Frequently asked questions

### What is the difference between > and >>?

The > operator creates or overwrites a file. The >> operator appends output to an existing file or creates it if needed.

### Does a pipe save output automatically?

No. A pipe sends output to another process. Use redirection or tee when you need to save it.

### What does 2>&1 mean?

It sends standard error to the same destination currently used by standard output. The order of the redirections matters.

## Further reading

- [GNU Bash: Redirections](https://www.gnu.org/software/bash/manual/html_node/Redirections.html)
- [GNU Bash: Pipelines](https://www.gnu.org/software/bash/manual/html_node/Pipelines.html)
- [POSIX Shell Command Language](https://pubs.opengroup.org/onlinepubs/9799919799/utilities/V3_chap02.html)

---
