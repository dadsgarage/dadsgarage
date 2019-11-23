# kubectl

## Developer Experience

The simplest way to use kubectl is to just mount the `$HOME/.kube` directory in the container.

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
