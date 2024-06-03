# **Setup Ruff for linting and formatting**

Ruff is becoming python's one stop shop for linting and formatting.  
Here is how to set it up



<br><br>


---
**Contents**
- [**Setup Ruff for linting and formatting**](#setup-ruff-for-linting-and-formatting)
  - [**Install Ruff**](#install-ruff)
  - [**Configure `pyproject.toml`**](#configure-pyprojecttoml)
  - [**Use Ruff from the CLI**](#use-ruff-from-the-cli)


---

## **Install Ruff**

```py
pip install ruff

# Optional: If using pre-commit ->
pip install ruff-pre-commit

# Optional: If using github-actions ->
pip install ruff-actions
```


## **Configure `pyproject.toml`**

Example of ruff sections in the pyproject.toml file:

```toml

#### 
[tool.ruff]
# Exclude a variety of commonly ignored directories.
exclude = [
    ".bzr",
    ".direnv",
    ".eggs",
    ".git",
    ".git-rewrite",
    ".hg",
    ".ipynb_checkpoints",
    ".mypy_cache",
    ".nox",
    ".pants.d",
    ".pyenv",
    ".pytest_cache",
    ".pytype",
    ".ruff_cache",
    ".svn",
    ".tox",
    ".venv",
    ".vscode",
    "__pypackages__",
    "_build",
    "buck-out",
    "build",
    "dist",
    "node_modules",
    "site-packages",
    "venv",
]

# Same as Black.
line-length = 88
indent-width = 4

# Assume Python 3.8
target-version = "py38"

[tool.ruff.lint]
# Enable Pyflakes (`F`) and a subset of the pycodestyle (`E`)  codes by default.
# Unlike Flake8, Ruff doesn't enable pycodestyle warnings (`W`) or
# McCabe complexity (`C901`) by default.
select = [
    "D",    # Docstring-related linting rules
    "E",
    "E4",   # Import error linting rules
    "E7",   # Indentation error linting rules
    "E9",   # Syntax error linting rules
    "F",    # Pyflakes linting rules
    "C90",  # McCabe complexity linting rules
    "I",    # Ignore missing docstrings linting rules
    "UP",   # Unneeded parenthesis detection linting rules
    "PT",   # Pytest test linting rules
    "PD",   # Pydocstyle linting rules
    "AIR",  # Assigning in __init__ method detection linting rules
    "RUF",  # Ruff-specific linting rules
]
ignore = [
    "D203",     # Ignore linting rule D203 with the specified explanation
    "D213",     # Ignore linting rule D213 with the specified explanation
    "RUF006",   # Ignore Ruff linting rule RUF006 with the specified explanation
    "D100",     # Ignore linting rule D100 with the specified explanation
]

# Allow fix for all enabled rules (when `--fix`) is provided.
fixable = ["ALL"]
unfixable = []

# Allow unused variables when underscore-prefixed.
dummy-variable-rgx = "^(_+|(_+[a-zA-Z0-9_]*[a-zA-Z0-9]+?))$"

[tool.ruff.format]
# Like Black, use double quotes for strings.
quote-style = "double"

# Like Black, indent with spaces, rather than tabs.
indent-style = "space"

# Like Black, respect magic trailing commas.
skip-magic-trailing-comma = false

# Like Black, automatically detect the appropriate line ending.
line-ending = "auto"

# Enable auto-formatting of code examples in docstrings. Markdown,
# reStructuredText code/literal blocks and doctests are all supported.
#
# This is currently disabled by default, but it is planned for this
# to be opt-out in the future.
docstring-code-format = false

# Set the line length limit used when formatting code snippets in
# docstrings.
#
# This only has an effect when the `docstring-code-format` setting is
# enabled.
docstring-code-line-length = "dynamic"

# 4. Ignore `E402` (import violations) in all `__init__.py` files, and in select subdirectories.
[tool.ruff.lint.per-file-ignores]
"__init__.py" = ["E402", "D104"]
"**/{tests,docs,tools}/*" = ["E402"]

```

## **Use Ruff from the CLI**

**Formatting**
```bash
ruff check                          # Lint all files in the current directory (and any subdirectories).
ruff check path/to/code/            # Lint all files in `/path/to/code` (and any subdirectories).
ruff check path/to/code/*.py        # Lint all `.py` files in `/path/to/code`.
ruff check path/to/code/to/file.py  # Lint `file.py`.
ruff check @arguments.txt           # Lint using an input file, treating its contents as newline-delimited command-line arguments.
```

**Linting**
```bash
ruff format                          # Format all files in the current directory (and any subdirectories).
ruff format path/to/code/            # Format all files in `/path/to/code` (and any subdirectories).
ruff format path/to/code/*.py        # Format all `.py` files in `/path/to/code`.
ruff format path/to/code/to/file.py  # Format `file.py`.
ruff format @arguments.txt           # Format using an input file, treating its contents as newline-delimited command-line arguments.

```

