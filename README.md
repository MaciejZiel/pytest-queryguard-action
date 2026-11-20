# pytest-queryguard action

Run pytest with [pytest-queryguard](https://github.com/MaciejZiel/pytest-queryguard)
and see N+1 queries, repeated statements and missing indexes as GitHub code
scanning alerts, with a file, a line and a fix hint. On a pull request the
action also compares the query count of each test with the base branch and
comments the difference.

pytest-queryguard works with SQLAlchemy (Core and ORM, sync and async) and,
experimentally, with Django. The plugin already fails the test run on a new
N+1 query; the action adds the upload, the comparison and the comment.

## Usage

```yaml
name: Tests
on:
  push:
    branches: [main]
  pull_request:

permissions:
  contents: read

jobs:
  tests:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write   # needed to upload to code scanning
      actions: read            # compare query counts with the base branch
      pull-requests: write     # comment the comparison on the pull request
    steps:
      - uses: actions/checkout@v7
      - uses: actions/setup-python@v7
        with:
          python-version: "3.12"
      - run: pip install -e ".[test]"        # your project and its test dependencies
      - uses: MaciejZiel/pytest-queryguard-action@v1.1.0   # a release tag, or a commit SHA
        with:
          pytest-args: tests/   # no -x
```

Install your own project and its test dependencies first; the action installs
the plugin and runs `python -m pytest` in the active Python.

The inputs (`pytest-args`, `baseline`, `fail`, `upload`, `compare`,
`comment`, `trend`, `lint`, `working-directory` and others), the outputs and
the permissions each feature needs are described in
[CI and GitHub Action](https://maciejziel.github.io/pytest-queryguard/ci.html).

## Versions

- `@vX.Y.Z` or a commit SHA: a fixed release; the safer pin. Dependabot
  proposes the updates.
- `@v1`: the newest 1.x final release (never a release candidate); it moves
  with every 1.x release.

Without the `package` input the action installs the plugin wheel in `dist/`
of the same commit, the file published to PyPI for that release, so a pin
also fixes the plugin code that runs. pip still resolves pytest and
SQLAlchemy from the index.

The action follows the [stability policy](https://maciejziel.github.io/pytest-queryguard/stability.html)
of pytest-queryguard: its inputs, outputs and artifact name change
incompatibly only in a major release.

## Where the action is developed

This repository holds a copy of
[`action/action.yml`](https://github.com/MaciejZiel/pytest-queryguard/blob/main/action/action.yml)
of MaciejZiel/pytest-queryguard and the plugin wheel of the release; each
release of pytest-queryguard copies them here and tags the same version. The
only workflow here runs the action on `test-project/`.
Please open issues and pull requests in
[MaciejZiel/pytest-queryguard](https://github.com/MaciejZiel/pytest-queryguard/issues),
and report vulnerabilities as its
[security policy](https://github.com/MaciejZiel/pytest-queryguard/security/policy) says.

Workflows that use `MaciejZiel/pytest-queryguard/action@<tag>` keep working;
switching to this repository is a change of the `uses:` line only, and the
comparison with the base branch, the trend and open code scanning alerts carry
over.

## License

[MIT](LICENSE)
