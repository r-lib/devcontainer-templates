
# Schedule an R script on GitHub Actions (gh-script)

## Features

* Ubuntu 22.04 base image.
* R 4.4.x via [rig](https://github.com/r-lib/rig#readme).
* Package dependencies are specified in a `DESCRIPTION` file, as
  `Dependes:` or `Imports:`.
* Package dependencies for tests are specified in `DESCRIPTION`, under
  `Suggests:`.
* The `test` stage of `Dockerfile` runs the tests.
* The `prod` stage of `Dockerfile` runs the tests cases via
 `testthat::test_local()`.

## GitHub Action workflows:

### `test.yaml`

Runs the test cases via `testthat::test_local()`.

### `deploy.yaml`

Runs the tests, creates the production container and pushes it to the
GitHub Container Registry.

### `run.yaml`

Runs the deployed container. Make sure you update the default schedule.
