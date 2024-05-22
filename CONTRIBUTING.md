# Where to start

We eagerly welcome contributions of any type (e.g., bug fixes, new features, reporting issues, documentation, etc).  If you're looking for a good place to get started you might like to peruse our current Git issues (those marked with <a href="https://github.com/Parsl/parsl/labels/help%20wanted" target="_blank">help wanted</a> are a good place to start).

Please be aware of <a href="https://github.com/Parsl/.github/blob/main/CODE_OF_CONDUCT.md" target="_blank">Parsl's Code of Conduct</a>. 

If you are not familiar with GitHub pull requests, the main mechanism to contribute changes to our code, there is <a href="https://opensource.com/article/19/7/create-pull-request-github" target="_blank">documentation available</a>.

If you have questions or would like to discuss ideas, please post in our Slack's <a href="https://parsl-project.slack.com/archives/C02P57G6NCB" target="_blank">#parsl-hackers channel</a>. You can <a href="https://join.slack.com/t/parsl-project/shared_invite/zt-4xbquc5t-Ur65ZeVtUOX51Ts~GRN6_g" target="_blank">join our Slack here</a>.


# Coding conventions

## Formatting conventions

Parsl code should adhere to Python <a href="https://peps.python.org/pep-0008/" target="_blank">PEP-8</a>. This is enforced in CI (with some exceptions). You can also run this test yourself using ``make flake8``.

## Naming conventions

The following convention should be followed: ClassName, ExceptionName, GLOBAL_CONSTANT_NAME, and lowercase_with_underscores for everything else.

## Version increments

Parsl follows the <a href="https://calver.org/#scheme" target="_blank">calendar versioning scheme</a> with ``YYYY.MM.DD`` numbering scheme for versions.
This scheme was chosen following a switch from semantic versioning and manual release processes to an automated weekly process.
Releases are pushed from github actions to PyPI and will be picked up automatically by Conda.
Manual packaging instructions are included in the <a href="http://parsl.readthedocs.io/en/latest/devguide/packaging.html" target="_blank">packaging docs</a>.

## Documentation

Classes should be documented following the <a href="https://github.com/numpy/numpy/blob/master/doc/HOWTO_DOCUMENT.rst.txt" target="_blank">NumPy/SciPy
style. A concise summary is available <a href="http://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_numpy.html" target="_blank">here</a>. User and developer documentation is auto-generated and made available on
<a href="https://parsl.readthedocs.io" target="_blank">ReadTheDocs</a>.

## Testing

Parsl uses ``pytest`` to run most tests. All tests should be placed in
the ``parsl/tests`` directory.

There are two broad groups of tests: those which must run with a
specific configuration, and those which should work with any
configuration.

Tests which should run with with any configuration live under
themed directories ``parsl/tests/test*/`` and should be named ``test*.py``.
They can be run with any configuration, by specifying ``--config CONFIGPATH``
where CONFIGPATH is a path to a ``.py`` file exporting a parsl configuration
object named ``config``. The parsl-specific test fixtures will ensure
a suitable DFK is loaded with that configuration for each test.

Tests which require their own specially configured DFK, or no DFK at all,
should be labelled with ``@pytest.mark.local`` and can be run with
``--config local``.
Provide the special configuration creating a ``local_config`` function
that returns the required configuration in that test file.
Or, provide both a ``local_setup`` function that loads the proper configuration
and ``local_teardown`` that stops parsl.

There is more fine-grained enabling and disabling of tests within the
above categories:

A pytest marker of ``cleannet`` (for clean network) can be used to select
or deselect tests which need a very clean network (for example, for tests
making FTP transfers). When the test environment (github actions) does not
provide a sufficiently clean network, run all tests with ``-k "not cleannet"`` to
disable those tests.

Some other markers are available but unused in testing;
see ``pytest --markers parsl/tests/`` for more details.

A specific test in a specific file can be run like this:::

  $ pytest test_python_apps/test_basic.py::test_simple

A timeout can be added to test runs using a pytest parameter such as
``--timeout=60``

Many tests are marked with ``@pytest.mark.skip`` for reasons usually
specified directly in the annotation - generally because they are broken
in one way or another.


## Coverage testing

There is also some coverage testing available. The CI by default records
coverage for most of the tests that it runs and outputs a brief report
at the end of each CI run. This is purely informational and a Lack of
coverage won't produce a CI failure.

It is possible to produce a more detailed coverage report on your
development machine: make sure you have no `.coverage` file, run the
test commands as shown in `.github/workflows/ci.yaml`, and then run
`coverage report` to produce the summary as seen in CI, or run
`coverage html` to produce annotated source code in the `htmlcov/`
subdirectory. This will show, line by line, if each line of parsl
source code was executed during the coverage test.

# Development Process

If you are a contributor to Parsl at large, we recommend forking the repository and submitting pull requests from your fork.
The <a href="https://github.com/orgs/Parsl/teams" target="_blank">Parsl development team</a> has the additional privilege of creating development branches on the repository.
Parsl development follows a common pull request-based workflow similar to <a href="http://scottchacon.com/2011/08/31/github-flow.html" target="_blank">GitHub flow</a>. That is:

* every development activity (except very minor changes, which can be discussed in the PR) should have a related GitHub issue
* all development occurs in branches (named with a short descriptive name which includes the associated issue number, for example, `add-globus-transfer-#1`)
* the master branch is always stable
* development branches should include tests for added features
* development branches should be tested after being brought up-to-date with the master (in this way, what is being tested is what is actually going into the code; otherwise unexpected issues from merging may come up)
* branches what have been successfully tested are merged via pull requests (PRs)
* PRs should be used for review and discussion
* PRs should be reviewed in a timely manner, to reduce effort keeping them synced with other changes happening on the master branch

Git commit messages should include a single summary sentence followed by a more explanatory paragraph. Note: all commit messages should reference the GitHub issue to which they relate. A nice discussion on the topic can be found <a href="https://chris.beams.io/posts/git-commit/" target="_blank">here</a>.

Implemented Globus data staging support

    Added the ability to reference and automatically transfer Globus-accessible
    files. References are represented using the Parsl file format
    “globus://endpoint/path/file.” If Globus endpoints are known for source and
    destination Parsl will use the Globus transfer service to move data to the
    compute host. Fixes #-1.

# Git hooks

Developers may find it useful to setup a pre-commit git hook to automatically lint and run tests. This is a script which is run before each commit. For example::

    $ cat ~/parsl/.git/hooks/pre-commit
    #!/bin/sh

    make lint flake8 mypy local_thread_test

# Project documentation

All project documentation is written in reStructuredText. <a href="http://sphinx-doc.org/" target="_blank">Sphinx</a> is used to generate the HTML documentation from the rst documentation and structured docstrings in Parsl code.  Project documentation is built automatically and added to the <a href="https://parsl.readthedocs.io" target="_blank">Parsl documentation.

# Credit and Contributions

Parsl wants to make sure that all contributors get credit for their contributions.  When you make your first contribution, it should include updating the codemeta.json file to include yourself as a contributor to the project.

# Discussion and Support

The best way to discuss development activities is via Git issues.

To get involved in community discussion please <a href="https://join.slack.com/t/parsl-project/shared_invite/zt-4xbquc5t-Ur65ZeVtUOX51Ts~GRN6_g" target="_blank">join the Parsl Slack channel</a>.
