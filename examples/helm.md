# helm

## helm plugins

- [helm-diff](https://github.com/databus23/helm-diff)

## Other

- [helmfile](https://github.com/roboll/helmfile)

## Developer Experience

### Setup

The simplest way to use helm is to just mount the `$HOME/.kube` directory in the container.

Start the docker image:

```sh
docker run -it --rm \
  --mount type=bind,source=$HOME/.cache,target=/home/dadsgarage/.cache \
  --mount type=bind,source=$HOME/.ssh,target=/home/dadsgarage/.ssh \
  --mount type=bind,source="$(pwd)",target=/home/dadsgarage/workdir \
  --mount type=bind,source=$HOME/.kube,target=/home/dadsgarage/.kube \
  --workdir /home/dadsgarage/workdir \
  dadsgarage/dadsgarage:latest \
  bash
```

### Helm 2

The helm 2 executable is called `helm2`.

To invoke helmfile with helm 2 we have a handy alias called `helmfile2` which under the hood is just `helmfile --helm-binary helm2`.

Inside the Docker container you can use:

```sh
helm2 list
helm2 repo add stable http://storage.googleapis.com/kubernetes-charts-stable
```

### helm 3

Inside the Docker container you can use:

```sh
helm list -a --all-namespaces
helm repo add stable http://storage.googleapis.com/kubernetes-charts-stable
```
