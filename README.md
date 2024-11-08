# Dev container templates for R projects

## Common features

* Ubuntu 22.04 base image.
* R 4.4.x via [rig](https://github.com/r-lib/rig#readme).
* Package dependencies are specified in a `DESCRIPTION` file, as
  `Dependes:` or `Imports:`.
* Package dependencies for tests are specified in `DESCRIPTION`, under
  `Suggests:`.
* The `test` stage of `Dockerfile` runs the tests.
* The `prod` stage of `Dockerfile` runs the tests cases via
 `testthat::test_lcal()`, but does not include them in the image.

## Example project

See the https://github.com/r-hub/cran-metadata project that uses these
dev container templates.

## Templates

* [`gh-script`](src/gh-script) An R script to run with a scheduler on
  GitHub Actions.

## VS Code installation

1. From the command palette, select "Dev Containers: Add Dev Container
   Configuration Files...".
1. Select "Add configuration to workspace" or "Add configuration to
   user data folder".
1. Then use `ghcr.io/r-lib/devcontainer-templates/gh-script:latest` as
   the template to apply.
1. Select additional features. None are required currently.
1. When including optional dependencies you may want to include some GitHub
   actions workflows to deploy the script to GitHub Actions.
1. Create a `DESCRIPTION `file with package name and version. E.g.
   ```
   Package: mypackage
   Version: 1.0.0
   ```
1. You now select "Rebuild and Reopen in Container" to start the project in
   in the dev container.

## Command line installation

1. Install the [devcontainer cli](
    https://github.com/devcontainers/cli#readme). On macOS you can use
   `brew install devcontainer`
1. Before installing the template, I recommend to commit your changes,
   because the template might overwrite some of your files.
1. Install the template:
   ```
   devcontainer templates apply -w . -t \
     ghcr.io/r-lib/devcontainer-templates/gh-script:latest
   ```
1. Create a `DESCRIPTION `file with package name and version. E.g.
   ```
   Package: mypackage
   Version: 1.0.0
   ```
1. Build the template:
   ```
   devcontainer build --workspace-folder .
   ```
1. Start the container:
   ```
   devcontainer up --workspace-folder .
   ```

## Usage

Use the template as usual in tools that natively support dev containers,
e.g. VS Code, [GitHub Codespaces](https://github.com/features/codespaces),
[VSCodium](https://vscodium.com/) + [Devpod](https://devpod.sh/), etc.

### Positron

To use R projects with these templates from Poisitron, for now there is a
bit of extra legwork:

1. Use a random port for SSH (optional).
   ```
   echo PORT=2222 >> .env
   ```
1. Start the container:
   ```
   devcontainer up --workspace-folder .
   ```
1. Connect from Positron. From the command palette choose
   'Remote-SSH: Connect to Host...' or 'Remote-SSH: Connect Current Window
   to Host...'. The connection string is
   ```
   root@localhost:2222
   ```
   (Use the port you set in `.env`. The default is 2222 if you don't have
   `.env`.)
1. Open the `/workspaces/cran-metadata` folder.


* Reconnecting from Positron: to reconnect to a running dev container, the
  easiest is to select the remote folder from the list of recently opened
  project on the top right. If it is not there, use the last two steps of
  the previous section.
* Stopping: Positron does not stop the container when it disconnects. The
  devcontainer cli also cannot stop it currently. To stop the container
  call docker directly:
  ```
  docker stop cran-metadata-r-app-1
  ```
* Restarting: to restart a stopped container call
  ```
  devcontainer up --workspace-folder .
  ```
* Rebuilding: to rebuild a container stop it and delete it before you
  build it again:
  ```
  docker stop cran-metadata-r-app-1
  docker rm cran-metadata-r-app-1
  devcontainer up --workspace-folder .
  ```

# License

MIT (c) Posit, PBC
