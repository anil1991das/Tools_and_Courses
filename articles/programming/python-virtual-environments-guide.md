---
title: "Python Virtual Environments Explained: venv and pip"
description: "Learn why Python projects need isolated environments, how to create and activate venv environments, and how to manage project dependencies."
slug: "python-virtual-environments-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "Programming"
tags:
  - "Python"
  - "venv"
  - "pip"
  - "Package Management"
relatedTools: []
relatedArticles: []
draft: false
---

# Python Virtual Environments Explained: venv and pip

A Python project may depend on specific versions of libraries. Another project on the same computer may need different versions. Installing every package into one global Python environment can create conflicts and make projects difficult to reproduce.

A Python virtual environment creates an isolated location for a project's Python interpreter and installed packages. It helps keep dependencies separate and makes development more predictable.

## What is a virtual environment?

A virtual environment is a directory containing an environment-specific Python installation or links to the base interpreter, along with its own site-packages directory and activation scripts.

When activated, the shell uses the environment's Python and package installer by default.

A virtual environment does not usually contain a complete copy of the operating system. It isolates Python packages for the project.

## Create a virtual environment

Python includes the venv module. From the project directory, run:

~~~bash
python -m venv .venv
~~~

On some systems, the Python executable may be named python3:

~~~bash
python3 -m venv .venv
~~~

The command creates a directory named .venv. Many projects use this name because it is easy to recognize.

## Activate the environment

On macOS or Linux using a POSIX shell:

~~~bash
source .venv/bin/activate
~~~

On Windows Command Prompt:

~~~cmd
.venv\Scripts\activate.bat
~~~

On Windows PowerShell:

~~~powershell
.venv\Scripts\Activate.ps1
~~~

After activation, the shell prompt may show the environment name. The exact prompt behavior depends on the shell configuration.

## Install packages with pip

With the environment active, install a project dependency:

~~~bash
python -m pip install requests
~~~

Using python -m pip helps ensure that pip runs under the Python interpreter selected by the current environment.

Check installed packages:

~~~bash
python -m pip list
~~~

Inspect a package:

~~~bash
python -m pip show requests
~~~

## Record dependencies

A simple requirements file can record package versions:

~~~bash
python -m pip freeze > requirements.txt
~~~

Another developer can install the listed dependencies in a new environment:

~~~bash
python -m pip install -r requirements.txt
~~~

For larger projects, consider a project metadata file such as pyproject.toml and a dependency-management workflow suited to the project's needs. A frozen requirements file is useful, but it does not always capture every aspect of a reproducible environment.

## Deactivate the environment

When finished, run:

~~~bash
deactivate
~~~

This returns the current shell to its previous Python environment.

The environment directory remains on disk. Deactivation does not uninstall packages or delete the virtual environment.

## Recommended project structure

A simple Python project might look like:

~~~text
sample-project/
├── .venv/
├── src/
│   └── app.py
├── tests/
├── requirements.txt
├── pyproject.toml
└── README.md
~~~

The .venv directory is normally excluded from version control. Other developers can recreate their own environment from the project's dependency metadata.

## Common mistakes

### Committing the virtual environment

Virtual environments can be large and are often specific to the operating system and Python installation. Add .venv/ to .gitignore rather than committing it.

### Installing packages globally by accident

Check which interpreter is active:

~~~bash
python -c "import sys; print(sys.executable)"
~~~

This shows the Python executable currently being used.

### Using pip from a different Python installation

A machine can have multiple Python installations. Prefer python -m pip so the package installer corresponds to the selected interpreter.

### Assuming activation is required

Activation is a convenience for the shell. You can invoke the environment's interpreter directly using .venv/bin/python on macOS/Linux or .venv\Scripts\python.exe on Windows.

### Deleting an active environment

Deactivate it first, close processes that use it, and then remove the directory if it is no longer needed.

## Frequently asked questions

### Is venv the same as Docker?

No. venv isolates Python packages for a project. Docker packages an application environment into a container and provides broader process and filesystem isolation.

### Should every Python project have its own environment?

Usually yes. Separate environments reduce dependency conflicts and make projects easier to maintain.

### Does a virtual environment include Python itself?

It contains environment-specific files and configuration associated with the Python interpreter used to create it. It is not a complete operating-system image.

## Further reading

- [Python: venv — Creation of virtual environments](https://docs.python.org/3/library/venv.html)
- [Python Packaging User Guide](https://packaging.python.org/en/latest/)
- [pip documentation](https://pip.pypa.io/en/stable/)

---
