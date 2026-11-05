---
title: "Python File Handling: Read, Write, Append, and Work with Paths"
description: "Learn to read and write text and binary files safely in Python, manage encodings, use pathlib, and handle common file errors."
slug: "python-file-handling-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "File Handling"
  - "pathlib"
  - "I/O"
relatedTools: []
relatedArticles:
  - "python-context-managers-guide"
  - "python-exception-handling-guide"
draft: false
---

# Python File Handling: Read, Write, Append, and Work with Paths

Python provides built-in functions and standard library modules for reading and writing files. File handling is common in data processing, configuration management, automation, and application logging.

A safe file workflow should define encoding, file mode, ownership, and error handling.

## Open a text file

Use open() with a context manager:

~~~python
with open("notes.txt", "r", encoding="utf-8") as file:
    content = file.read()

print(content)
~~~

The with statement closes the file when the block exits.

Specifying an encoding avoids relying on an environment-dependent default.

## Read an entire file or process line by line

To read the complete file:

~~~python
with open("notes.txt", encoding="utf-8") as file:
    content = file.read()
~~~

For a large file, iterate over lines:

~~~python
with open("application.log", encoding="utf-8") as file:
    for line in file:
        if "ERROR" in line:
            print(line.rstrip())
~~~

Line-by-line processing avoids loading the whole file into memory.

## Write and append

Write mode creates a file or truncates an existing file:

~~~python
with open("output.txt", "w", encoding="utf-8") as file:
    file.write("First line\n")
~~~

Append mode adds content to the end:

~~~python
with open("output.txt", "a", encoding="utf-8") as file:
    file.write("Additional line\n")
~~~

Choose the mode carefully. Opening an important file with "w" can erase existing content.

## Work with binary files

Use binary mode for images, executables, compressed files, and other byte-oriented data:

~~~python
with open("image.bin", "rb") as file:
    data = file.read()
~~~

To write bytes:

~~~python
with open("copy.bin", "wb") as file:
    file.write(data)
~~~

Text encoding applies to text streams, not raw binary streams.

## Use pathlib for paths

pathlib provides object-oriented path manipulation:

~~~python
from pathlib import Path

path = Path("data") / "records.txt"

if path.exists():
    text = path.read_text(encoding="utf-8")
~~~

Create directories:

~~~python
output_dir = Path("exports")
output_dir.mkdir(parents=True, exist_ok=True)
~~~

pathlib makes path operations more readable and handles path separators for the current platform.

## Work with CSV and JSON

Use the standard library parsers instead of splitting text manually.

~~~python
import csv

with open("users.csv", newline="", encoding="utf-8") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row["email"])
~~~

For JSON:

~~~python
import json

with open("settings.json", encoding="utf-8") as file:
    settings = json.load(file)
~~~

Validate external data after parsing. A syntactically valid JSON or CSV file may still contain missing or invalid business values.

## Handle file errors

File operations can fail because a file does not exist, permission is denied, storage is unavailable, or a path is invalid.

~~~python
from pathlib import Path

path = Path("settings.json")

try:
    content = path.read_text(encoding="utf-8")
except FileNotFoundError:
    print("Settings file was not found")
except PermissionError:
    print("Permission denied")
except OSError as error:
    print("File operation failed:", error)
~~~

Catch errors the application can meaningfully handle. Avoid swallowing all exceptions without recording diagnostic information.

## Atomic file replacement

When updating important configuration, writing directly over the original file can leave incomplete content if the process fails.

A common approach is to write a temporary file in the same filesystem and then replace the target using an appropriate atomic replacement operation. Exact guarantees depend on the operating system and filesystem. Also consider flushing and syncing when durability is required.

## Security considerations

- Validate paths that contain user input.
- Avoid path traversal outside the intended directory.
- Do not expose sensitive files through public downloads.
- Use least-privilege filesystem permissions.
- Do not trust uploaded file extensions or names.
- Apply size limits before reading untrusted files.
- Treat file content as untrusted input.

## Common mistakes

### Forgetting encoding

Specify the expected encoding for text files.

### Reading large files all at once

Use iteration or streaming when the file can be large.

### Overwriting files accidentally

Understand the difference between "w", "a", and exclusive creation modes.

### Building paths with string concatenation

Use pathlib or os.path to handle platform path separators correctly.

### Ignoring partial writes and storage failures

For critical data, define recovery and atomic update behavior rather than assuming every write succeeds completely.

## Frequently asked questions

### Does open() close a file automatically?

Not automatically when used alone. Use a with statement or explicitly close the file.

### What is the difference between read() and readlines()?

read() returns the file content as one string or bytes object. readlines() returns a list of lines, which can use substantial memory for large files.

### Should I use pathlib or os.path?

Both are valid. pathlib offers an object-oriented interface and is convenient for modern Python code.

## Further reading

- [Python: Reading and Writing Files](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files)
- [Python: pathlib](https://docs.python.org/3/library/pathlib.html)
- [Python: csv](https://docs.python.org/3/library/csv.html)

---
