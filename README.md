# Dad's Garage

Dad's Garage is a container that has all of the build tools you need to build, test, and deploy a cloud-native application to Kubernetes. The idea is to

1. Be able to develop your application on a computer that has nothing but an IDE and a container runtime (Docker, Podman, etc) by using Dad's Garage as the "developer toolbox".
2. Use Dad's Garage in your CI/CD pipeline to build, test, secure, deliver, and deploy your application to Kubernetes.

It's the containerized version of your dad's garage. It's full of tools, you spend lots of time in it, and you use it to build great things.

Dad's Garage is very new, and many of the tools that you will need to use aren't there yet. Stay tuned, or contribute by submitting a pull request!

## What Dad's Garage is (or will be)

- A container full of tools that follows best practices for security and usability
- A Jenkins JNLP Agent
- A Selenium Server
- Maybe more???

## What Dad's Garage is not (and never will be)

- A small container. As tools are added it is likely the container will grow to several gigabytes. This is expected and should not be seen as an issue, since this container will act as the main toolkit for develoeprs to use to develop with.
- A container that is used to run applications in production. Don't do this! Production containers should be tiny, only installing what is absolutely necessary to run.
- A windows container. You won't be able to build .NET Framework apps with this. (.NET Core should work great though!)

## Usage

Our goal is to make Dad's Garage as simple as possible to use. A user should be able to pick up DG and use it with no required configuration.

### Developer Experience

The most basic way to use DG, is to just use `docker run`

```sh
docker run -it --rm dadsgarage/dadsgarage:latest bash
```

But that's not very useful, since it doesn't have your source code, or any of your other files on your system that are necessary to get your work done. Here's a more complete example:

```sh
docker run -it --rm \
  --mount type=bind,source=$HOME/.cache,target=/home/dadsgarage/.cache \
  --mount type=bind,source=$HOME/.ssh,target=/home/dadsgarage/.ssh \
  --mount type=bind,source="$(pwd)",target=/home/dadsgarage/workdir \
  --workdir /home/dadsgarage/workdir \
  dadsgarage/dadsgarage:latest \
  bash
```

Now we're talkin'! We've got a bash shell inside the container, with the current working directory, as well as $HOME/.ssh for connecting with the git repo using your SSH key, and $HOME/.cache for caching pre-commit's environments so you don't have to build them every time. This is a command that you can alias or create a shell script for in your path so you can run it easily.

For more examples of how to use tools installed in the container, see the [examples](examples) folder.

### Continuous Integration

Work in progress, check back later.

### Tool Versions

Most installed tools use the Dockerfile's `ARG` command for the tool version, so you are free to build the tool yourself using the `--build-arg` flag. Example:

```sh
docker build src/docker -t dadscustomgarage:latest --build-arg PRE_COMMIT_VERSION=1.19.0
```

This will build the container with pre-commit v1.19.0 instead of whatever is specified in the dockerfile. You can use as many `--build-arg` parameters in a `docker build` command that you want. Inspect the [Dockerfile](src/docker/Dockerfile) for the list of available `ARG`s and the syntax for each one.

> The list of ARGs are scattered throughout the Dockerfile in order to preserve caching. If all of the ARGS were collected at the beginning, each change to the version of any tool would bust the whole cache. Eventually we will extract all default versions into a different file and bring them in some other way, but we don't want to introduce a dependency on a build tool like `make` yet. We're definitely heading in that direction though.

## Installed Software

The following is a mostly complete list of the software installed in Dad's Garage. Whatever is already installed in the base image (which is [bitnami/minideb:buster](https://hub.docker.com/r/bitnami/minideb/)) is not listed.

### Debian Packages

- [build-essential](https://packages.debian.org/buster/build-essential)
- [ca-certificates](https://packages.debian.org/buster/ca-certificates)
- [curl](https://packages.debian.org/buster/curl)
- [dirmngr](https://packages.debian.org/buster/dirmngr)
- [git](https://packages.debian.org/buster/git)
- [gnupg](https://packages.debian.org/buster/gnupg)
- [gosu](https://packages.debian.org/buster/gosu)
- [jq](https://packages.debian.org/buster/jq) ([docs](https://stedolan.github.io/jq))
- [libaio1](https://packages.debian.org/buster/gosu)
- [locales](https://packages.debian.org/buster/locales)
- [openssh-client](https://packages.debian.org/buster/openssh-client)
- [pkg-config](https://packages.debian.org/buster/pkg-config)
- [procps](https://packages.debian.org/buster/procps)
- [python3](https://packages.debian.org/buster/python3)
- [python3-pip](https://packages.debian.org/buster/python3-pip)
- [python3-setuptools](https://packages.debian.org/buster/python3-setuptools)
- [python3-venv](https://packages.debian.org/buster/python3-venv)
- [unzip](https://packages.debian.org/buster/unzip)

### Other

- [aws-cli](https://aws.amazon.com/cli/)
- [azure-cli](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli)
- [k9s](https://github.com/derailed/k9s)
- [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)
- [pipx](https://github.com/pipxproject/pipx)
- [helm 2](https://v2.helm.sh/docs/)
- [helm 3](https://helm.sh/docs/)
- [nvm](https://github.com/nvm-sh/nvm)
- [nodejs](https://nodejs.org) (using NVM)
- [npm](https://www.npmjs.com/) (using NVM)
- [pre-commit](https://pre-commit.com/)
- [terraform](https://www.terraform.io/docs/commands/index.html)
- [tini](https://github.com/krallin/tini)

## Contributing

Pull requests are welcome! If you are going to contribute to this project you should

- Have [pre-commit](https://pre-commit.com/) installed
- Install the pre-commit hooks by running `pre-commit install` in this directory
- Run `pre-commit run -a` if the CI build isn't passing. The CI pipeline runs `pre-commit run -a` and fails if any of the checks fail.

### Dad's Garage CI/CD

Pull Requests are not automatically run through the Codefresh pipeline. A maintainer must put `/test` in a comment on the PR for the pipeline run to trigger.

Public build logs for Pull Request pipelines can be found [here](https://g.codefresh.io/public/accounts/rothandrew/pipelines/5dd4666e751f051a7ff8666e).

The repo that contains the codefresh YAML pipeline for this project is [here](https://github.com/dadsgarage/cf-pipelines).
