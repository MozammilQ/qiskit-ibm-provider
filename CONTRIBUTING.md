Contributing
============

First read the overall project contributing guidelines. These are all
included in the qiskit documentation:

https://qiskit.org/documentation/contributing_to_qiskit.html


Contributing to qiskit-ibm-provider
---------------------------

In addition to the general guidelines there are specific details for
contributing to the qiskit-ibm-provider, these are documented below.


### Initial Steps

1. Fork your own copy of qiskit-ibm-provider repository, then clone it locally.
2. Setup a virtual environment based on the requirements.txt and requirements-dev.txt 
   files.
3. Create a new branch and then make your contributions on it.


### Pushing a new branch

Before pushing your contribution please ensure that:

1. The code follows the code style of this repository. For convenience, you can 
   check [Style guide](#style-guide)
2. If it makes sense, add tests that cover the changes. 
3. Make sure all tests pass. For convenience, you can verify the [Test Types](#test-types).
4. The documentation has been updated accordingly. In particular, if a
   function or class has been modified during your contribution, please update
   the *docstring* accordingly. For convenience, you can check [Building the 
   Documentation Locally](#building-documentation-locally).

### Pull request creation

When submitting a pull request and you feel it is ready for review,
if your change has an end user facing impact (new feature, deprecation, removal 
etc) please ensure that you add a reno release note and that the PR is tagged 
for the changelog.

### Changelog generation

The changelog is automatically generated as part of the release process
automation. This works through a combination of the git log and the pull
request. When a release is tagged and pushed to github the release automation
bot looks at all commit messages from the git log for the release. It takes the
PR numbers from the git log (assuming a squash merge) and checks if that PR had
a `Changelog:` label on it. If there is a label it will add the git commit
message summary line from the git log for the release to the changelog.

If there are multiple `Changelog:` tags on a PR the git commit message summary
line from the git log will be used for each changelog category tagged.

The current categories for each label are as follows:

| PR Label               | Changelog Category |
| -----------------------|--------------------|
| Changelog: Deprecation | Deprecated         |
| Changelog: New Feature | Added              |
| Changelog: API Change  | Changed            |
| Changelog: Removal     | Removed            |
| Changelog: Bugfix      | Fixed              |

### Release Notes

When making any end user facing changes in a contribution we have to make sure
we document that when we release a new version of qiskit-ibm-provider. The
expectation is that if your code contribution has user facing changes that you
will write the release documentation for these changes. This documentation must
explain what was changed, why it was changed, and how users can either use or
adapt to the change. The idea behind release documentation is that when a naive
user with limited internal knowledege of the project is upgrading from the
previous release to the new one, they should be able to read the release notes,
understand if they need to update their program which uses qiskit, and how they
would go about doing that. It ideally should explain why they need to make
this change too, to provide the necessary context.

To make sure we don't forget a release note or if the details of user facing
changes over a release cycle we require that all user facing changes include
documentation at the same time as the code. To accomplish this we use the
[reno](https://docs.openstack.org/reno/latest/) tool which enables a git based
workflow for writing and compiling release notes.

#### Adding a new release note

Making a new release note is quite straightforward. Ensure that you have reno
installed with::

    pip install -U reno

Once you have reno installed you can make a new release note by running in
your local repository checkout's root::

    reno new short-description-string

where short-description-string is a brief string (with no spaces) that describes
what's in the release note. This will become the prefix for the release note
file. Once that is run it will create a new yaml file in releasenotes/notes.
Then open that yaml file in a text editor and write the release note. The basic
structure of a release note is restructured text in yaml lists under category
keys. You add individual items under each category and they will be grouped
automatically by release when the release notes are compiled. A single file
can have as many entries in it as needed, but to avoid potential conflicts
you'll want to create a new file for each pull request that has user facing
changes. When you open the newly created file it will be a full template of
the different categories with a description of a category as a single entry
in each category. You'll want to delete all the sections you aren't using and
update the contents for those you are. For example, the end result should
look something like::

```yaml
features:
  - |
    Introduced a new feature foo, that adds support for doing something to
    ``QuantumCircuit`` objects. It can be used by using the foo function,
    for example::

      from qiskit import foo
      from qiskit import QuantumCircuit
      foo(QuantumCircuit())

  - |
    The ``qiskit.QuantumCircuit`` module has a new method ``foo()``. This is
    the equivalent of calling the ``qiskit.foo()`` to do something to your
    QuantumCircuit. This is the equivalent of running ``qiskit.foo()`` on
    your circuit, but provides the convenience of running it natively on
    an object. For example::

      from qiskit import QuantumCircuit

      circ = QuantumCircuit()
      circ.foo()

deprecations:
  - |
    The ``qiskit.bar`` module has been deprecated and will be removed in a
    future release. Its sole function, ``foobar()`` has been superseded by the
    ``qiskit.foo()`` function which provides similar functionality but with
    more accurate results and better performance. You should update your calls
    ``qiskit.bar.foobar()`` calls to ``qiskit.foo()``.
```

You can also look at other release notes for other examples.

You can use any restructured text feature in them (code sections, tables,
enumerated lists, bulleted list, etc) to express what is being changed as
needed. In general you want the release notes to include as much detail as
needed so that users will understand what has changed, why it changed, and how
they'll have to update their code.

After you've finished writing your release notes you'll want to add the note
file to your commit with `git add` and commit them to your PR branch to make
sure they're included with the code in your PR.

#### Linking to issues

If you need to link to an issue or other github artifact as part of the release
note this should be done using an inline link with the text being the issue
number. For example you would write a release note with a link to issue 12345
as:

```yaml
fixes:
  - |
    Fixes a race condition in the function ``foo()``. Refer to
    `#12345 <https://github.com/Qiskit/qiskit-ibm-provider/issues/12345>`_ for
    more details.
```

#### Generating the release notes

After release notes have been added if you want to see what the full output of
the release notes. In general the output from reno that we'll get is a rst
(ReStructuredText) file that can be compiled by
[sphinx](https://www.sphinx-doc.org/en/master/). To generate the rst file you
use the ``reno report`` command. If you want to generate the full Qiskit IBM provider
release notes for all releases (since we started using reno during 0.9) you just
run::

    reno report

but you can also use the ``--version`` argument to view a single release (after
it has been tagged::

    reno report --version 0.9.0

At release time ``reno report`` is used to generate the release notes for the
release and the output will be submitted as a pull request to the documentation
repository's [release notes file](
https://github.com/Qiskit/qiskit/blob/master/docs/release_notes.rst)

### Building documentation and release notes locally

Building The release notes are part of the standard qiskit-ibm-provider
documentation builds. To check what the rendered html output of the release
notes will look like for the current state of the repo you can run:
`tox -edocs` which will build all the documentation into `docs/_build/html`
and the release notes in particular will be located at
`docs/_build/html/release_notes.html`

## Installing Qiskit IBM Provider from source
Please see the [Getting started - Installation](https://qiskit.org/documentation/getting_started.html#installation)
section of the Qiskit documentation.
Select the _Install from source_ tab and scroll down to _Installing IBM Quantum Provider from Source_.


### Test

#### Test Types
There are three different types of tests in `qiskit-ibm-provider`. The 
implementation is based upon the well-documented [unittest](https://docs.python.org/3/library/unittest.html) Unit testing framework.

##### 1. Unit tests
Run locally without connecting to an external system. They are short-running,
stable and give a basic level of confidence during development.

To execute all unit tests, run:
``` {.bash}
$ make unit-test
```
##### 2. Integration tests
Executed against an external system configured via a (token, instance, url) 
tuple. Detailed coverage of happy and non-happy paths. They are long-running and
unstable at times. A successful test run gives a high level of confidence that 
client and APIs work well together.

To execute all integration tests, run
``` {.bash}
$ make integration-test-1 integration-test-2 integration-test-3
```

##### 3. E2E tests

Executed against an external system configured via a (token, instance, url) 
tuple. Basic coverage of most important user-facing happy paths. Test suite runs
faster than integration but slower than unit tests and is stable.

To execute all e2e tests, run
``` {.bash}
$ make e2e-test
```

###### Configuration

Integration and E2E tests require an environment configuration and will be run 
against the IBM Quantum API ("ibm_quantum").


Sample configuration for IBM Quantum
```bash
QISKIT_IBM_TOKEN=...                                            # IBM Quantum API token
QISKIT_IBM_URL=https://auth.quantum-computing.ibm.com/api       # IBM Quantum API URL
QISKIT_IBM_INSTANCE=ibm-q/open/main                             # IBM Quantum provider to use (hub/group/project)
```

To enable test cases against external system in your private fork's CI workflow, make sure to set above values as
[encrypted environment secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-environment).
The names of the environments must match the ones that the [CI workflow](.github/workflows/ci.yml) relies
upon.

To run integration and E2E tests locally, set environment variables in your terminal.
In Linux or MacOS, this is accomplished using the `export` command.
In Windows, the method will depend on which console you are using.

### Style guide

Please submit clean code and please make effort to follow existing
conventions in order to keep it as readable as possible. We use:
* [Pylint](https://www.pylint.org) linter
* [PEP 8](https://www.python.org/dev/peps/pep-0008) style
* [mypy](http://mypy-lang.org/) type hinting

To ensure your changes respect the style guidelines, you can run the following
commands:

All platforms:

```bash
make lint
make style
make mypy
```

### Development Cycle

The development cycle for qiskit-ibm-provider is all handled in the open using
the project boards in Github for project management. We use milestones
in Github to track work for specific releases. The features or other changes
that we want to include in a release will be tagged and discussed in Github.
As we're preparing a new release we'll document what has changed since the
previous version in the release notes.

### Branches

* `main`:

The main branch is used for development of the next version of qiskit-ibm-provider.
It will be updated frequently and should not be considered stable. The API
can and will change on main as we introduce and refine new features.

* `stable/*` branches:
Branches under `stable/*` are used to maintain released versions of qiskit-ibm-provider.
It contains the version of the code corresponding to the latest release for
that minor version on pypi. For example, stable/0.8 contains the code for the
0.8.2 release on pypi. The API on these branches are stable and the only changes
merged to it are bugfixes.

### Release cycle

When it is time to release a new minor version of qiskit-ibm-provider we will:

1.  Create a new tag with the version number and push it to github
2.  Change the `main` version to the next release version.

The release automation processes will be triggered by the new tag and perform
the following steps:

1.  Create a stable branch for the new minor version from the release tag
    on the `main` branch
2.  Build and upload binary wheels to pypi
3.  Create a github release page with a generated changelog
4.  Generate a PR on the meta-repository to bump the Qiskit IBM Provider version and
    meta-package version.

The `stable/*` branches should only receive changes in the form of bug
fixes.
