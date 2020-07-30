# Image Lint

To comply with [Docker best practices][dbp], we are using the [Hadolint][hadolint] tool to analyse each `Dockerfile` .

## Installation

There is a specific `make` target to install the linter.
By default `hadolint` will be installed in `${HOME}/hadolint`.

```bash
$ make lint-install

# Installing hadolint at /Users/romain/hadolint ...
# Installation done!
# Haskell Dockerfile Linter v1.17.6-0-gc918759
```

## Lint

### Per Stack

The linter can be run per stack.

```bash
$ make lint/scipy-notebook  

# Linting Dockerfiles in scipy-notebook...
# scipy-notebook/Dockerfile:4 DL3006 Always tag the version of an image explicitly
# scipy-notebook/Dockerfile:11 DL3008 Pin versions in apt get install. Instead of `apt-get install <package>` use `apt-get install <package>=<version>`
# scipy-notebook/Dockerfile:18 SC2086 Double quote to prevent globbing and word splitting.
# scipy-notebook/Dockerfile:68 SC2086 Double quote to prevent globbing and word splitting.
# scipy-notebook/Dockerfile:68 DL3003 Use WORKDIR to switch to a directory
# scipy-notebook/Dockerfile:79 SC2086 Double quote to prevent globbing and word splitting.
# make: *** [lint/scipy-notebook] Error 1
```

Optionally you can pass arguments to the linter.

```bash
# Use a different export format
$ make lint/scipy-notebook ARGS="--format codeclimate"  
```

### All the Stacks

The linter can be run against all the stacks.

```bash
$ make lint-all
```

## Ignoring Rules

Sometimes it is necessary to ignore [some rules][rules]. 
The following rules are ignored by default and sor for all images in the `.hadolint.yaml` file.

- [`DL3006`][DL3006]: We use a specific policy to manage image tags. 
  - `base-notebook` `FROM` clause is fixed but based on an argument (`ARG`). 
  - Building downstream images from (`FROM`) the latest is done on purpose.
- [`DL3008`][DL3008]: System packages are always updated (`apt-get`) to the latest version.

For other rules, the preferred way to do it is to flag ignored rules in the `Dockerfile`.

> It is also possible to ignore rules by using a special comment directly above the Dockerfile instruction you want to make an exception for. Ignore rule comments look like `# hadolint ignore=DL3001,SC1081`. For example:

```dockerfile

FROM ubuntu

# hadolint ignore=DL3003,SC1035
RUN cd /tmp && echo "hello!"
```

[hadolint]: https://github.com/hadolint/hadolint
[dbp]: https://docs.docker.com/develop/develop-images/dockerfile_best-practices
[rules]: https://github.com/hadolint/hadolint#rules
[DL3006]: https://github.com/hadolint/hadolint/wiki/DL3006
[DL3008]: https://github.com/hadolint/hadolint/wiki/DL3008