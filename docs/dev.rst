Developer guide
===============

AI usage policy
---------------

Pintext has an explicit
`AI usage policy <https://github.com/leroyvn/pintext/blob/main/AI_POLICY.md>`__.
Please read it before opening an issue or a pull request. Agent-specific
guidance for working in this repository lives in
`AGENTS.md <https://github.com/leroyvn/pintext/blob/main/AGENTS.md>`__ .

Contributing to the code base
-----------------------------

Requirements
^^^^^^^^^^^^

Developing requires:

* the `uv <https://docs.astral.sh/uv/>`__ package manager;
* the `pre-commit <https://pre-commit.com/>`__ git hook manager (or its drop-in
  replacement `prek <https://prek.j178.dev/>`__);
* the `poethepoet <https://poethepoet.natn.io/>`__ task runner (optional).

Setting up a development environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone the repository:

.. code:: bash

    git clone git@github.com:leroyvn/pintext.git

Enter the created repository and set up the development environment:

.. code:: bash

    cd pintext
    uv sync --all-groups --all-extras

Finally, activate the pre-commit hooks:

.. code:: bash

    pre-commit install  # or prek install


Package layout
^^^^^^^^^^^^^^

Pintext is built in three layers:

* ``src/pintext/``: the core, which must depend on Pint alone;
* ``src/pintext/attrs/``: *attrs* integration, behind the ``attrs`` extra;
* ``src/pintext/pydantic/``: *pydantic* integration, behind the ``pydantic``
  extra.

.. important::

    Nothing in the core may import ``attrs`` or ``pydantic``. A dedicated CI job
    installs the package without extras and runs `tests/core`, so a stray import
    breaks the build.

Running tests
^^^^^^^^^^^^^

The testing process of Pintext uses [pytest](https://docs.pytest.org). A task is
defined to help you run the tests:

.. code:: bash

    poe test  # or uv run poe test if poe is not available globally on your system

This runs unit tests, the doctests embedded in every docstring under `src/`,
and the doctests in the documentation. Tests are split by layer:

.. code:: bash

    uv run pytest tests/core       # no optional dependency required
    uv run pytest tests/attrs      # skipped without attrs
    uv run pytest tests/pydantic   # skipped without pydantic

Linting and type checking
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: bash

    uv run ruff check
    uv run ruff format --check
    uv run ty chec

Repository conventions are checked with
`repo-review
<https://learn.scientific-python.org/development/guides/repo-review/>`__:

.. code:: bash

    uvx --from 'sp-repo-review[cli]' repo-review

Deliberate exemptions are recorded in ``[tool.repo-review] ignore`` in
``pyproject.toml``. Passing ``--ignore`` on the command line overrides that list
rather than adding to it, so run the command bare.

Building the documentation
--------------------------

To build the documentation, use the dedicated task:

.. code:: bash

    poe docs  # or uv run poe docs

Incremental autobuild is also supported:

.. code:: bash

    poe docs-serve  # or uv run poe docs-serve

Maintainers: release process
----------------------------

Pintext follows `Semantic Versioning <https://semver.org/>`__.

**Pre-release steps**

1. Make sure that all tests pass.
2. Make sure the change log is up-to-date. Add the release date to the relevant
   section header.
3. Set the package version number to the target value:

   .. code:: bash

       uv version <MAJOR.MINOR.PATCH>

4. Create and push a commit with the following message:

   .. code:: text

       pintext version <MAJOR.MINOR.PATCH>

**Release steps**

1. Create a
   `new release on GitHub <https://github.com/leroyvn/pintext/releases>`__.
2. When asked for a tag, create a new one (``v<MAJOR.MINOR.PATCH>``).
3. The automated workflow will build the package and upload it to PyPI using
   `Trusted Publishing <https://docs.pypi.org/trusted-publishers/>`__. No API
   token is involved; the ``pypi`` environment must be configured as a trusted
   publisher for the project.

**Post-release steps**

1. Create a new section in the change log (``CHANGELOG.md``) with the title
   *Pintext <MAJOR.MINOR.PATCH> (upcoming release)*.
2. Bump the version to the next development one:

   .. code:: bash

       uv version <MAJOR.MINOR.PATCH>.dev

3. Create and push a commit with the following message:

   .. code:: text

       Version <MAJOR.MINOR.PATCH> ready for development

Roadmap
-------

**Not planned yet**

* Nothing currently.
