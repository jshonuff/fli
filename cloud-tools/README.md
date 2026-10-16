# Shared cloud tools

This setup provides Git, uv, Node.js 24 LTS with npm, build-essential
(GCC, G++, and Make), and FFmpeg. Python 3.11 is included for FLI.

## GitHub Codespaces

Create a Codespace from this repository. GitHub builds the .devcontainer
image, installs the tools, then installs FLI and its development dependencies. Existing Codespaces need "Rebuild Container".

## Docker on another machine

From this repository:
```sh
docker build -f .devcontainer/Dockerfile -t basic-cloud-tools .
docker run --rm -it -v "$PWD:/workspaces/project" -w /workspaces/project basic-cloud-tools bash
```

Inside the container, install the cloned project's own dependencies.
For FLI, run `python -m pip install .`. Other projects may use
`uv sync` or `npm ci`, according to their documentation.

## GitHub Actions in another repository

Use this reusable job to verify that the base environment builds:
```yaml
jobs:
  verify-tools:
    uses: jshonuff/fli/.github/workflows/basic-cloud-tools.yml@main
```
That job verifies the environment; tools do not carry over into other jobs.
To run your own project with the tools, copy the Dockerfile to your repository,
build it, and run your commands inside the resulting container in the same job.
Pin the reusable workflow to a reviewed commit SHA for stable builds.

## Persistence and updates

GitHub stores these setup files, not an account-wide software installation.
Actions runners are temporary; each run builds or restores its environment.
Codespaces retain their filesystem until deleted. Other repositories must
opt into this setup; cloning a repository alone does not install its dependencies.

Upstream sources: https://github.com/git/git,
https://github.com/astral-sh/uv, https://github.com/nodejs/node,
https://packages.debian.org/bookworm/build-essential,
https://github.com/FFmpeg/FFmpeg.
No upstream fork is required to install these tools.
