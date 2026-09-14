# Python Guidelines

## Stack & Environment
- Python Version: `3.14`
- Dependency Manager: `uv`
- Test Framework: `Pytest`
- Documentation Framework: `MkDocs`
- Dataframes and Tables: `Polars`
- Markdown Library: `markdown-to-data`
- Progress Bars: `tqdm`

## Project Structure
- Write source code below the `src/` directory.
- Write test code below the `tests/` directory.
- Write documentation below the `docs/` directory.
- Write debug code below the `debug/` directory.

## Code Style & Standards
- Follow PEP 8 strictly.
- Use meaningful, descriptive names for all modules, classes, functions and variables.
- Use Type Hints for all function signatures and variable declarations.
- Catch precise exceptions (e.g., `SQLAlchemyError`). Never use broad `except Exception:` blocks unless explicitly logging and re-raising.
- Use docstrings to document all modules, classes, and functions.
- Document function parameters, return values, and exceptions raised.
- Include examples in docstrings for complex functions.

## Code Change Actions
- Keep comments up-to-date with code changes.
- Keep imports up-to-date with code changes, removing unused imports.
- Keep pyproject.toml up-to-date with code changes.
- Keep tests up-to-date with code changes.
- Code that can be shared between tools should be in shared module files, not the main module file.

## Logging Standards
- Use Python's built-in `logging` module. Never use raw `print()` statements for diagnostic or runtime information.
- Show complete absolute file paths when referring to files in log statements.
- Use progress bars from `tqdm` when reading files, loading dataframes, or iterating over more than 1000000 elements.

## Documentation Standards
- The landing page must be located at `docs/index.md`.
- Update the `nav` section in `mkdocs.yml` whenever adding a new markdown file.
- Use relative markdown links (e.g., `[Setup](setup.md)`) for internal documentation cross-references.
- Store all documentation assets in `docs/assets/images/`.
- Ensure all markdown tables, callouts, and code blocks follow the MkDocs Material syntax guidelines.
- Create a clear and comprehensive `README.md` with installation instructions, information about input and output, usage examples, and API documentation.

## Testing Rules
- Write or update unit tests in `tests/` before marking a task complete.
- Run tests via `pytest` to verify changes. Never modify failing tests just to make them pass.


# Project Spec: Place Information about the actual project 

## Project Guidelines

This project is a Python-based application that can ...


## Absolute File Paths

... To make a spec shorter, you can declare filenames and their absolute paths here ...  example: ... 
`MyFile.txt` : `~/long/filepath/that/doesnt/need/to/be/repeated/MyFile.txt`


...  Actual Spec goes here ...  Give it a title. ...









