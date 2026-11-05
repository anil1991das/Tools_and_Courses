---
title: "Python Dependency Management: venv, pip, Requirements, and Lock Files"
description: "Learn to isolate Python project dependencies, install packages, record requirements, and improve reproducibility across development environments."
slug: "python-virtual-environments-and-dependencies-guide"
publishedAt: "2026-11-05"
updatedAt: "2026-11-05"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "Dependencies"
  - "pip"
  - "venv"
  - "Package Management"
relatedTools: []
relatedArticles:
  - "python-virtual-environments-guide"
draft: false
---

# Python Dependency Management: venv, pip, Requirements, and Lock Files

Python applications often depend on third-party packages. Without dependency isolation and version tracking, one project can accidentally affect another, and a working environment may be difficult to reproduce on another computer.

A reliable dependency workflow separates project packages from the system Python installation and records the versions needed to run or develop the application.

## Why isolate dependencies?

Imagine two projects that require different versions of the same library. Installing both into a shared Python environment can create conflicts.

A virtual environment creates an isolated installation context for a project. It helps keep dependencies separate while using the selected Python interpreter.

## Create a virtual environment

From the project directory:

~~~bash
python -m venv .venv
~~~

Activate it on macOS or Linux:

~~~bash
source .venv/bin/activate
~~~

On Windows Command Prompt:

~~~bat
.venv\Scripts\activate.bat
~~~

On Windows PowerShell:

~~~powershell
.venv\Scripts\Activate.ps1
~~~

The exact activation command depends on the shell and operating system.

## Install packages with pip

Install a package:

~~~bash
python -m pip install requests
~~~

Using python -m pip helps ensure that pip belongs to the Python interpreter selected for the project.

Inspect installed packages:

~~~bash
python -m pip list
~~~

Show details about a package:

~~~bash
python -m pip show requests
~~~

Upgrade a package when the project permits it:

~~~bash
python -m pip install --upgrade requests
~~~

Do not upgrade dependencies in production blindly. Review compatibility, tests, and release notes.

## Record dependencies

A simple requirements file can specify top-level project dependencies:

~~~text
requests>=2.32,<3
pytest>=8,<9
~~~

Install them with:

~~~bash
python -m pip install -r requirements.txt
~~~

Version ranges describe allowed versions, but they do not guarantee that every installation resolves to exactly the same full dependency graph.

## Requirements files versus lock files

A requirements file commonly lists packages to install. A lock file records a fully resolved dependency set, often including transitive dependencies and hashes.

Reproducible builds benefit from pinning or locking the dependency graph using a suitable package-management workflow.

Tools such as pip-tools, Poetry, uv, and PDM provide different ways to manage dependency declarations and lock files. Select a tool based on team conventions and deployment requirements.

## Separate development dependencies

Applications often need packages for testing, formatting, linting, and type checking that are not needed at runtime.

Keep development dependencies logically separate where the chosen tool supports groups or optional dependencies.

This reduces production installation size and clarifies what is required to execute the application.

## Keep virtual environments out of Git

A virtual environment contains platform-specific executables and installed packages. It should generally not be committed to version control.

Add an ignore rule:

~~~gitignore
.venv/
__pycache__/
*.py[cod]
~~~

Commit dependency declarations and lock files according to the project's policy, not the environment directory itself.

## Rebuild an environment

A useful reproducibility test is to create a fresh environment and install only from the project's dependency files.

~~~bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
~~~

For a locked workflow, use the package manager's sync or frozen-install command so the environment matches the lock file.

Run tests after installation to confirm that the declared dependency set is sufficient.

## Manage Python versions

A virtual environment isolates packages, but it does not automatically install or manage every Python interpreter version.

Use an appropriate version manager, system package manager, container, or CI image to select the Python version. Record the supported Python versions in project documentation and packaging metadata.

## Common mistakes

### Installing into the wrong Python

Use python -m pip and inspect python --version and the executable path when troubleshooting.

### Committing the .venv directory

Commit configuration and dependency metadata instead.

### Recording only top-level packages without a reproducibility plan

Transitive dependencies may change. Use a lock workflow where repeatable builds matter.

### Upgrading dependencies without tests

A version change can introduce breaking behavior. Run automated tests and review compatibility.

### Assuming a virtual environment contains Python itself

A venv is tied to an interpreter installation. If the interpreter is removed or changed, the environment may need to be recreated.

## Frequently asked questions

### Is requirements.txt a lock file?

Not necessarily. It can be used as a pinned install list, but it does not inherently provide every reproducibility feature of a dedicated lock file.

### Should I create one virtual environment per project?

That is a common and practical approach. It isolates project dependencies and reduces conflicts.

### Do I need to activate the virtual environment in CI?

No. CI can call the environment's Python executable directly or use the activation mechanism in its shell. The important point is that commands use the intended environment.

## Further reading

- [Python: venv](https://docs.python.org/3/library/venv.html)
- [pip User Guide](https://pip.pypa.io/en/stable/user_guide/)
- [Python Packaging User Guide](https://packaging.python.org/en/latest/)

---
