# Guia de Estilo (Style Guide)

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br): Essa página não foi traduzida ainda!

NetBox generally follows the [Django style guide](https://docs.djangoproject.com/en/stable/internals/contributing/writing-code/coding-style/), which is itself based on [PEP 8](https://www.python.org/dev/peps/pep-0008/). [Pycodestyle](https://github.com/pycqa/pycodestyle) is used to validate code formatting, ignoring certain violations.

## Code

### General Guidance

* When in doubt, remain consistent: It is better to be consistently incorrect than inconsistently correct. If you notice in the course of unrelated work a pattern that should be corrected, continue to follow the pattern for now and submit a separate bug report so that the entire code base can be evaluated at a later point.

* Prioritize readability over concision. Python is a very flexible language that typically offers several multiple options for expressing a given piece of logic, but some may be more friendly to the reader than others. (List comprehensions are particularly vulnerable to over-optimization.) Always remain considerate of the future reader who may need to interpret your code without the benefit of the context within which you are writing it.

* Include a newline at the end of every file.

* No easter eggs. While they can be fun, NetBox must be considered as a business-critical tool. The potential, however minor, for introducing a bug caused by unnecessary code is best avoided entirely.

* Constants (variables which do not change) should be declared in `constants.py` within each app. Wildcard imports from the file are acceptable.

* Every model must have a [docstring](https://peps.python.org/pep-0257/). Every custom method should include an explanation of its function.

* Nested API serializers generate minimal representations of an object. These are stored separately from the primary serializers to avoid circular dependencies. Always import nested serializers from other apps directly. For example, from within the DCIM app you would write `from ipam.api.nested_serializers import NestedIPAddressSerializer`.

### PEP 8 Exceptions

NetBox ignores certain PEP8 assertions. These are listed below.

#### Wildcard Imports

Wildcard imports (for example, `from .constants import *`) are acceptable under any of the following conditions:

* The library being import contains only constant declarations (e.g. `constants.py`)
* The library being imported explicitly defines `__all__`

#### Maximum Line Length (E501)

NetBox does not restrict lines to a maximum length of 79 characters. We use a maximum line length of 120 characters, however this is not enforced by CI. The maximum length does not apply to HTML templates or to automatically generated code (e.g. database migrations).

#### Line Breaks Following Binary Operators (W504)

Line breaks are permitted following binary operators.

### Enforcing Code Style

The [`pycodestyle`](https://pypi.org/project/pycodestyle/) utility (formerly `pep8`) is used by the CI process to enforce code style. A [pre-commit hook](./getting-started.md#2-enable-pre-commit-hooks) which runs this automatically is included with NetBox. To invoke `pycodestyle` manually, run:

```
pycodestyle --ignore=W504,E501 netbox/
```

### Introducing New Dependencies

The introduction of a new dependency is best avoided unless it is absolutely necessary. For small features, it's generally preferable to replicate functionality within the NetBox code base rather than to introduce reliance on an external project. This reduces both the burden of tracking new releases and our exposure to outside bugs and supply chain attacks.

If there's a strong case for introducing a new dependency, it must meet the following criteria:

* Its complete source code must be published and freely accessible without registration.
* Its license must be conducive to inclusion in an open source project.
* It must be actively maintained, with no longer than one year between releases.
* It must be available via the [Python Package Index](https://pypi.org/) (PyPI).

When adding a new dependency, a short description of the package and the URL of its code repository must be added to `base_requirements.txt`. Additionally, a line specifying the package name pinned to the current stable release must be added to `requirements.txt`. This ensures that NetBox will install only the known-good release.

## Written Works

### General Guidance

* Written material must always meet a reasonable professional standard, with proper grammar, spelling, and punctuation.

* Use two line breaks between paragraphs.

* Use only a single space between sentences.

* All documentation is to be written in [Markdown](../reference/markdown.md), with modest amounts of HTML permitted where needed to overcome technical limitations.

### Branding

* When referring to NetBox in writing, use the proper form "NetBox," with the letters N and B capitalized. The lowercase form "netbox" should be used in code, filenames, etc. but never "Netbox" or any other deviation.

* There is an SVG form of the NetBox logo at [docs/netbox_logo.svg](../netbox_logo.svg). It is preferred to use this logo for all purposes as it scales to arbitrary sizes without loss of resolution. If a raster image is required, the SVG logo should be converted to a PNG image of the prescribed size.